---
title: "Worklog Tuần 6"
date: 2026-10-21
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

* Duy trì và cải tiến chatbot RAG bán phụ tùng xe máy trên AWS với API Gateway WebSocket, Lambda, OpenSearch Serverless, DynamoDB và Gemini.
* Khắc phục sự cố hạ tầng và giữ đường kết nối WebSocket ổn định cho nhóm.
* Đo độ trễ của lần hỏi đầu tiên sau thời gian không dùng (cold start).
* Thiết kế lại khung chatbot theo hệ thống đang chạy và kiểm thử toàn bộ luồng vận hành.
* Ghi lại tài liệu, sơ đồ và chi phí theo hệ thống thực tế.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 4 | - Thử thêm route `$connect` kiểu Mock cho API Gateway WebSocket; mọi kết nối trả lỗi 500, sau đó API `chatbot-ws` bị xóa nhầm <br> - Tạo lại API `chatbot-ws`: route selection `$request.body.action`, route `sendMessage` gắn Lambda `chat-receiver`, stage `dev`; đổi sang URL WebSocket mới <br> - Kiểm thử lại sau khi tạo: câu hỏi chính sách 3,7 giây, câu hỏi sản phẩm 3,6 giây khi instance đang nóng <br> - Đo cold start sau nhiều ngày không dùng: tổng 150,8 giây, gồm nạp model 10,9 giây, embed 127,3 giây, tìm kiếm OpenSearch 19,7 giây, Gemini 3,6 giây. Câu xem giỏ hàng vẫn nhanh (3,1 giây) vì đường tắt chạy trước bước embedding <br> - Đọc chi phí OpenSearch Serverless từ Cost Explorer: khoảng $16,98 từ 30/9 đến 17/10; ngày dùng thưa gần $0, hai ngày test dồn dập 16 và 17/10 là $6,02 và $9,06; ngày 19/10 cả tài khoản còn khoảng $0,40 <br> - Đặt ECR lifecycle giữ 3 image mới nhất và thời gian lưu CloudWatch Logs 2 tuần <br> - Cập nhật `README.md` (hợp đồng giao tiếp), worklog và sơ đồ kiến trúc theo hạ tầng thực tế: Lambda không nằm trong VPC, không có NAT Gateway <br> - Rút bài học: không thử cấu hình mới trên stage đang chạy; muốn thử `$connect` thì tạo API riêng | 21/10/2026 | 21/10/2026 | Tài liệu nội bộ của dự án chatbot |
| 5 | - Vẽ lại sơ đồ khung chatbot theo hệ thống đang chạy: API Gateway WebSocket `chatbot-ws` → Lambda `chat-receiver` → Lambda `chat-orchestrator-img` (container image trong ECR) → OpenSearch Serverless (`kb-index` cho tài liệu chính sách, `products-index` cho sản phẩm) → Gemini <br> - Ghi các thành phần phụ trợ: DynamoDB (Products, Carts, Orders, Users, chatbot-sessions), Secrets Manager giữ khóa Gemini, SNS gửi email khi chuyển nhân viên, EventBridge Scheduler cho warmup <br> - Phân luồng xử lý: thao tác giỏ hàng chạy đường tắt không qua embedding; câu hỏi chính sách và tư vấn sản phẩm đi qua embedding bge-m3 và tìm kiếm kết hợp (0,7 vector + 0,3 từ khóa); yêu cầu gặp nhân viên tạo mã yêu cầu <br> - Đối chiếu với sơ đồ VPC của nhóm: chatbot không nằm trong VPC nên không có NAT Gateway, VPC endpoint hay node OpenSearch chạy cố định; sơ đồ VPC được ghi rõ là kiến trúc mục tiêu cho production <br> - Kiểm thử vận hành qua WebSocket thật bằng notebook `test_chatbot_answers.ipynb`, thời gian khi hệ thống đã nóng: hỏi chính sách đổi trả 8,0 giây; tư vấn bố thắng theo xe 5,1 giây; hỏi tiếp về xe khác để kiểm tra nhớ ngữ cảnh 5,1 giây; cá nhân hóa theo Garage 6,1 giây; xem giỏ hàng 0,7 giây; thêm 20 phuộc khi kho còn 8 bị từ chối 5,7 giây; khách chưa đăng nhập thêm giỏ bị báo cần đăng nhập 9,7 giây; câu ngoài phạm vi (thời tiết) bị từ chối lịch sự 6,1 giây; yêu cầu gặp nhân viên tạo mã yêu cầu và gửi email qua SNS 14,2 giây <br> - Ghi nhận ba phát hiện: giỏ hàng test của `USR_98765` còn dữ liệu từ các lần test cần dọn trước demo; câu ngoài phạm vi vẫn trả `low_confidence = false` dù bot từ chối đúng; cold start cần xử lý trước demo | 22/10/2026 | 22/10/2026 | Tài liệu nội bộ của dự án chatbot |

### Kết quả đạt được tuần 6:

* Khôi phục đường kết nối WebSocket sau sự cố và kiểm thử lại: câu chính sách 3,7 giây, câu sản phẩm 3,6 giây khi hệ thống đã nóng.

* Đo được cold start 150,8 giây và xác định thời gian chủ yếu nằm ở bước embed và tìm kiếm OpenSearch.

* Vẽ lại khung chatbot theo hạ tầng đang chạy (Lambda ngoài VPC, không NAT) và đối chiếu với sơ đồ VPC mục tiêu của nhóm.

* Kiểm thử 9 tình huống vận hành qua WebSocket thật gồm chính sách, tư vấn theo xe, Garage, giỏ hàng, vượt tồn kho, chưa đăng nhập, ngoài phạm vi và chuyển nhân viên; thời gian phản hồi khi hệ thống nóng từ 0,7 đến 14,2 giây.

* Ghi lại chi phí OpenSearch Serverless từ Cost Explorer và cập nhật README, worklog, sơ đồ theo hệ thống thực tế.
