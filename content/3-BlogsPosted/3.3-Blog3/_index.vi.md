---
title: "Blog 3"
date: 2026-11-14
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Amazon Rekognition kết hợp Amazon Athena – Tìm hiểu cách phân tích ảnh bằng AI và truy vấn kết quả bằng SQL

*Đăng ngày 14/11/2026 trên nhóm AWS Study Group – [Xem bài viết gốc](https://www.facebook.com/photo/?fbid=4678263929124258&set=gm.2337636550334637&idorvanity=660548818043427)*

Một thư viện ảnh có hàng trăm hay hàng nghìn tệp sẽ rất khó tìm kiếm nếu không có thông tin mô tả nội dung từng ảnh. Cách thủ công là tự gắn nhãn, nhưng việc đó tốn thời gian và khó duy trì khi dữ liệu tăng lên.

Khi tìm hiểu các dịch vụ AI của AWS, mình quan tâm đến việc dùng một dịch vụ có sẵn để tự nhận biết nội dung ảnh, rồi biến kết quả thành dữ liệu có thể thống kê. Hai dịch vụ mình tìm hiểu là Amazon Rekognition và Amazon Athena. Rekognition phân tích ảnh bằng deep learning và trả về các nhãn mô tả nội dung, còn Athena truy vấn và thống kê các nhãn đó bằng SQL.

![Ảnh trên S3 được Amazon Rekognition gắn nhãn qua AWS CLI trong CloudShell, kết quả JSON Lines lưu lại S3 và được Amazon Athena truy vấn bằng SQL với metadata trong AWS Glue Data Catalog](/images/3-BlogsPosted/3.3-Blog3/rekognition-athena-architecture.png)

### Amazon Rekognition – Phân tích ảnh bằng deep learning

Theo tài liệu của AWS, Amazon Rekognition là dịch vụ phân tích ảnh và video dựa trên deep learning, không đòi hỏi kiến thức về machine learning để sử dụng. Dịch vụ có API đơn giản để phân tích ảnh hoặc video lưu trong Amazon S3, với các khả năng như phát hiện đối tượng, văn bản, nội dung không an toàn và khuôn mặt.

Trong bài này mình dùng `DetectLabels`, API phát hiện đối tượng, sự kiện và khái niệm trong ảnh JPEG hoặc PNG. Mỗi nhãn trả về gồm tên nhãn (`Name`), độ tin cậy từ 0 đến 100 (`Confidence`), các nhãn cha trong cây phân loại (`Parents`) và vị trí khung bao của vật thể (`Instances`). Hai tham số mình dùng là `MaxLabels` để giới hạn số nhãn và `MinConfidence` để loại các nhãn có độ tin cậy thấp (mặc định là 55).

Về chi phí, theo trang giá của Rekognition, `DetectLabels` có giá 0,001 USD cho mỗi ảnh ở bậc đầu tiên (1,00 USD cho 1.000 ảnh). Free tier cho 1.000 ảnh mỗi tháng trong 12 tháng đầu, và chạy nhiều API trên cùng một ảnh thì được tính là nhiều ảnh.

### Amazon Athena – Truy vấn và thống kê kết quả

Kết quả của Rekognition là JSON cho từng ảnh. Để thống kê trên nhiều ảnh, mình gộp kết quả thành các dòng JSON trong S3 và để Athena truy vấn trực tiếp bằng SQL, giống cách mình đã làm trong bài về [Athena và Glue Data Catalog](../3.2-blog2/). Theo trang giá của Athena, truy vấn tính 5 USD cho mỗi TB dữ liệu quét.

### Khi kết hợp Rekognition và Athena, quy trình hoạt động như thế nào?

Mình thử một kịch bản với 30 ảnh lấy từ bộ dữ liệu Imagenette (bản 160 px, giấy phép Apache-2.0), gồm 3 ảnh cho mỗi loại trong 10 loại như nhà thờ, xe chở rác, trụ bơm xăng, dù, bóng golf. Mình chọn bộ này vì biết trước nội dung ảnh nên dễ kiểm tra nhãn đúng hay sai.

- Mình tải 30 ảnh lên S3 rồi dùng AWS CLI gọi `DetectLabels` cho từng ảnh với `MaxLabels` là 10 và `MinConfidence` là 80.
- Kết quả được chuyển thành các dòng JSON (ảnh, nhãn, độ tin cậy, nhãn cha), gộp thành một tệp và tải lên S3.
- Mình tạo một bảng ngoài (external table) trong Athena trỏ vào thư mục chứa tệp đó, rồi chạy các câu truy vấn thống kê bằng SQL.

Kết quả mình ghi lại:

- **Số nhãn:** 166 cặp ảnh và nhãn từ 30 ảnh ở ngưỡng 80.
- **Nhãn nhiều nhất:** Animal (7 ảnh), Plant (5 ảnh), sau đó là Gas Pump, Grass, Machine và Pump (mỗi nhãn 4 ảnh), với độ tin cậy trung bình khoảng 95 đến 97.
- **Tìm theo nhãn cha:** lọc các ảnh có nhãn cha Vehicle trả về 3 ảnh.
- **Chi phí Athena:** mỗi truy vấn quét 18,87 KB, chạy khoảng 300 ms.
- **Chi phí Rekognition:** 60 lượt gọi (30 ảnh với hai ngưỡng), nằm trong free tier; theo giá niêm yết khoảng 0,06 USD.

### Confidence và MinConfidence

Mình chạy thêm một lượt với `MinConfidence` là 55 trên cùng bộ ảnh: ngưỡng 80 cho 166 nhãn (84 nhãn khác nhau), ngưỡng 55 cho 243 nhãn (124 nhãn khác nhau). Ngưỡng thấp cho nhiều nhãn hơn nhưng có thể kèm nhãn kém chính xác, ngưỡng cao cho ít nhãn hơn nhưng đáng tin hơn. Đây là sự đánh đổi quen thuộc giữa độ phủ và độ chính xác.

Mình cũng kiểm tra nhanh kết quả: mỗi loại có đúng 3 ảnh, nhưng nhãn Gas Pump xuất hiện ở 4 ảnh, nghĩa là có ít nhất một ảnh thuộc loại khác bị gán nhãn này, trong khi nhãn Brass Section xuất hiện ở 3 ảnh, khớp với 3 ảnh kèn French horn. Vì vậy nhãn chỉ nên xem là dự đoán của mô hình, cần kiểm tra mẫu khi dùng thực tế.

### Một số lưu ý

- **Chi phí:** mỗi lần gọi một API trên một ảnh được tính là một ảnh, nên chạy lại với ngưỡng khác sẽ tính thêm.
- **Quyền riêng tư:** nên dùng bộ dữ liệu công khai hoặc ảnh của chính mình, không đưa ảnh của người khác lên khi chưa được đồng ý.
- **IAM:** chỉ cấp quyền cần thiết như `rekognition:DetectLabels` và quyền đọc ảnh trong đúng thư mục S3.
- **Dọn dẹp:** sau khi thử nên xóa ảnh, tệp kết quả trong S3 và bảng trong Athena.

### Kết luận

Rekognition và Athena bổ trợ cho nhau khi biến dữ liệu ảnh thành thông tin có thể thống kê: Rekognition phân tích ảnh và trả về nhãn kèm độ tin cậy mà không cần kiến thức machine learning, còn Athena truy vấn kết quả bằng SQL ngay trên S3 mà không cần máy chủ. Với S3 làm nơi lưu cả ảnh lẫn kết quả, mình có thể xây dựng một quy trình đơn giản từ ảnh thô đến bảng thống kê, rồi mở rộng thành tự động hóa khi có ảnh mới.

### Tài liệu tham khảo

- What is Amazon Rekognition?: <https://docs.aws.amazon.com/rekognition/latest/dg/what-is.html>
- DetectLabels (Amazon Rekognition API Reference): <https://docs.aws.amazon.com/rekognition/latest/APIReference/API_DetectLabels.html>
- aws rekognition detect-labels (AWS CLI Command Reference): <https://docs.aws.amazon.com/cli/latest/reference/rekognition/detect-labels.html>
- Amazon Rekognition Pricing: <https://aws.amazon.com/rekognition/pricing/>
- Imagenette (Hugging Face dataset): <https://huggingface.co/datasets/LibreYOLO/imagenette160>
- Amazon Athena Pricing: <https://aws.amazon.com/athena/pricing/>

`#AWS` `#AmazonRekognition` `#AmazonAthena` `#ComputerVision` `#DeepLearning` `#ArtificialIntelligence` `#CloudComputing` `#AWSStudyGroup`
