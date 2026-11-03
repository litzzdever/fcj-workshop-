---
title: "Bản đề xuất"
date: 2026-11-03
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Conversational Commerce Agent
## Chatbot RAG tư vấn phụ tùng xe máy trên AWS Serverless

### 1. Tổng quan bài toán và giải pháp

Website bán phụ tùng xe máy của nhóm cần một nhân viên tư vấn trực tuyến hoạt động 24/7: trả lời giá, tồn kho, phụ tùng nào lắp vừa xe của khách, giải đáp chính sách bảo hành, đổi trả, vận chuyển, thêm hàng vào giỏ và chuyển cho nhân viên thật khi cần.

Giải pháp là một chatbot RAG (Retrieval-Augmented Generation) chạy hoàn toàn bằng dịch vụ AWS theo nhu cầu (serverless) kết hợp Google Gemini API. Với mỗi câu hỏi, chatbot tìm các sản phẩm và đoạn tài liệu chính sách liên quan rồi nhờ Gemini soạn câu trả lời tiếng Việt, chỉ dựa trên dữ liệu tìm được nên không bịa giá hay tồn kho. Chatbot kết nối với website qua một địa chỉ WebSocket duy nhất.

Điểm khác biệt so với phương án ban đầu: bản đầu dùng Amazon OpenSearch Serverless và model embedding chạy trong Lambda (chi phí cố định khoảng 175–350 USD mỗi tháng). Bản hiện tại bỏ OpenSearch, lưu vector trong file JSON trên S3 và dùng Gemini để tạo embedding, nên chi phí cố định gần bằng 0 và chi phí tăng theo số câu hỏi thật.

Trạng thái hiện tại: phiên bản v6 đã chạy trên tài khoản AWS của nhóm (vùng ap-southeast-1), đạt toàn bộ bài kiểm thử đầu cuối ngày 30/10/2026. Phần còn lại là ba cải tiến theo góp ý của leader (mục 5).

### 2. Mục tiêu và phạm vi

**Bối cảnh và đối tượng sử dụng**

- Khách hàng của website bán phụ tùng xe máy: cần tư vấn nhanh theo dòng xe và đời xe của mình.
- Nhân viên chăm sóc khách hàng (CSKH): chỉ muốn nhận những yêu cầu bot không tự giải quyết được, kèm đủ ngữ cảnh.
- Nhóm phát triển: cần hệ thống rẻ, dễ vận hành, tích hợp được vào backend và frontend hiện có.

**Mục tiêu cụ thể và tiêu chí hoàn thành**

| Mục tiêu | Tiêu chí đo lường | Kết quả hiện tại |
|---|---|---|
| Trả lời đúng chính sách | 6/6 câu kiểm thử về bảo hành, đổi trả, vận chuyển trả lời đúng và ghi nguồn tài liệu | 6/6 |
| Tư vấn sản phẩm đúng dữ liệu | 12/12 câu kiểm thử theo xe và đúng mã sản phẩm | 12/12 |
| Không bịa ngoài phạm vi | 4/4 câu ngoài chủ đề (thời tiết, thơ, bitcoin, nấu ăn) không gợi ý sản phẩm, không tạo phiếu | 4/4 |
| Thêm giỏ hàng an toàn | Từ chối số lượng vượt tồn kho hoặc quá 10, không tự đổi số lượng của khách | Đạt |
| Chuyển nhân viên | Nhân viên nhận đúng 1 email Gmail kèm mã yêu cầu, ngữ cảnh | Đạt |
| Phản hồi nhanh | Khoảng 2–3 giây mỗi câu khi Gemini ổn định | Khoảng 2–3 giây |
| Chi phí thấp | Từ 10 USD mỗi tháng trở xuống ở mức 10.000 câu hỏi mỗi tháng | Khoảng 9,4 USD (xem mục 6) |

**Phạm vi thực hiện**

- Trong phạm vi: hỏi đáp sản phẩm và chính sách, đối chiếu xe trong Garage của khách, giỏ hàng (thêm, xem), chuyển nhân viên qua email, giám sát và cảnh báo, giới hạn chống lạm dụng, tài liệu vận hành.
- Ngoài phạm vi: thanh toán, tạo hoặc hủy đơn hàng, nhận ảnh từ khách, hỗ trợ ngôn ngữ ngoài tiếng Việt và tiếng Anh.

