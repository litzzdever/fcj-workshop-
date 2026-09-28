---
title: "Worklog Tuần 2"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

* Thực hành launch, kết nối và quản lý cả EC2 Windows Server và Amazon Linux instance.
* Nắm vững các nghiệp vụ quản trị EC2 nền tảng: đổi cấu hình instance, tạo/quản lý EBS Snapshot, tạo Custom AMI, khôi phục quyền truy cập khi mất key pair, chia sẻ AMI giữa các account.
* Triển khai thành công một ứng dụng web full-stack (Node.js CRUD) trên cả 2 nền tảng Linux (LAMP) và Windows (XAMPP).
* Thực hành Cost & Usage Governance với IAM: hạn chế Region, Instance Family, Instance Type, EBS Volume Type, quyền xóa theo IP và theo thời gian.
* Clean up toàn bộ tài nguyên lab để tránh phát sinh chi phí.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 3 | - Ôn lại Launch & Connect Windows Server 2025 Instance (3.1–3.2) <br> - Launch & Connect Amazon Linux Instance (4.1–4.2): xử lý lỗi "Connection timed out" bằng cách thêm inbound rule SSH vào Security Group <br> - Create and Manage EBS Snapshots (5.2): Snapshot theo Instance vs Volume <br> - Create Custom AMI (5.3): Stop instance trước khi tạo AMI để đảm bảo Sysprep xử lý xong <br> - Launch an Instance from a Custom AMI (5.4): tạo key pair mới `kp-windows2`, xác nhận Sysprep hoạt động đúng <br> - Recovering Access to Windows Instances qua SSM (5.5): tạo IAM Role (AmazonSSMManagedInstanceCore + AmazonSSMFullAccess), bật Default Host Management Configuration, cài module AWSPowerShell, chạy EC2Rescue tool, lấy lại Administrator password qua Parameter Store và RDP thành công | 22/09/2026 | 22/09/2026 | <https://000004.awsstudygroup.com/5-amazonec2basic/> |
| 4 | - Recovering Access to Linux Instances (5.6): tạo key pair mới, dùng cloud-init User data để inject public key mới vào `authorized_keys`, kết nối thành công bằng PuTTY <br> - Remote Desktop to EC2-Ubuntu (5.7): cài Desktop Xfce4 + xRDP, cấu hình inbound rule RDP (My IP), kết nối qua Remote Desktop Connection <br> - Amazon EBS Snapshots Archive – Optional (5.8): deregister AMI, archive snapshot (tiết kiệm ~75% chi phí), restore chế độ Permanent <br> - Share AMI – Optional (5.9): chia sẻ Custom AMI với AWS Account khác qua Account ID (chế độ Private) | 23/09/2026 | 23/09/2026 | <https://000004.awsstudygroup.com/5-amazonec2basic/> |
| 5 | - Tổng hợp nội dung và viết báo cáo worklog Tuần 2 (phần 3–5) | 24/09/2026 | 24/09/2026 | — |
| 6 | - Install LAMP Web Server on Amazon Linux 2023 (6.1): điều chỉnh lệnh `dnf` cho AL2023 (thay vì `amazon-linux-extras`), bảo mật MariaDB bằng `mysql_secure_installation`, cài phpMyAdmin, tạo database `awsuser` và bảng `user` <br> - Install Node.js on Amazon Linux 2023 (6.2): cài qua nvm, xác nhận Security Group mở đủ port 22/80/443/5000 <br> - Deploy ứng dụng AWS FCJ Management trên Linux (6.3): clone repo, cài dependencies Express stack, cấu hình `.env`, thêm 18 user mẫu qua phpMyAdmin, thực hành đầy đủ CRUD | 25/09/2026 | 25/09/2026 | <https://000004.awsstudygroup.com/6-awsfcjmanagement-linux/> |
| 7 | - Install XAMPP on Windows Server 2025 (7.1): bật Apache + MySQL, tạo database `awsuser` và bảng `user` giống hệt bản Linux <br> - Install Node.js + Git + VS Code trên Windows (7.2) <br> - Deploy ứng dụng AWS User Management trên Windows (7.3): xử lý cảnh báo lỗ hổng npm high severity bằng cách cập nhật nodemon, thêm 18 user mẫu, thực hành đầy đủ Add/View/Edit/Delete qua giao diện web | 26/09/2026 | 26/09/2026 | <https://000004.awsstudygroup.com/7-awsfcjmanagement-windows/> |
| CN | - **Cost & Usage Governance with IAM** (8.1–8.6): <br>&emsp; + 8.1 Restrict by Region: policy `RegionRestrict` (chỉ `ap-southeast-1`), tạo group `CostTest` và user `TestUser` <br>&emsp; + 8.2 Restrict by Instance Family: policy `EC2_FamilyRestrict` (chỉ `t3.*`, `t4g.*`, `m5.*`) <br>&emsp; + 8.3 Restrict by Instance Type: policy `EC2_InstanceTypeRestrict` (chỉ `t3.small`, `t3.large`) <br>&emsp; + 8.4 Restrict EBS Volume Type: chỉ cho phép `gp3` <br>&emsp; + 8.5 Restrict Terminate theo Source IP: policy `IP_Restrict` <br>&emsp; + 8.6 Restrict Terminate theo khung thời gian: policy `EC2_TimeRestrict` (DateGreaterThan / DateLessThan) <br> - **Clean up resources** (9): terminate toàn bộ EC2 instances, deregister AMI, xóa Snapshots / Security Groups / Key Pairs / VPC, xóa IAM User / Group / Policies / Roles | 27/09/2026 | 27/09/2026 | <https://000004.awsstudygroup.com/8-costusagegovernance/> <br> <https://000004.awsstudygroup.com/9-cleanup/> |

