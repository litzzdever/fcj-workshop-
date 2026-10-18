---
title: "Worklog Tuần 5"
date: 2026-10-14
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

* Tìm hiểu Amazon Cognito User Pool và cách hai website dùng chung một hệ thống danh tính.
* Thực hành AWS CDK: bootstrap môi trường, deploy stack và destroy sau lab.
* Cấu hình hai ứng dụng React trên Windows và kiểm thử đăng nhập, đăng xuất chéo website.
* Hoàn thành SageMaker Immersion Day từ feature engineering đến train, tune và deploy.
* Dựng luồng đơn hàng serverless với API Gateway, Lambda, SQS, SNS và DynamoDB.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 4 | - Học Cognito User Pool làm kho danh tính dùng chung cho ứng dụng web, và khác biệt giữa tài khoản dùng chung với session SSO <br> - Chuẩn bị repo `aws_cognito_cross_sites` trên Windows PowerShell, với Node.js, npm và AWS CLI profile `cognito-lab` <br> - Chạy `cdk bootstrap` cho tài khoản và region `ap-southeast-1`, rồi deploy `CognitoStack` từ thư mục `cdk` <br> - Ghi User Pool ID `ap-southeast-1_YJMBULPFi`, App Client ID `3fr8lkifba3p3in0oi1oomo22h`, region `ap-southeast-1` <br> - Tạo `.env.local` cho `app` và `app2` với `REACT_APP_USERPOOL_ID` và `REACT_APP_CLIENT_ID`; chạy Website 1 tại `localhost:3000` và Website 2 tại `localhost:3001` <br> - Đăng ký trên Website 1, rồi đăng nhập cả hai site bằng cùng username và password <br> - Sau khi đăng nhập Website 1, Website 2 tự nhận session, không hỏi lại mật khẩu. Sau SIGN OUT WEB1 và Ctrl+F5 trên Website 2, Website 2 yêu cầu đăng nhập lại <br> - Chạy `npx cdk destroy` trong `cdk`; stack trả `CognitoStack: destroyed`. Dừng hai app React. Giữ `CDKToolkit` cho lần dùng CDK sau <br> - Sửa lỗi PowerShell bằng cách ghi biến môi trường vào `.env.local`, chạy lại deploy khi gặp `ECONNRESET`, và ghi nhận cảnh báo Node.js v24 nhưng deploy và destroy vẫn xong | 14/10/2026 | 14/10/2026 | <https://000141.awsstudygroup.com/1-intro/> |
| 6 | - Hoàn thành workshop SageMaker Immersion Day: luồng ML từ feature engineering đến thuật toán tích hợp, training, tuning và deploy <br> - Dùng Data Wrangler để chuẩn bị dataset, phân tích, kiểm tra tương quan, chuyển đổi feature, ghi Feature Store và export sang S3 <br> - Train, tune và deploy model XGBoost, rồi gọi endpoint để dự đoán <br> - Clean up theo thứ tự: xóa endpoint, xóa app của từng user trong Studio, xóa user, rồi xóa Studio. Gõ `delete` để xác nhận | 16/10/2026 | 16/10/2026 | <https://000200.awsstudygroup.com/> |
| 7 | - Hoàn thành lab đơn hàng serverless. Checkout ghi hàng đợi và gửi thông báo; xử lý đơn là bước riêng <br> - Phân biệt SQS và SNS: SQS là hàng đợi giữa producer và consumer; SNS là pub/sub từ một topic tới nhiều subscriber. Lab dùng cả hai <br> - Deploy ứng dụng SAM: sửa `template.yaml`, chạy `sam validate`, `sam build` và `sam deploy --guided` thành stack `fcaj-book-shop` ở `us-east-1` <br> - Tạo bảng DynamoDB `OrdersTable` và bốn Lambda: `checkout_order`, `order_management`, `handle_order`, `delete_order` <br> - Luồng: `POST /books/order` đẩy message vào SQS `checkout-queue` và publish SNS topic `order-notice`. Admin xem đơn bằng `GET /books/order`. Handle ghi đơn vào DynamoDB và xóa khỏi queue. Delete xóa khỏi queue <br> - Build frontend React, đặt `APP_API_URL` bằng Invoke URL của API Gateway stage staging, upload `build/` lên S3 <br> - Test: tạo sách Java và Let's Go, thêm vào giỏ, Checkout, thấy message trong `checkout-queue` và email thông báo, Handle đổi trạng thái Processed, xác nhận item trong `OrdersTable`, rồi Delete đơn <br> - Clean up hosted zone Route 53 nếu có, chạy `sam delete` cho `fcaj-book-store` và `aws-sam-cli-managed-default`, làm trống bucket S3 liên quan, xóa queue `checkout-queue` và topic `order-notice` | 17/10/2026 | 17/10/2026 | <https://000083.awsstudygroup.com/> |

### Kết quả đạt được tuần 5:

* Deploy Cognito User Pool bằng AWS CDK và dùng cùng một tài khoản trên hai website React local.

* Kiểm thử hành vi chéo website: Website 2 nhận session của Website 1, và đăng xuất ở Website 1 buộc Website 2 hỏi lại mật khẩu sau khi tải lại cứng.

* Hoàn thành luồng SageMaker từ Data Wrangler và Feature Store đến endpoint XGBoost đã tune, rồi xóa Studio và endpoint.

* Dựng luồng đơn tách rời: checkout đi vào SQS và SNS, đơn chỉ được ghi DynamoDB khi admin xử lý.

* Thực hành hạ tầng dưới dạng code hai lần, CDK cho Cognito và SAM cho book shop, gồm destroy và `sam delete` sau lab.