**Phù hợp định hướng Cloud và AWS.** Toàn bộ chatbot dùng kiến trúc serverless trả tiền theo lượt dùng: API Gateway, Lambda, DynamoDB, S3, EventBridge, Systems Manager Parameter Store, CloudWatch.

### 3. Vấn đề cần giải quyết

1. **Khách khó chọn đúng phụ tùng.** Cùng một loại phụ tùng nhưng mỗi dòng xe và đời xe lắp một loại khác nhau. Khách hay mua nhầm rồi đổi trả, nhân viên mất thời gian trả lời lặp đi lặp lại.
2. **Không có người trực ngoài giờ.** Câu hỏi về giá, tồn kho, chính sách đến bất kỳ lúc nào.
3. **Chatbot dùng AI dễ bịa thông tin.** Với bán hàng, sai giá hoặc báo còn hàng khi đã hết gây thiệt hại thật. Hệ thống phải chỉ trả lời từ dữ liệu của cửa hàng.
4. **Chi phí hạ tầng cho chatbot dễ vượt quá giá trị mang lại.** Phiên bản đầu tiên dùng OpenSearch Serverless và mạng riêng (VPC, NAT) có chi phí cố định hàng trăm USD mỗi tháng, không phù hợp với một dự án nhỏ. Leader đã đặt mục tiêu 5–10 USD mỗi tháng.
5. **Cần chuyển cho người thật đúng lúc.** Khi khách khiếu nại hoặc bot không giải quyết được, nhân viên phải nhận được đủ ngữ cảnh mà không phải hỏi lại.
6. **Cần vận hành được.** Gemini bản miễn phí hay quá tải vào buổi tối, vì vậy hệ thống phải có phương án dự phòng, giới hạn tần suất và cảnh báo.

### 4. Kiến trúc giải pháp sơ bộ

![Khung 3: Chatbot RAG](/images/2-Proposal/chatbot_architecture.png)

**Luồng hỏi đáp (đi từ trái sang phải trong sơ đồ):**

1. Website gửi câu hỏi qua **API Gateway (WebSocket)**.
2. **ChatReceiverHandler** nhận và gọi bất đồng bộ **ChatOrchestratorHandler**, để kết nối không bị treo trong lúc AI xử lý.
3. **ChatOrchestratorHandler** kiểm tra giới hạn tần suất, tạo vector cho câu hỏi bằng **Gemini Embedding**, tìm trong kho vector trên **S3** (đã nạp vào RAM), bổ sung giá và tồn kho mới nhất, xe trong Garage và lịch sử hội thoại từ **DynamoDB**.
4. Orchestrator gọi **Gemini** để soạn câu trả lời. Gemini có thể gọi công cụ thêm giỏ hàng hoặc chuyển nhân viên, và mỗi công cụ đều được kiểm tra lại bằng mã chương trình.
5. Câu trả lời được đẩy về đúng kết nối của khách qua API Gateway.
6. Khóa Gemini lấy từ **SSM Parameter Store**, không nằm trong code.

**Luồng chuyển nhân viên:** Orchestrator phát sự kiện lên **EventBridge** → **HandoverMailerHandler** soạn thư → gửi qua **Gmail SMTP** đến hộp thư nhân viên.

**Giám sát:** CloudWatch theo dõi lỗi và độ trễ của Lambda, DynamoDB, WebSocket và số lần Gemini lỗi; khi bất thường thì báo qua SNS đến email của nhóm.

**Các dịch vụ sử dụng**

