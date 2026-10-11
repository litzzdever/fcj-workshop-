---
title: "Worklog Tuần 4"
date: 2026-10-07
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Tìm hiểu Amazon DynamoDB: thành phần cốt lõi, primary key, secondary index, read consistency và chế độ capacity.
* Cấu hình AWS CLI trên máy Windows và dùng credential quyền tối thiểu, không dùng root.
* Thực hành vòng đời dữ liệu DynamoDB bằng boto3: tạo bảng, ghi, đọc, cập nhật, xóa, nạp mẫu, Query, Scan, xóa bảng.
* Xử lý lỗi xác minh tài khoản mới trên CloudFront, Bedrock và CloudShell qua AWS Support.
* Dựng lịch dừng và khởi động EC2 bằng Lambda, EventBridge và Slack webhook để giảm chi phí instance chạy không.
* Hoàn thành Secrets Manager với RDS và Fargate, workshop CloudWatch, và API tài liệu serverless DynamoDB với Lambda.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 4 | - Học thành phần DynamoDB: Table, Item, Attribute, partition key, sort key, GSI và LSI, kiểu Scalar, Set, Document <br> - Phân biệt eventually consistent, strongly consistent và transactional read; On-demand với Provisioned <br> - Xem backup, PITR 35 ngày, TTL, mã hóa at rest và tính năng 2026 như vector search, global tables <br> - CloudShell báo account verification in progress, cùng nhóm lỗi với CloudFront và Bedrock. Mở support case, dùng AWS CLI trên máy thay CloudShell <br> - Cấu hình AWS CLI v2 trên Windows với Python 3.13 và boto3, region `us-east-1` <br> - Tạo bảng `Movies` (`year` number, `title` string, `PAY_PER_REQUEST`); put, get, update, delete item; nạp 4 phim mẫu bằng `Decimal`; Query theo năm và Scan theo rating; xóa bảng | 07/10/2026 | 07/10/2026 | <https://000060.awsstudygroup.com/1-introduce/1.1-corecomponentsofamazondynamodb/> <br> <https://000060.awsstudygroup.com/3-gettingstartedwithawssdk/> |
| 5 | - Đọc lab giảm chi phí EC2: Lambda và EventBridge dừng, khởi động instance có tag, rồi gửi trạng thái sang Slack <br> - Tạo VPC, security group và instance `lambda-lab-instance`; gắn tag `environment_auto=true` <br> - Tạo role `dc-common-lambda-role` với quyền tối thiểu để describe, stop, start và ghi log <br> - Viết `dc-common-lambda-auto-stop` và hàm start bằng Python 3.14, x86_64; EventBridge schedule gọi hai hàm <br> - Test stop và start trên EC2 console, nhận tin Slack; webhook URL để ngoài code chia sẻ <br> - Clean up rule EventBridge, Lambda, role, instance, security group và VPC | 08/10/2026 | 08/10/2026 | <https://000022.awsstudygroup.com/1-introduce/> |
| 6 | - Hoàn thành lab level 300: Secrets Manager với Amazon RDS và AWS Fargate. Mật khẩu không nằm trong code hay biến môi trường <br> - Mạng lab: VPC 2 subnet, bastion Amazon Linux 2, RDS private, credential trong Secrets Manager <br> - Truy cập RDS qua secret, rồi chạy secret rotation và kiểm tra bằng shell script <br> - Build image, push ECR, chạy ECS Fargate; container đọc secret và nối RDS private <br> - Gắn lab với CSF Prevention và CAF Preventative | 09/10/2026 | 09/10/2026 | <https://000096.awsstudygroup.com/> |
| 7 | - Hoàn thành workshop CloudWatch: metric, log, alarm và dashboard trên một nền tảng <br> - Tạo CloudFormation stack `AwsWorkshopStack` từ `template.yml` <br> - Xem metric và dimension, rồi metric từ log `/var/log/messages - ERROR` <br> - Tạo alarm, period 1 phút, ngưỡng Greater hơn 10, kèm SNS topic gửi email <br> - Thêm alarm vào dashboard và clean up stack, alarm, topic, dashboard | 10/10/2026 | 10/10/2026 | <https://000036.awsstudygroup.com/1-introduce/> |
| CN | - Hoàn thành lab serverless: Lambda đọc và ghi DynamoDB cho hệ thống tài liệu <br> - Tạo bảng `Documents`, partition key `user_id`, sort key `file`, capacity on-demand <br> - Tạo `list_documents`, `upload_document` và `delete_document` <br> - Nạp dữ liệu mẫu bằng `aws dynamodb batch-write-item` và `documentData.json` <br> - Test list user `abcd1234`, upload `aws_serverless.doc` và delete; cả ba trả succeeded, bảng khớp kết quả <br> - Xem sơ đồ DMS đầy đủ: Amplify, Cognito, S3, DynamoDB, OpenSearch, CloudFront, X-Ray, CloudWatch; clean up bảng và hàm | 11/10/2026 | 11/10/2026 | <https://000133.awsstudygroup.com/> |

### Kết quả đạt được tuần 4:

* Giải thích được thành phần DynamoDB, khác biệt Query và Scan, On-demand và Provisioned.

* Cấu hình AWS CLI trên Windows và hoàn thành vòng đời bảng Movies bằng boto3, gồm `Decimal` cho số.

* Thay CloudShell bị chặn bằng CLI local, và gộp xác minh CloudFront, Bedrock, CloudShell vào một support case.

* Dựng và test lịch dừng, khởi động EC2 theo tag bằng Lambda, EventBridge và Slack, rồi xóa tài nguyên lab.

* Dùng Secrets Manager cho credential RDS, có rotation, và nối task Fargate vào database private.

* Tạo alarm CloudWatch từ log metric và đưa lên dashboard.

* Mở DynamoDB qua ba Lambda list, upload và delete, test event đều đạt.
