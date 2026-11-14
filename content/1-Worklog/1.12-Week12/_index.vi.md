---
title: "Worklog Tuần 12"
date: 2026-12-01
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu tuần 12:

* Dựng luồng CI/CD trên AWS với AWS CodeBuild và AWS CodePipeline.
* Tìm hiểu mã hóa dữ liệu với AWS KMS và bảo vệ ứng dụng web với AWS WAF.
* Theo dõi tuân thủ cấu hình với AWS Config và tổng hợp phát hiện bảo mật với AWS Security Hub CSPM.
* Tìm hiểu quản trị nhiều tài khoản với AWS Organizations, IAM Identity Center và kiểm soát chi phí với AWS Budgets.
* Tổng kết 12 tuần học và kiểm tra, dọn dẹp toàn bộ tài nguyên còn lại trong tài khoản.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 3 | - Đọc Getting started with CodeBuild: build project, build environment, file `buildspec.yml` với các phase `install`, `pre_build`, `build`, `post_build` và `artifacts` <br> - Kết nối repo GitHub làm source, chọn image build managed và tạo service role cho CodeBuild <br> - Viết `buildspec.yml` cho một ứng dụng Node.js: cài dependency, chạy test, build và xuất artifact ra S3 <br> - Chạy build, đọc log từng phase trên console và CloudWatch Logs | 01/12/2026 | 01/12/2026 | <https://docs.aws.amazon.com/codebuild/latest/userguide/getting-started-overview.html> |
| 5 | - Đọc khái niệm CodePipeline: pipeline, stage, action, artifact, execution <br> - Làm tutorial tạo pipeline: stage Source (GitHub qua connection hoặc bucket S3), stage Build dùng project CodeBuild hôm trước, stage Deploy dùng Amazon S3 làm deployment provider cho website tĩnh <br> - Push commit mới và theo dõi pipeline tự chạy lại từ Source đến Deploy <br> - Tìm hiểu stage phê duyệt thủ công (manual approval) và biến ở mức pipeline <br> - Clean up: xóa pipeline, build project, bucket artifact và connection | 03/12/2026 | 03/12/2026 | <https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-s3deploy.html> |
| 7 | - Đọc AWS KMS cryptography essentials: KMS key, AWS managed key và customer managed key, symmetric và asymmetric <br> - Tạo symmetric customer managed key, đặt alias và đọc key policy mặc định <br> - Thực hành `aws kms encrypt` và `aws kms decrypt` với dữ liệu nhỏ <br> - Tìm hiểu envelope encryption: tạo data key bằng `generate-data-key`, dùng data key mã hóa dữ liệu lớn, KMS chỉ mã hóa data key <br> - Tìm hiểu cách S3, EBS, DynamoDB dùng KMS để mã hóa dữ liệu lưu trữ và chính sách tự xoay vòng key <br> - Clean up: lên lịch xóa key (thời gian chờ tối thiểu 7 ngày) | 05/12/2026 | 05/12/2026 | <https://docs.aws.amazon.com/kms/latest/developerguide/kms-cryptography.html> |
| 3 | - Đọc Getting started with AWS WAF: protection pack (web ACL), rule, rule group, AWS Managed Rules, hành động Allow, Block, Count <br> - Tạo web ACL gắn vào một API Gateway REST API hoặc Application Load Balancer dùng thử <br> - Thêm managed rule group (Core rule set, Known bad inputs) và một rate-based rule giới hạn số request theo IP <br> - Kiểm thử bằng request bình thường và request bị chặn, xem số liệu sampled requests và CloudWatch metrics <br> - Tìm hiểu AWS Shield Standard (mặc định miễn phí) và khác biệt với Shield Advanced <br> - Clean up: gỡ web ACL khỏi tài nguyên, xóa web ACL và tài nguyên thử | 08/12/2026 | 08/12/2026 | <https://docs.aws.amazon.com/waf/latest/developerguide/setup-existing-console.html> |
| 5 | - Đọc AWS Config: configuration recorder, configuration item, managed rule, custom rule, conformance pack <br> - Bật Config cho region đang dùng và thêm managed rule như kiểm tra bucket S3 chặn public access, EBS volume có mã hóa <br> - Xem trạng thái Compliant và Noncompliant, lịch sử thay đổi cấu hình của từng tài nguyên <br> - Bật AWS Security Hub CSPM, bật standard AWS Foundational Security Best Practices và xem security score, findings theo mức độ nghiêm trọng <br> - Hiểu Security Hub CSPM tổng hợp findings từ GuardDuty, Inspector, Config <br> - Clean up: tắt Security Hub CSPM, xóa rule và dừng configuration recorder để không phát sinh phí | 10/12/2026 | 10/12/2026 | <https://docs.aws.amazon.com/config/latest/developerguide/setting-up-aws-config-rules-with-console.html> |
| 7 | - Làm tutorial AWS Organizations: tạo organization, hiểu management account, member account, organizational unit (OU) <br> - Tìm hiểu service control policy (SCP) để giới hạn quyền tối đa của tài khoản trong OU, ví dụ chặn sử dụng ngoài region cho phép <br> - Đọc Enable IAM Identity Center: tạo user, group, permission set và gán quyền vào tài khoản, đăng nhập qua AWS access portal thay cho IAM user lâu dài <br> - Tạo cost budget hằng tháng trong AWS Budgets với ngưỡng cảnh báo gửi email khi chi phí thực tế hoặc dự báo vượt mức <br> - Clean up: xóa user và permission set thử nghiệm | 12/12/2026 | 12/12/2026 | <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_tutorials_basic.html> |
| 2 | - Tổng kết 12 tuần: hệ thống lại các nhóm dịch vụ đã học (compute, storage, database, serverless, container, analytics, AI/ML, DevOps, security, governance) <br> - Rà soát toàn bộ tài khoản ở các region đã dùng: EC2, EBS, Elastic IP, NAT Gateway, Load Balancer, RDS, Redshift, Kinesis, CloudFormation stack, bucket S3 <br> - Kiểm tra Billing và Cost Explorer để xác nhận không còn dịch vụ phát sinh chi phí ngoài dự kiến <br> - Cập nhật lại worklog và ghi chú những bài lab cần làm lại hoặc tìm hiểu sâu hơn | 14/12/2026 | 14/12/2026 | <https://docs.aws.amazon.com/cost-management/latest/userguide/create-cost-budget.html> |

### Kết quả đạt được tuần 12:

* Hiểu luồng CI/CD trên AWS: CodeBuild chạy build theo `buildspec.yml`, CodePipeline tự động hóa từ source đến deploy.

* Nắm cách KMS quản lý key, cơ chế envelope encryption và cách các dịch vụ lưu trữ dùng KMS để mã hóa dữ liệu.

* Biết dùng AWS WAF với managed rule và rate-based rule để bảo vệ API và ứng dụng web.

* Biết dùng AWS Config để theo dõi tuân thủ cấu hình và Security Hub CSPM để tổng hợp findings bảo mật.

* Hiểu mô hình quản trị nhiều tài khoản với Organizations, SCP, IAM Identity Center và cách đặt ngân sách cảnh báo với AWS Budgets.

* Hoàn thành tổng kết chương trình 12 tuần và dọn dẹp, kiểm tra chi phí toàn bộ tài khoản.