| Dịch vụ | Vai trò |
|---|---|
| Amazon API Gateway (WebSocket) | Cổng nhận tin và đẩy câu trả lời về khách |
| AWS Lambda (3 hàm) | `chat-receiver`, `chat-orchestrator`, `chat-handover-mailer` |
| Amazon S3 | Kho vector của tài liệu và sản phẩm (`kb.json`, `products.json`) |
| Amazon DynamoDB | Sản phẩm, giỏ hàng, khách hàng, lịch sử hội thoại, bộ đếm giới hạn tần suất |
| AWS Systems Manager Parameter Store | Lưu khóa Gemini và mật khẩu ứng dụng Gmail (miễn phí, thay cho Secrets Manager) |
| Amazon EventBridge | Chuyển sự kiện yêu cầu gặp nhân viên |
| Amazon CloudWatch và Amazon SNS | Cảnh báo lỗi, độ trễ, Gemini quá tải |
| AWS IAM | Role riêng, quyền tối thiểu cho từng Lambda |
| Google Gemini API (ngoài AWS) | Tạo embedding và soạn câu trả lời |

**Ba cải tiến đang thực hiện theo góp ý của leader**

- **Đóng gói Lambda bằng .zip thay vì Docker và ECR.** Sau khi bỏ torch, mình đã đo gói thật: 39 MB giải nén, 10,9 MB nén, nằm trong giới hạn của Lambda (250 MB giải nén, 50 MB nén). Cách này bỏ hẳn ECR và bước build image.
- **Khung 4, tự động đồng bộ vector.** Thay vì chạy script `build_vector_snapshot.py` bằng tay, bật DynamoDB Streams trên bảng Products để một Lambda đồng bộ tự tạo embedding bằng Gemini và cập nhật file vector trên S3 mỗi khi sản phẩm đổi. Tài liệu chính sách trên S3 cũng kích hoạt cập nhật tương tự.
- **Viết hạ tầng bằng Terraform** theo cấu trúc của dự án (thêm các file chatbot như `lambda_chatbot.tf`, `iam_chatbot.tf`, `eventbridge.tf`, `monitoring.tf`), thay cho các file CloudFormation đang dùng.

### 5. Kế hoạch và timeline thực hiện

| Giai đoạn | Thời gian | Kết quả |
|---|---|---|
| Thiết kế và phiên bản đầu (OpenSearch Serverless) | Đến 24/10/2026 | Chatbot chạy được, nhưng chi phí cố định cao |
| Chuyển sang v2 không OpenSearch, triển khai trên tài khoản của nhóm | 25–27/10 | Vector trên S3, embedding bằng Gemini, 3 Lambda chạy |
| Chuyển nhân viên bằng Gmail SMTP, dọn tài khoản cũ | 28/10 | Email thật thay cho SNS, đã xóa OpenSearch ở tài khoản cũ |
| Tối ưu prompt và dự phòng (v6), kiểm thử đầu cuối | 30/10 | 39 test offline đạt; kiểm thử qua WebSocket đạt |
| Tài liệu vận hành và Proposal | 30/10–03/11 | Tài liệu từng bước theo sơ đồ Khung 3, bản đề xuất này |
| Sửa theo góp ý leader: gói .zip, Khung 4 tự đồng bộ, Terraform | Dự kiến tuần 03–09/11 | Hạ tầng đúng chuẩn dự án, không còn ECR, vector tự cập nhật |
| Gắn chatbot vào website, thử nghiệm với người dùng thật | Sau khi hoàn tất các bước trên | Chạy thật trên website của nhóm |
| Xác thực bằng Lambda authorizer (Cognito), bật thanh toán Gemini trước buổi demo | Trước demo | Không giả mạo được mã khách; không bị quá tải giờ cao điểm |

### 6. Bảng dự toán ngân sách và chi phí (Cost Estimation)

