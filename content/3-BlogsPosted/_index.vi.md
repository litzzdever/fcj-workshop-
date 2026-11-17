---
title: "Các bài blogs đã đăng"
date: 2026-11-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Tại đây liệt kê các bài blog tôi đã đăng trên nhóm [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj) trong quá trình thực tập.

###  [Blog 1 - AMAZON SAGEMAKER AI – AWS GIÚP AI ENGINEER TRAIN, TUNE VÀ DEPLOY MODEL NHƯ THẾ NÀO?](3.1-Blog1/)
*Đăng ngày 01/11/2026.* Bài viết giới thiệu Amazon SageMaker AI, dịch vụ machine learning được quản lý toàn phần giúp AI engineer đưa model từ notebook lên production: Feature Store, train linh hoạt với instance preference list, tune bằng serverless model customization (SFT, DPO, RLAIF), chọn cấu hình deploy bằng inference recommendations và giám sát hơn 100 metric trên CloudWatch. Bài cũng nêu các trường hợp sử dụng, lưu ý về chi phí, region và một lỗi hay gặp với SageMaker SDK v3.

###  [Blog 2 - Amazon Athena kết hợp AWS Glue Data Catalog – Truy vấn dữ liệu trong S3 bằng SQL mà không cần máy chủ](3.2-Blog2/)
*Đăng ngày 11/11/2026.* Bài viết tìm hiểu cách Glue Data Catalog lưu metadata còn Athena chạy SQL trực tiếp trên dữ liệu S3 và tính phí theo lượng dữ liệu quét. Thử nghiệm với 1.000.000 dòng dữ liệu bán hàng mô phỏng cho thấy chuyển CSV sang Parquet và chia partition theo tháng giúp giảm lượng dữ liệu quét từ khoảng 11 đến khoảng 1.300 lần tùy truy vấn, kèm các lưu ý về partition, kích thước tệp và kiểm soát chi phí.

###  [Blog 3 - Amazon Rekognition kết hợp Amazon Athena – Phân tích ảnh bằng AI và truy vấn kết quả bằng SQL](3.3-Blog3/)
*Đăng ngày 14/11/2026.* Bài viết dùng Amazon Rekognition (`DetectLabels`) để tự gắn nhãn cho 30 ảnh từ bộ dữ liệu Imagenette, lưu kết quả dạng JSON Lines trên S3 rồi thống kê bằng SQL trong Amazon Athena. Bài so sánh hai ngưỡng `MinConfidence` 80 và 55 (166 và 243 nhãn), kiểm tra độ chính xác của nhãn và nêu các lưu ý về chi phí, quyền riêng tư và IAM.
