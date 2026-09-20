---
title: "Worklog Tuần 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu tuần 1:

* Làm quen với AWS Management Console, tạo và cấu hình tài khoản AWS.
* Hiểu dịch vụ Amazon EC2 và các dịch vụ liên quan, cách dùng AWS Console để quản lý tài nguyên.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 4 | - Tạo tài khoản AWS Management Console (Root user) <br> - Tạo tài khoản AWS Builder ID <br> - Tìm hiểu sự khác biệt giữa Root user, IAM user và AWS Builder ID <br> - Đăng nhập vào AWS Management Console, làm quen giao diện Console home page <br> - Tìm hiểu về MFA (Multi-Factor Authentication) cho root user: <br>&emsp; + Passkey / Security key <br>&emsp; + Authenticator app <br> - Đổi Region phù hợp gần Việt Nam (Singapore / Jakarta) | 16/09/2026 | 16/09/2026 | <https://docs.aws.amazon.com/> |
| 5 | - Tìm hiểu Amazon EC2: <br>&emsp; + Khái niệm instance, instance type <br>&emsp; + AMI (Amazon Machine Image) <br>&emsp; + Các tính năng cốt lõi: EBS volume, Key pair, Security group <br> - Tìm hiểu Amazon EC2 Auto Scaling: <br>&emsp; + Auto Scaling group, scaling policies <br>&emsp; + Ứng dụng thực tế (e-commerce, app gọi xe, batch job...) <br> - Tìm hiểu AWS Backup: <br>&emsp; + Centralized backup, backup plan <br>&emsp; + Lifecycle policy, Backup Vault Lock <br> - Tìm hiểu Amazon CloudWatch: <br>&emsp; + Metrics, Alarms, Dashboards <br>&emsp; + Application Performance Monitoring (APM), log management <br> - Tìm hiểu các dịch vụ liên quan đến EC2 (Load Balancing, GuardDuty, Systems Manager...) <br> - Tìm hiểu các mô hình giá EC2 (Free Tier, On-Demand, Savings Plans, Reserved Instances, Spot Instances, Dedicated Host...) | 17/09/2026 | 17/09/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| 7 | - **Thực hành Tutorial** "Launch my first EC2 instance" (Task 1-4): <br>&emsp; + Task 1: Launch instance <br>&emsp; + Task 2: Find instance <br>&emsp; + Task 3: View instance configuration <br>&emsp; + Task 4: Terminate instance <br> - Đọc tài liệu Reference for Amazon EC2 instance configuration parameters <br> - Đọc tài liệu Launch an EC2 instance using the launch instance wizard (Quick Launch vs Defined Parameters) <br> - Tìm hiểu Launch EC2 instances using a launch template | 19/09/2026 | 19/09/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> |
| CN | - **Thực hành Tutorial** "Launch, Connect, and Stop your Instance" (Task 1-9): <br>&emsp; + Task 1-2: Tìm hiểu thành phần EC2 (AMI, Instance type, Key pair, Network, Security group, EBS) và sơ đồ kỹ thuật <br>&emsp; + Task 3: Tạo Key pair (ED25519, định dạng .pem) <br>&emsp; + Task 4: Launch test instance có gắn Key pair <br>&emsp; + Task 5-6: Tìm và xem cấu hình chi tiết instance <br>&emsp; + Task 7-8: Kết nối SSH từ Windows PowerShell (xử lý lỗi permission private key bằng lệnh `icacls`), thực hành lệnh Linux cơ bản (`whoami`, `pwd`, `cat`) <br>&emsp; + Task 9: Stop instance <br> - Tìm hiểu và thực hành tạo VPC tùy chỉnh (Multi-AZ, Public/Private subnet) cho Linux và Windows instance <br> - Tạo Security Group `Linux-SG` (7 inbound rules) và tìm hiểu Security Group `Windows-SG` (8 inbound rules) <br> - Viết báo cáo worklog tuần 1 | 20/09/2026 | 20/09/2026 | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/> <br> <https://000004.awsstudygroup.com/2-prerequiste/> |

### Kết quả đạt được tuần 1:

* Hiểu được khái niệm Amazon EC2 và vai trò trong hệ sinh thái AWS Compute.

* Nắm được các thành phần cốt lõi cấu thành một EC2 instance:
  * AMI (Amazon Machine Image)
  * Instance type
  * Key pair
  * VPC / Subnet
  * Security group
  * EBS volume

* Đã tạo và cấu hình thành công tài khoản AWS (Root user) và AWS Builder ID, phân biệt được mục đích sử dụng của từng loại.

* Thực hành trọn vẹn vòng đời của một EC2 instance: Launch → Find → View configuration → Connect (SSH) → Stop / Terminate.

* Tạo và quản lý Key pair (ED25519, định dạng .pem) phục vụ xác thực SSH.

* Kết nối SSH thành công vào EC2 Linux instance từ Windows PowerShell; tự xử lý được lỗi permission của file private key (.pem) bằng lệnh `icacls`.

* Làm quen với một số lệnh Linux cơ bản trên server (`whoami`, `pwd`, `cat`...).

* Tìm hiểu và thực hành tạo VPC tùy chỉnh với cấu trúc Multi-AZ (2 Availability Zone, 2 Public subnet, 2 Private subnet) cho cả Linux và Windows instance.

* Tạo Security Group tùy chỉnh với nhiều inbound rule (SSH, ICMP, HTTP, HTTPS, MySQL, Custom TCP); hiểu rõ và xử lý đúng sự khác biệt giữa Source IPv4 và IPv6.

* Tìm hiểu về EC2 Auto Scaling, AWS Backup, Amazon CloudWatch và vai trò hỗ trợ vận hành EC2 trong thực tế.

* Nắm được các dịch vụ liên quan đến EC2 (Elastic Load Balancing, GuardDuty, Systems Manager...) và các mô hình giá (On-Demand, Spot, Reserved Instances, Savings Plans...).

* Hiểu được khái niệm Launch Template và ứng dụng trong Auto Scaling Group / EC2 Fleet.

* ...
