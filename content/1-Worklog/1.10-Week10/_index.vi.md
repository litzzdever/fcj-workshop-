---
title: "Worklog Tuần 10"
date: 2026-11-17
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10:

* Tìm hiểu AWS Glue: Data Catalog, crawler và ETL job để chuẩn bị dữ liệu cho phân tích.
* Thực hành ETL trực quan bằng AWS Glue Studio và đọc script Glue for Spark.
* Tìm hiểu AWS Lake Formation để quản lý quyền truy cập data lake ở mức database, bảng và cột.
* Làm quen kho dữ liệu Amazon Redshift Serverless và truy vấn bằng query editor v2.
* Dựng dashboard trực quan hóa dữ liệu với Amazon Quick Sight.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 3 | - Đọc AWS Glue concepts: Data Catalog, database, table, crawler, classifier, connection, job, trigger <br> - Hiểu vai trò Data Catalog là kho metadata dùng chung cho Athena, Redshift Spectrum và EMR <br> - Tạo IAM role cho Glue với quyền đọc bucket S3 chứa dữ liệu mẫu <br> - Tạo crawler quét dữ liệu CSV/JSON trên S3, chạy crawler và xem schema bảng được tạo trong Data Catalog <br> - Truy vấn bảng vừa tạo bằng Athena để kiểm tra schema | 17/11/2026 | 17/11/2026 | <https://docs.aws.amazon.com/glue/latest/dg/components-key-concepts.html> |
| 4 | - Làm visual ETL job trong AWS Glue Studio: source từ Data Catalog, transform (đổi kiểu, bỏ cột, lọc dòng), target ghi Parquet ra S3 <br> - Đọc tutorial viết script Glue for Spark: `GlueContext`, `DynamicFrame`, `ApplyMapping` <br> - Tìm hiểu DPU, worker type và job bookmark để chỉ xử lý dữ liệu mới <br> - Chạy crawler lần hai trên output Parquet và so sánh dung lượng quét khi truy vấn bằng Athena <br> - Clean up: xóa job, crawler, database trong Data Catalog và dữ liệu output trên S3 | 18/11/2026 | 18/11/2026 | <https://docs.aws.amazon.com/glue/latest/dg/aws-glue-programming-intro-tutorial.html> |
| 5 | - Đọc phần Set up AWS Lake Formation: data lake administrator, đăng ký vị trí S3 làm data lake location <br> - Hiểu mô hình quyền của Lake Formation kết hợp với IAM, và cài đặt mặc định dùng IAM access control cho bảng mới <br> - Thực hành cấp quyền `SELECT` ở mức database, bảng và cột cho một IAM user khác, rồi kiểm tra bằng Athena <br> - Đọc tutorial tag-based access control (LF-Tags) và row-level security <br> - Clean up: thu hồi quyền, hủy đăng ký data lake location | 19/11/2026 | 19/11/2026 | <https://docs.aws.amazon.com/lake-formation/latest/dg/initial-lf-config.html> |
| 6 | - Đọc Get started with Amazon Redshift Serverless: namespace (dữ liệu) và workgroup (tài nguyên tính toán) <br> - Tạo workgroup với cấu hình mặc định, hiểu đơn vị RPU và cơ chế tính phí theo thời gian truy vấn <br> - Mở query editor v2, nạp dữ liệu mẫu có sẵn và chạy các truy vấn SQL mẫu <br> - Tìm hiểu lệnh `COPY` để nạp dữ liệu từ S3 và Redshift Spectrum để truy vấn bảng trong Data Catalog <br> - So sánh Redshift (data warehouse) với Athena (query trực tiếp trên S3) <br> - Clean up: xóa workgroup và namespace để không phát sinh phí | 20/11/2026 | 20/11/2026 | <https://docs.aws.amazon.com/redshift/latest/gsg/new-user-serverless.html> |
| 7 | - Đọc Getting started với Amazon Quick Sight (nay thuộc Amazon Quick Suite): dataset, analysis, visual, dashboard <br> - Kiểm tra gói đăng ký và chi phí trước khi đăng ký Quick Sight <br> - Làm tutorial tạo analysis từ dữ liệu mẫu: thêm visual dạng cột, đường và bảng, thêm filter <br> - Publish analysis thành dashboard và tìm hiểu cách chia sẻ cho người dùng khác <br> - Tìm hiểu cách kết nối Quick Sight với Athena làm nguồn dữ liệu <br> - Clean up: xóa dataset, analysis, dashboard và hủy đăng ký nếu không dùng tiếp | 21/11/2026 | 21/11/2026 | <https://docs.aws.amazon.com/quicksight/latest/user/getting-started.html> |

### Kết quả đạt được tuần 10:

* Hiểu cách Glue crawler tự suy ra schema và đưa metadata vào Data Catalog dùng chung cho các dịch vụ phân tích.

* Biết dựng ETL job trực quan trong Glue Studio để làm sạch dữ liệu và chuyển sang Parquet.

* Hiểu cách Lake Formation phân quyền data lake ở mức database, bảng, cột và theo LF-Tags.

* Nắm kiến trúc Redshift Serverless (namespace và workgroup) và biết truy vấn bằng query editor v2.

* Biết dựng analysis và publish dashboard trên Amazon Quick Sight.

* Nối được luồng phân tích: S3 → Glue → Athena hoặc Redshift → Quick Sight.

* Clean up các dịch vụ tính phí theo thời gian chạy như Glue job, Redshift workgroup và gói Quick Sight.
