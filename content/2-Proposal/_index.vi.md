---
title: "Bản đề xuất"
date: 2026-10-30
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Phụ Tùng Xe Máy Platform
## Nền tảng thương mại điện tử phụ tùng xe máy trên AWS Serverless, tích hợp chatbot AI tư vấn

Mã nguồn dự án: [github.com/lointt/phutungxemay-platform](https://github.com/lointt/phutungxemay-platform)

### 1. Tóm tắt điều hành

Phụ Tùng Xe Máy Platform là website bán phụ tùng xe máy chính hãng, cho phép khách hàng tìm phụ tùng theo dòng xe, đặt hàng, thanh toán qua VNPay hoặc COD, theo dõi vận đơn Giao Hàng Nhanh (GHN) và nhận email xác nhận. Website có chatbot AI tư vấn phụ tùng, trả lời chính sách cửa hàng, kiểm tra tồn kho, thêm vào giỏ hàng và chuyển cho nhân viên khi cần.

Hệ thống được xây dựng **100% serverless trên AWS** tại vùng Singapore (`ap-southeast-1`): frontend Next.js 16 chạy trên AWS Lambda và Amazon CloudFront, 5 microservice Python sau Amazon API Gateway, xử lý đơn hàng bất đồng bộ bằng Amazon EventBridge và Amazon SQS, dữ liệu trên Amazon DynamoDB, đăng nhập bằng Amazon Cognito. Toàn bộ hạ tầng được quản lý bằng **Terraform** và triển khai tự động bằng **GitHub Actions**. Kiến trúc không có máy chủ chạy thường trực, nên chi phí tăng theo lượng truy cập thật và gần bằng 0 khi ít người dùng.

### 2. Tuyên bố vấn đề

**Vấn đề hiện tại**

- Khách hàng khó tìm đúng phụ tùng cho dòng xe của mình; cửa hàng nhỏ thường tư vấn thủ công qua điện thoại hoặc tin nhắn.
- Quy trình sau khi đặt hàng (trừ kho, tạo vận đơn, gửi email) làm bằng tay dễ sai sót, nhất là khi nhiều đơn đến cùng lúc có thể làm tồn kho bị âm.
- Thuê máy chủ chạy 24/7 tốn chi phí cố định dù lượng truy cập của cửa hàng nhỏ không đều.
- Chatbot AI dùng cơ sở dữ liệu vector chuyên dụng (ví dụ Amazon OpenSearch Serverless) có chi phí cố định hàng trăm USD mỗi tháng, không phù hợp quy mô cửa hàng.

**Giải pháp**

- Website thương mại điện tử đầy đủ: danh mục phụ tùng có lọc theo dòng xe, giỏ hàng, đặt hàng, "Garage" lưu xe của khách, trang quản trị cho admin.
- Backend tách thành microservice trên Lambda; các bước sau đặt hàng chạy bất đồng bộ qua EventBridge và SQS kèm hàng đợi lỗi (DLQ), nên một bước lỗi không làm hỏng cả đơn hàng.
- Trừ kho nguyên tử bằng điều kiện `stock >= :qty` trên DynamoDB, chống tồn kho âm khi nhiều đơn đồng thời.
- Chatbot RAG không dùng OpenSearch: vector lưu dạng file JSON trên S3, nạp vào bộ nhớ Lambda, tự cập nhật khi dữ liệu sản phẩm thay đổi nhờ DynamoDB Streams.

**Lợi ích và giá trị mang lại**

- Khách tự tra cứu và mua phụ tùng 24/7; chatbot giảm số câu hỏi lặp lại mà nhân viên phải trả lời.
- Đơn hàng được xử lý tự động từ thanh toán đến vận đơn và email, giảm thao tác thủ công.
- Chi phí vận hành ước tính khoảng 12–13 USD mỗi tháng ở quy mô vừa (mục 6), trong đó phần lớn là chi phí mô hình AI; có cảnh báo ngân sách để kiểm soát.
- Hạ tầng dạng mã (Terraform) giúp dựng lại toàn bộ môi trường nhanh chóng và nhất quán.

### 3. Kiến trúc giải pháp

![Kiến trúc Phụ Tùng Xe Máy Platform](/images/2-Proposal/phutungxemay_architecture.png)

**Luồng truy cập website**

1. Trình duyệt truy cập Amazon CloudFront. Đường dẫn `/_next/*` và ảnh sản phẩm lấy từ Amazon S3; các trang động được render (SSR) bởi Lambda chạy Next.js qua adapter OpenNext.
2. Đường dẫn `/api/*` đi qua một CloudFront Function để bỏ tiền tố `/api`, rồi tới API Gateway REST có Cognito Authorizer.
3. API Gateway gọi 5 microservice: Catalog, Cart, Order, Payment Webhook (VNPay IPN) và User Profile; dữ liệu nằm trong các bảng DynamoDB Products, Orders, Carts, Users.

**Luồng xử lý đơn hàng (event-driven)**

1. Order Service và Payment Webhook phát sự kiện lên một EventBridge custom bus.
2. Rule của EventBridge đẩy sự kiện vào 3 hàng đợi SQS, mỗi hàng đợi có DLQ riêng.
3. Ba worker xử lý: Inventory Worker trừ hoặc hoàn kho, Shipping Worker tạo vận đơn GHN API v2 (chuẩn hóa địa chỉ Việt Nam bằng fuzzy match), Notification Worker gửi email hóa đơn HTML qua Gmail SMTP.

**Luồng chatbot AI (RAG)**

1. Khách chat qua API Gateway WebSocket, xác thực JWT Cognito bằng Lambda Authorizer.
2. Lambda Chat Receiver nhận tin nhắn và gọi bất đồng bộ Lambda Chat Orchestrator (đóng gói dạng container image).
3. Orchestrator tìm kiếm kết hợp embedding Gemini và BM25 trên vector trong S3, đọc tồn kho và giỏ hàng trong DynamoDB, rồi gọi Gemini để trả lời; có chế độ dự phòng khi Gemini quá tải.
4. Khi khách cần người thật, Orchestrator phát sự kiện `HumanHandoverRequested` lên EventBridge để Notification Worker gửi email cho nhân viên.
5. Lambda Sync-to-KB đọc DynamoDB Streams và cập nhật vector sản phẩm trên S3 mỗi khi dữ liệu thay đổi.

**Các dịch vụ AWS sử dụng**

| Dịch vụ | Vai trò |
|---|---|
| Amazon CloudFront | CDN, định tuyến tĩnh, SSR và API; CloudFront Function bỏ tiền tố `/api` |
| Amazon S3 | Tài nguyên tĩnh của Next.js, ảnh phụ tùng, file vector của chatbot |
| AWS Lambda | SSR Next.js (Node.js 22), 5 microservice, 3 worker, 4 hàm chatbot (Python 3.12) |
| Amazon API Gateway | REST API cho website, WebSocket API cho chatbot |
| Amazon Cognito | Đăng ký, đăng nhập, nhóm Admins và Customers |
| Amazon DynamoDB | Products, Orders, Carts, Users, phiên chatbot; chế độ on-demand |
| Amazon EventBridge | Event bus cho sự kiện đơn hàng và yêu cầu chuyển nhân viên |
| Amazon SQS | 3 hàng đợi xử lý và 3 DLQ |
| AWS Secrets Manager | Khóa VNPay, GHN, mật khẩu SMTP, khóa Gemini (mã hóa KMS) |
| Amazon CloudWatch, Amazon SNS | Cảnh báo DLQ và lỗi, dashboard giám sát, gửi cảnh báo qua email |
| AWS Budgets | Cảnh báo khi chi phí tháng vượt ngưỡng (mặc định 10 USD) |
| IAM, IAM Access Analyzer | Phân quyền theo vai trò cho từng Lambda, rà soát quyền tự động |

**Dịch vụ bên ngoài:** VNPay Sandbox (thanh toán, chữ ký HMAC-SHA512), Giao Hàng Nhanh API v2 (vận đơn), Gmail SMTP (email), Google Gemini API (embedding và mô hình ngôn ngữ).

### 4. Triển khai kỹ thuật

**Công nghệ**

- *Frontend:* Next.js 16 (App Router, Server và Client Components, streaming SSR), Tailwind CSS v4, TypeScript, triển khai bằng `@opennextjs/aws`.
- *Backend:* Python 3.12 trên Lambda, thiết kế REST rõ ràng theo vai trò (Public, Customer, Admin).
- *Chatbot:* Gemini `gemini-embedding-001` (768 chiều) cho embedding, BM25 kết hợp cosine, function calling cho các công cụ `add_to_cart` và `request_human_agent`, giới hạn tần suất theo phiên, kết nối và ngày.
- *Hạ tầng:* Terraform quản lý toàn bộ tài nguyên (khoảng 3.600 dòng HCL, gồm 13 hàm Lambda, 6 bảng DynamoDB, 7 hàng đợi SQS, 4 secret, 6 cảnh báo CloudWatch).
- *CI/CD:* GitHub Actions với 3 workflow độc lập: `frontend.yml` (build OpenNext, đồng bộ S3, cập nhật Lambda SSR, xóa cache CloudFront), `backend.yml` (đóng gói và cập nhật các Lambda Python), `terraform.yml`.

**Các cơ chế kỹ thuật chính**

- Trừ kho nguyên tử bằng `ConditionExpression` để không bao giờ âm kho.
- Xác thực chữ ký VNPay bằng HMAC-SHA512, xử lý IPN có idempotency để không ghi trùng đơn.
- Mỗi hàng đợi có DLQ và cảnh báo CloudWatch khi có tin nhắn lỗi.
- Secret được chuyển từ SSM Parameter Store sang Secrets Manager có mã hóa KMS; bucket S3 chặn truy cập công khai và bắt buộc HTTPS.
- Bộ kiểm thử offline cho chatbot và kiểm thử bảo mật cho các microservice.

### 5. Lộ trình và mốc triển khai

| Giai đoạn | Thời gian | Nội dung |
|---|---|---|
| Khởi tạo | 30/10/2026 | Dựng frontend Next.js, hạ tầng Terraform cho frontend và CI/CD |
| Backend và event-driven | 30/10 – 10/11/2026 | 5 microservice, EventBridge, SQS và DLQ, 3 worker, tích hợp VNPay, GHN, Gmail SMTP |
| Chatbot | 01/11 – 13/11/2026 | Đưa chatbot vào Terraform, đồng bộ CDC từ DynamoDB sang vector S3, gộp email chuyển nhân viên vào Notification Worker |
| Bảo mật và giám sát | 10/11 – 16/11/2026 | Bắt buộc đăng nhập khi thanh toán, Cognito Authorizer cho WebSocket, IAM Access Analyzer, chuyển secret sang Secrets Manager |
| Hoàn thiện | 17/11 – 14/12/2026 | Kiểm thử đầu cuối, kiểm thử tải, tối ưu chi phí, chuẩn bị demo và tài liệu bàn giao |

### 6. Ước tính ngân sách

Dự toán theo kịch bản quy mô vừa mỗi tháng: **30.000 lượt xem trang, 300.000 lượt gọi API, 1.000 đơn hàng, 10.000 câu hỏi chatbot**, vùng Singapore. Phần chatbot dùng số liệu từ bản ước tính trên AWS Pricing Calculator của nhóm ([link ước tính](https://calculator.aws/#/estimate?id=d87e34b9f9a1e9f419182c4da7f6281aa720795f)); các dịch vụ còn lại tính theo bảng giá công bố của AWS và cần được xác nhận lại trên Pricing Calculator trước khi chốt.

| Dịch vụ | Giả định | Ước tính (USD/tháng) |
|---|---|---|
| Amazon CloudFront | Khoảng 60 GB và 1 triệu request, nằm trong mức miễn phí 1 TB và 10 triệu request mỗi tháng | 0,00 |
| Amazon S3 | Vài GB tài nguyên tĩnh và ảnh | 0,10 |
| AWS Lambda (website) | Khoảng 0,4 triệu lượt gọi, 70.000 GB-giây, trong mức miễn phí 1 triệu lượt và 400.000 GB-giây | 0,00 |
| Amazon API Gateway REST | 300.000 request, 3,50 USD mỗi triệu request | 1,05 |
| Amazon DynamoDB | On-demand, khoảng 600.000 lượt đọc và 50.000 lượt ghi | 0,20 |
| Amazon SQS, EventBridge | Vài nghìn sự kiện, trong mức miễn phí của SQS | 0,00 |
| Amazon Cognito | Dưới 10.000 người dùng hoạt động mỗi tháng (miễn phí) | 0,00 |
| AWS Secrets Manager | 4 secret, 0,40 USD mỗi secret | 1,60 |
| CloudWatch, SNS, Budgets | 6 cảnh báo, 1 dashboard, log nhỏ, trong mức miễn phí | 0,00 |
| Chatbot trên AWS | WebSocket, Lambda, DynamoDB, S3 ở 10.000 câu hỏi (theo Pricing Calculator, chưa tính mức miễn phí) | 1,92 |
| **Tổng phần AWS** | | **khoảng 4,9** |
| Google Gemini API (ngoài AWS) | Embedding và sinh câu trả lời cho 10.000 câu hỏi, theo giá trần giả định | khoảng 7,5 |
| **Tổng cộng** | | **khoảng 12–13** |

- Terraform đã tạo AWS Budgets với ngưỡng mặc định **10 USD mỗi tháng** cho phần AWS. Chi phí Gemini nằm ngoài AWS nên cần theo dõi riêng trên Google.
- Khi lượng truy cập thấp, gần như mọi dịch vụ nằm trong mức miễn phí; chi phí cố định chủ yếu là Secrets Manager (1,60 USD).
- So với phương án dùng OpenSearch Serverless cho chatbot (khoảng 175–350 USD mỗi tháng chỉ riêng chi phí cố định), kiến trúc vector trên S3 tiết kiệm phần lớn chi phí.

### 7. Đánh giá rủi ro

| Rủi ro | Ảnh hưởng | Xác suất | Cách giảm thiểu |
|---|---|---|---|
| Gemini quá tải hoặc giới hạn tần suất (lỗi 429, 503, 504) | Trung bình | Trung bình | Chuỗi mô hình dự phòng, chế độ trả lời dự phòng từ dữ liệu có sẵn, làm nóng Lambda trước demo |
| Chi phí vượt dự kiến | Trung bình | Thấp | AWS Budgets, giới hạn số câu hỏi chatbot mỗi ngày, DynamoDB on-demand |
| Xử lý đơn thất bại (GHN, SMTP lỗi) | Cao | Thấp | SQS có DLQ, cảnh báo CloudWatch qua SNS, xử lý lại tin nhắn từ DLQ |
| Bán vượt tồn kho khi nhiều đơn đồng thời | Cao | Thấp | Trừ kho nguyên tử có điều kiện trên DynamoDB |
| Lộ khóa API hoặc thông tin thanh toán | Cao | Thấp | Secrets Manager mã hóa KMS, IAM tối thiểu, IAM Access Analyzer, không lưu khóa trong repo |
| Độ trễ cao với khách ở Việt Nam | Thấp | Trung bình | CloudFront đang dùng `PriceClass_100` (chỉ edge ở Bắc Mỹ và châu Âu); có thể nâng lên `PriceClass_200` để dùng edge ở châu Á |
| Cold start của chatbot (khoảng 7 giây) | Thấp | Trung bình | Bộ nhớ Lambda phù hợp, làm nóng định kỳ trước giờ cao điểm |

**Đề xuất cải tiến:** CI/CD hiện dùng access key IAM lưu trong GitHub Secrets; nên chuyển sang **OIDC với IAM role** để không còn khóa dài hạn.

### 8. Kết quả kỳ vọng

- Website thương mại điện tử hoạt động đầy đủ từ xem sản phẩm đến thanh toán, vận đơn và email, chạy hoàn toàn serverless.
- Chatbot trả lời đúng chính sách và sản phẩm, thêm được vào giỏ hàng và chuyển nhân viên. Bản v6 đã đạt KB 6/6, sản phẩm 12/12, ngoài phạm vi 4/4 trong kiểm thử đầu cuối, thời gian trung bình khoảng 2,5 giây mỗi câu.
- Hạ tầng dựng lại được hoàn toàn bằng Terraform, triển khai tự động bằng GitHub Actions, có giám sát và cảnh báo chi phí.
- Chi phí vận hành khoảng 12–13 USD mỗi tháng ở quy mô vừa, gần 0 khi ít truy cập.
- Nền tảng làm cơ sở để mở rộng: thêm phương thức thanh toán, nhiều đơn vị vận chuyển, hoặc chuyển chatbot sang Amazon Bedrock khi cần.