### Kết quả đạt được tuần 2:

* Thực hành trọn vẹn vòng đời quản trị nâng cao của EC2 instance: Launch → Snapshot → Tạo Custom AMI → Launch lại từ AMI → Khôi phục quyền truy cập khi mất Key pair.

* Nắm vững sự khác biệt và cách áp dụng 2 loại EBS Snapshot: theo Volume (backup 1 ổ cụ thể) và theo Instance (backup đồng thời toàn bộ volume gắn với instance).

* Hiểu quy trình tạo Custom AMI đúng chuẩn cho Windows: cần Stop instance hoàn toàn trước khi tạo để đảm bảo Sysprep đã cấu hình trước đó xử lý đầy đủ.

* Debug thành công chuỗi lỗi thực tế khi khôi phục quyền truy cập Windows qua Systems Manager:
  * Thiếu IAM Role / IAM Role chưa đủ quyền (AmazonSSMManagedInstanceCore → cần bổ sung AmazonSSMFullAccess)
  * SSM Agent chưa đăng ký (kiểm tra qua Fleet Manager, bật Default Host Management Configuration)
  * Lỗi cài đặt module AWSPowerShell do thiếu NuGet provider — xử lý bằng `Install-PackageProvider`
  * Lỗi thiếu quyền `ssm:PutParameter` khi chạy EC2Rescue tool

* Hiểu và thực hành cơ chế khôi phục quyền truy cập cho Linux instance bằng cách chỉnh sửa User data với cloud-init, tiêm public key mới vào `authorized_keys` mà không cần truy cập trực tiếp vào instance trước đó.

* Cài đặt thành công giao diện Desktop (Xfce4) và xRDP trên Ubuntu, cho phép truy cập EC2 Linux instance bằng Remote Desktop thay vì chỉ dùng Terminal.

* Hiểu về EBS Snapshots Archive: cơ chế lưu trữ dài hạn với chi phí thấp hơn (~75%), điều kiện bắt buộc phải deregister AMI liên quan trước khi archive, và thời gian lưu trữ tối thiểu 90 ngày.

* Nắm được cách Share AMI giữa nhiều AWS Account khác nhau thông qua Account ID, phục vụ việc triển khai đồng bộ hạ tầng across nhiều môi trường/account.

* Triển khai thành công một LAMP stack hoàn chỉnh (Apache, MariaDB, PHP) trên Amazon Linux 2023, tự phát hiện và điều chỉnh được sự khác biệt lệnh cài đặt giữa Amazon Linux 2 (`yum` / `amazon-linux-extras`) và Amazon Linux 2023 (`dnf`).

* Deploy thành công cùng một ứng dụng web full-stack (Node.js + Express + Express-Handlebars + MySQL) trên **CẢ HAI** nền tảng Linux (LAMP) và Windows (XAMPP), qua đó so sánh và hiểu rõ khác biệt công cụ giữa 2 hệ điều hành:
  * Linux: `dnf` / `systemctl`, chỉnh sửa file qua `vi`, cài Node.js qua nvm
  * Windows: trình cài đặt GUI của XAMPP, Services qua XAMPP Control Panel, chỉnh sửa code qua Visual Studio Code, cài Node.js/Git qua installer

* Cấu hình bảo mật cơ bản cho MariaDB và quản lý database trực quan qua phpMyAdmin trên cả 2 nền tảng.

* Xử lý được cảnh báo lỗ hổng bảo mật (npm audit) trong quá trình cài đặt dependencies bằng cách cập nhật package lên bản mới nhất.

* Kết nối trực tiếp kiến thức Node.js/full-stack đang học ở trường với hạ tầng AWS thực tế, có khả năng triển khai ứng dụng đa nền tảng (cross-platform deployment).

* Củng cố kỹ năng đọc và debug log lỗi kỹ thuật tiếng Anh (IAM permission error, PowerShell verbose log, npm audit) để tự xác định nguyên nhân và hướng khắc phục.

* **Cost & Usage Governance với IAM:**
  * Nắm vững cách dùng IAM Policy Condition Keys để kiểm soát chi phí: `aws:RequestedRegion`, `ec2:InstanceType`, `ec2:VolumeType`, `aws:SourceIp`, `aws:CurrentTime`.
  * Thực hành nguyên tắc least-privilege: bắt đầu từ policy rộng (Region) → thu hẹp dần theo Family → Instance Type cụ thể → kết hợp Volume Type.
  * Hiểu cơ chế Deny có điều kiện (`NotIpAddress`, `DateGreaterThan` / `DateLessThan`) để bảo vệ tài nguyên khỏi thao tác xóa ngoài ý muốn theo IP hoặc khung thời gian nhạy cảm.
  * Áp dụng IAM Group + Customer managed policy để quản lý quyền tập trung, dễ attach/detach và audit.
  * Thực hiện Clean up đầy đủ: Terminate instances, Deregister AMI, Delete Snapshots / Security Groups / Key pairs / VPC và dọn dẹp IAM User / Group / Policy / Role — tránh chi phí phát sinh sau lab.