**Cách tính.** Dự toán dựa trên bảng giá công bố của AWS và Google, kịch bản tải thực tế và số liệu đo từ chatbot đang chạy. Các đơn giá AWS lấy theo bảng giá của trang giá AWS (vùng US East), cộng 15% dự phòng cho chênh lệch vùng Singapore; con số cuối cùng nên đối chiếu bằng [AWS Pricing Calculator](https://calculator.aws/).

**Bản ước tính trên AWS Pricing Calculator (vùng Singapore, kịch bản 10.000 câu hỏi/tháng):** [https://calculator.aws/#/estimate?id=d87e34b9f9a1e9f419182c4da7f6281aa720795f](https://calculator.aws/#/estimate?id=d87e34b9f9a1e9f419182c4da7f6281aa720795f)

**Giả định cho mỗi câu hỏi (đo từ chatbot đang chạy)**

| Thông số | Giá trị |
|---|---|
| Tin nhắn WebSocket | 2 (1 gửi vào, 1 trả về) |
| Phút kết nối WebSocket | 1 (tính thận trọng, mỗi câu một kết nối) |
| Lambda orchestrator | 1.024 MB, khoảng 2,5 giây, tức 2,5 GB-giây |
| Lambda khác | 2 lượt gọi, không đáng kể |
| DynamoDB | 5 lượt ghi, 6 lượt đọc (đã gồm bộ đếm giới hạn tần suất, lịch sử, giỏ hàng) |
| Gemini | Khoảng 2.500 token vào, 200 token ra, 30 token embedding |

**Đơn giá sử dụng**

| Dịch vụ | Đơn giá |
|---|---|
| Lambda | 0,20 USD mỗi 1 triệu lượt gọi; 0,0000166667 USD mỗi GB-giây; miễn phí 1 triệu lượt và 400.000 GB-giây mỗi tháng |
| API Gateway WebSocket | 1,00 USD mỗi 1 triệu tin nhắn; 0,25 USD mỗi 1 triệu phút kết nối |
| DynamoDB theo yêu cầu | 0,625 USD mỗi 1 triệu lượt ghi; 0,125 USD mỗi 1 triệu lượt đọc; miễn phí 25 GB lưu trữ |
| S3 | Dữ liệu vector dưới 1 MB nên chi phí lưu trữ gần như 0; yêu cầu kiểm tra phiên bản khoảng 8.640 lượt mỗi tháng |
| SSM Parameter Store (loại Standard), SNS email, 10 cảnh báo CloudWatch đầu tiên, EventBridge (vài chục sự kiện) | Trong mức miễn phí hoặc không đáng kể |
| Gemini (giả định trần, cần kiểm tra lại trên trang giá của Google) | 0,25 USD mỗi 1 triệu token vào; 1,50 USD mỗi 1 triệu token ra; embedding 0,15 USD mỗi 1 triệu token |

**Dự toán theo ba kịch bản (USD mỗi tháng)**

| Hạng mục | Demo: 1.000 câu | Thử nghiệm: 10.000 câu | Mở rộng: 100.000 câu |
|---|---|---|---|
| Lambda (trong mức miễn phí) | 0,00 | 0,00 | 0,00 |
| API Gateway WebSocket | 0,00 | 0,02 | 0,23 |
| DynamoDB | 0,00 | 0,04 | 0,39 |
| S3 | 0,01 | 0,01 | 0,01 |
| CloudWatch, SNS, SSM, EventBridge | 0,00 | 0,00 | 0,00 |
| Dự phòng chênh lệch vùng (+15%) | 0,00 | 0,01 | 0,09 |
| **Tổng phần AWS** | **khoảng 0,02** | **khoảng 0,09** | **khoảng 0,72** |
| Gemini (giá trần) | 0,93 | 9,30 | 92,95 |
| **Tổng cộng** | **khoảng 0,95** | **khoảng 9,38** | **khoảng 93,67** |

Các điểm cần lưu ý khi đọc bảng:

- Phần AWS gần như không đáng kể; chi phí chủ yếu là Gemini. Nếu dùng model rẻ hơn (giả định 0,10 và 0,40 USD cho 1 triệu token vào và ra) thì tổng cộng lần lượt khoảng 0,36, 3,43 và 34,17 USD.
- Giai đoạn demo chỉ dùng Gemini gói miễn phí thì chi phí Gemini bằng 0, nhưng gói này hay quá tải vào buổi tối.
- Miễn phí Lambda dùng chung cho cả tài khoản. Nếu các Lambda khác của nhóm đã dùng hết, chi phí Lambda của chatbot khoảng 0,05, 0,43 và 4,25 USD ở ba kịch bản.
- Miễn phí 12 tháng của API Gateway chỉ dành cho tài khoản AWS mới; bảng đã tính theo giá đầy đủ.

**So sánh với kiến trúc cũ.** OpenSearch Serverless tính 0,24 USD mỗi OCU-giờ, tối thiểu 2 OCU cho môi trường chạy thật (khoảng 350 USD mỗi tháng) hoặc 1 OCU cho môi trường thử nghiệm (khoảng 175 USD mỗi tháng), chưa kể NAT Gateway và mạng riêng. Bản hiện tại giảm chi phí cố định về gần 0, và ở mức 10.000 câu mỗi tháng vẫn nằm trong mục tiêu 5–10 USD của leader.

**Chiến lược tối ưu chi phí**

1. **Bỏ OpenSearch, đặt vector trên S3** (đã làm): tránh 175–350 USD mỗi tháng.
2. **Không dùng VPC và NAT Gateway** (đã làm): Lambda gọi thẳng Gemini qua Internet.
3. **Dùng SSM Parameter Store thay Secrets Manager** (đã làm): Secrets Manager tính phí theo từng bí mật mỗi tháng và theo lượt gọi API, còn SSM loại Standard miễn phí.
4. **Không gọi AI khi không cần** (đã làm): câu ngoài phạm vi trả lời có sẵn; xem giỏ hàng đọc thẳng DynamoDB, không qua Gemini.
5. **Giảm token mỗi câu hỏi**: chỉ gửi 3 sản phẩm và 3 đoạn tài liệu, giữ 6 tin gần nhất; đặt giới hạn độ dài câu hỏi 500 ký tự (đã làm).
6. **Đóng gói .zip bỏ ECR và bước build image** (đang làm).
7. **Giới hạn chi tiêu theo ngân sách.** Mức trần hiện tại 2.000 câu mỗi ngày tương ứng tối đa khoảng 18 USD mỗi ngày ở giá Gemini trần. Để bám mục tiêu 10 USD mỗi tháng, nên hạ mức trần xuống khoảng 350–400 câu mỗi ngày bằng biến môi trường `RL_GLOBAL_PER_DAY`, và bật AWS Budgets (hiện chưa bật vì thiếu quyền).
8. **Vòng đời dữ liệu.** Phiên hội thoại, bộ đếm giới hạn và giỏ hàng tự hết hạn bằng TTL của DynamoDB (thuộc tính `expires_at`). Đặt thời gian giữ log CloudWatch 14 đến 30 ngày và thêm S3 Lifecycle Policy tự xóa các bản đóng gói Lambda cũ sau 30 ngày (sẽ thêm trong Terraform).
9. **Dùng Gemini gói miễn phí khi thử nghiệm**, chỉ bật thanh toán trước buổi demo thật; cân nhắc model rẻ hơn khi tải tăng.

**Quy trình dọn dẹp tài nguyên sau khi thực hành**

Làm theo thứ tự dưới đây để không phát sinh chi phí ngoài ý muốn. Không xóa các bảng `Products`, `Users`, `Carts` vì chúng dùng chung với ứng dụng của nhóm.

1. Xóa các bản stack (hiện dùng CloudFormation):
   ```bash
   aws cloudformation delete-stack --stack-name chatbot-ops
   aws cloudformation delete-stack --stack-name chatbot-mailer
   aws cloudformation delete-stack --stack-name chatbot-v2
   ```
   Nếu đã chuyển sang Terraform thì chạy `terraform destroy` trong thư mục `terraform/`.
2. Làm rỗng rồi xóa bucket vector và bucket chứa gói Lambda:
   ```bash
   aws s3 rb s3://<tên-bucket-vector> --force
   ```
3. Xóa nhóm log (stack không tự xóa log do Lambda tạo):
   ```bash
   aws logs delete-log-group --log-group-name /aws/lambda/chat-receiver
   aws logs delete-log-group --log-group-name /aws/lambda/chat-orchestrator-v2
   aws logs delete-log-group --log-group-name /aws/lambda/chat-handover-mailer
   ```
4. Xóa tham số khóa Gemini và mật khẩu ứng dụng Gmail trong SSM: `aws ssm delete-parameter --name /chatbot/gemini_api_key` (và tham số mật khẩu Gmail).
5. Nếu còn dùng Docker: `aws ecr delete-repository --repository-name chat-orchestrator-v2 --force`.
6. Nếu đã tạo ngân sách: `aws budgets delete-budget`.
7. Kiểm tra không còn tài nguyên tính phí:
   ```bash
   aws ec2 describe-nat-gateways
   aws ec2 describe-addresses
   aws opensearchserverless list-collections
   ```
8. Thu hồi khóa Gemini trên Google AI Studio và tắt thanh toán Gemini nếu đã bật.

### 7. Rủi ro và phương án giảm thiểu

| Rủi ro | Mức ảnh hưởng | Khả năng xảy ra | Phương án giảm thiểu |
|---|---|---|---|
| Gemini quá tải hoặc hết hạn mức (đặc biệt gói miễn phí vào buổi tối) | Cao | Cao | Chuỗi 3 model dự phòng, ngắt mạch tạm thời, câu trả lời dự phòng chỉ trình bày kết quả tìm kiếm; cảnh báo khi Gemini lỗi nhiều; bật thanh toán trước demo |
| Chi phí vượt dự toán (Gemini tính theo token) | Trung bình | Trung bình | Hạ mức trần câu hỏi mỗi ngày, bật AWS Budgets, giới hạn token mỗi câu, theo dõi hằng tuần |
| Bot bịa giá hoặc tồn kho | Cao | Thấp | Chỉ trả lời từ dữ liệu tìm được; đọc lại giá và tồn kho từ DynamoDB mỗi câu hỏi; kiểm thử câu ngoài phạm vi |
| Bot tư vấn sai xe lắp vừa | Trung bình | Trung bình | Prompt bắt đối chiếu mục "Lắp vừa xe"; kiểm thử 12 câu theo xe; hướng phát triển: kiểm tra tương thích bằng mã trước khi thêm vào giỏ |
| Dữ liệu vector cũ so với dữ liệu thật | Trung bình | Trung bình | Giá và tồn kho luôn đọc mới; Khung 4 tự đồng bộ vector khi sản phẩm đổi |
| Giả mạo mã khách để dùng giỏ hàng của người khác | Cao | Thấp khi demo, cao khi chạy thật | Có sẵn Lambda authorizer (JWT Cognito) lấy mã khách từ token; bật trước khi chạy thật |
| Lạm dụng, gửi quá nhiều câu hỏi | Trung bình | Trung bình | Giới hạn theo phiên, theo kết nối và theo ngày; giới hạn độ dài câu hỏi |
| Lộ khóa API | Cao | Thấp | Khóa chỉ nằm trong SSM, không nằm trong code hay repo; xoay khóa khi nghi ngờ lộ |
| Thiếu quyền trên tài khoản AWS của nhóm (ví dụ Budgets, Streams) | Thấp | Trung bình | Dùng `preflight.sh` kiểm tra quyền trước khi triển khai; tạo từng phần độc lập |
| Mất email chuyển nhân viên hoặc gửi trùng | Trung bình | Thấp | Chỉ gửi 1 yêu cầu mỗi phiên trong 10 phút; có mã yêu cầu để truy vết; có cảnh báo lỗi Lambda |

**Phương án dự phòng.** Nếu Gemini hoặc AWS gặp sự cố kéo dài, website vẫn hiển thị khung chat và bot chuyển sang câu trả lời dự phòng kèm số hotline CSKH; mọi yêu cầu quan trọng đi theo kênh nhân viên.

### 8. Kết quả kỳ vọng

- Khách nhận tư vấn đúng xe, đúng giá, đúng tồn kho trong 2–3 giây, 24/7.
- Nhân viên CSKH chỉ xử lý những yêu cầu thật sự cần người, kèm đủ ngữ cảnh.
- Chi phí vận hành thấp, tăng theo mức sử dụng thật: khoảng 9,4 USD mỗi tháng ở 10.000 câu hỏi.
- Hạ tầng quản lý bằng mã (Terraform) cùng chuẩn với các phần khác của dự án, dễ dựng lại và dọn dẹp.
