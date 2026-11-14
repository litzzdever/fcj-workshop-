---
title: "Worklog Tuần 9"
date: 2026-11-10
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:

* Tìm hiểu AWS Step Functions để điều phối nhiều bước xử lý thành một workflow có trạng thái.
* Thực hành xử lý lỗi trong workflow với Retry, Catch và các loại state chính.
* Tìm hiểu Amazon Kinesis Data Streams và thao tác cơ bản với stream bằng AWS CLI.
* Dựng luồng đưa dữ liệu streaming vào S3 bằng Amazon Data Firehose.
* Truy vấn dữ liệu trên S3 bằng SQL với Amazon Athena.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 3 | - Đọc phần Getting started của Step Functions: state machine, state, execution, định nghĩa bằng Amazon States Language (JSON) <br> - Phân biệt Standard workflow (chạy lâu, exactly-once) và Express workflow (khối lượng lớn, thời gian ngắn) <br> - Làm tutorial tạo state machine trong Workflow Studio: thêm state Pass, Choice và Task, chạy execution và xem input/output của từng bước trong Graph view <br> - Tìm hiểu IAM role mà state machine dùng để gọi các dịch vụ khác <br> - Clean up: xóa state machine và IAM role của lab | 10/11/2026 | 10/11/2026 | <https://docs.aws.amazon.com/step-functions/latest/dg/getting-started.html> |
| 4 | - Làm tutorial tạo state machine gọi Lambda: viết hàm Lambda, gắn vào state Task, truyền input và đọc output <br> - Làm tutorial xử lý lỗi: cấu hình `Retry` (số lần thử, `IntervalSeconds`, `BackoffRate`) và `Catch` để chuyển sang state xử lý lỗi <br> - Tìm hiểu các state Parallel và Map để chạy nhiều nhánh song song hoặc lặp qua danh sách phần tử <br> - Tìm hiểu cách tạo cùng workflow bằng CloudFormation để so sánh với cách tạo trên console <br> - Clean up: xóa state machine, hàm Lambda và log group | 11/11/2026 | 11/11/2026 | <https://docs.aws.amazon.com/step-functions/latest/dg/tutorial-handling-error-conditions.html> |
| 5 | - Đọc khái niệm Kinesis Data Streams: data stream, shard, partition key, sequence number, thời gian lưu dữ liệu <br> - Phân biệt chế độ capacity On-demand và Provisioned <br> - Làm tutorial thao tác cơ bản bằng AWS CLI: `create-stream`, `describe-stream-summary`, `put-record`, `get-shard-iterator`, `get-records` <br> - Hiểu vai trò producer và consumer, và cách partition key quyết định shard nhận bản ghi <br> - Clean up: `delete-stream` để dừng tính phí theo giờ của stream | 12/11/2026 | 12/11/2026 | <https://docs.aws.amazon.com/streams/latest/dev/fundamental-stream.html> |
| 6 | - Tìm hiểu Amazon Data Firehose (tên cũ Kinesis Data Firehose): dịch vụ đưa dữ liệu streaming tới đích mà không cần tự viết consumer <br> - Tạo Firehose stream với source Direct PUT hoặc Kinesis Data Streams và destination là bucket S3 <br> - Cấu hình buffering (kích thước và thời gian) và prefix theo thời gian cho object trên S3 <br> - Tìm hiểu tùy chọn biến đổi bản ghi bằng Lambda và chuyển đổi định dạng sang Parquet <br> - Gửi bản ghi thử và kiểm tra file xuất hiện trên S3 sau khi hết thời gian buffer <br> - Clean up: xóa Firehose stream, làm trống và xóa bucket S3 | 13/11/2026 | 13/11/2026 | <https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html> |
| 7 | - Đọc Get started của Amazon Athena: truy vấn trực tiếp dữ liệu trên S3 bằng SQL, không cần dựng server <br> - Cấu hình query result location trên S3 cho workgroup <br> - Làm tutorial: tạo database, tạo bảng trên dữ liệu mẫu bằng `CREATE EXTERNAL TABLE`, chạy truy vấn `SELECT` và xem kết quả <br> - Hiểu cách tính phí theo lượng dữ liệu được quét và lý do nên dùng partition, định dạng cột (Parquet) để giảm chi phí <br> - Nối với ngày trước: định hướng dùng Athena để truy vấn dữ liệu do Firehose ghi vào S3 <br> - Clean up: xóa bảng, database và kết quả truy vấn trên S3 | 14/11/2026 | 14/11/2026 | <https://docs.aws.amazon.com/athena/latest/ug/getting-started.html> |

### Kết quả đạt được tuần 9:

* Hiểu cách Step Functions điều phối nhiều bước thành workflow, và khác biệt giữa Standard workflow và Express workflow.

* Biết dùng Retry và Catch để workflow tự thử lại hoặc chuyển nhánh khi một bước bị lỗi, cùng các state Choice, Parallel và Map.

* Nắm các khái niệm shard, partition key, producer và consumer của Kinesis Data Streams, và thao tác stream bằng AWS CLI.

* Hiểu cách Firehose gom dữ liệu streaming theo buffer rồi ghi vào S3, kèm tùy chọn biến đổi bằng Lambda.

* Biết truy vấn dữ liệu trên S3 bằng Athena và cách giảm chi phí quét dữ liệu.

* Clean up tài nguyên sau mỗi bài thực hành để tránh phí theo giờ của stream và phí lưu trữ.
