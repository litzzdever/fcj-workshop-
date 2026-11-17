---
title: "Blog 2"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Amazon Athena kết hợp AWS Glue Data Catalog – Tìm hiểu cách truy vấn dữ liệu trong S3 bằng SQL mà không cần máy chủ

*Đăng ngày 11/11/2026 trên nhóm AWS Study Group – [Xem bài viết gốc](https://www.facebook.com/photo/?fbid=4673778119572839&set=gm.2333754607389498&idorvanity=660548818043427)*

Trong nhiều hệ thống, dữ liệu thô như log hay tệp CSV thường được lưu trong Amazon S3. S3 chỉ lưu tệp: nó không biết tệp có những cột nào và không thể trả lời trực tiếp những câu hỏi như “tổng doanh thu theo từng nhóm sản phẩm”. Cách quen thuộc là nạp dữ liệu vào một cơ sở dữ liệu, nhưng việc đó tốn công dựng, vận hành và đồng bộ dữ liệu.

Khi tìm hiểu về AWS và các giải pháp phân tích dữ liệu, mình bắt đầu quan tâm đến cách hỏi dữ liệu ngay trên S3 mà không phải di chuyển nó. Hai dịch vụ xuất hiện trong cách làm này là Amazon Athena và AWS Glue Data Catalog.

Điều mình thấy thú vị là hai dịch vụ này không thực hiện cùng một nhiệm vụ. Glue Data Catalog chịu trách nhiệm lưu metadata mô tả dữ liệu (cột nào, kiểu gì, nằm ở đâu), còn Athena đảm nhiệm việc thực thi các câu truy vấn SQL dựa trên metadata đó.

![Amazon Athena đọc CSV trên S3, dùng metadata trong AWS Glue Data Catalog và ghi ra Parquet chia partition theo tháng](/images/3-BlogsPosted/3.2-Blog2/athena-glue-architecture.png)

### AWS Glue Data Catalog – Nơi lưu metadata cho dữ liệu trong S3

AWS Glue là dịch vụ tích hợp dữ liệu của AWS. Theo trang so sánh của AWS, Glue gồm nhiều thành phần như Data Catalog, ETL, crawler..., nhưng trong bài này mình chỉ tập trung vào Data Catalog, thành phần mà Athena sử dụng trực tiếp.

Data Catalog lưu metadata mô tả dữ liệu thay vì lưu chính dữ liệu. Sau khi tạo bảng, mình dùng AWS CLI (lệnh `get-table` và `get-partitions`) để xem bản ghi trong Catalog và thấy một số thành phần chính:

- **Columns:** tên và kiểu dữ liệu của các cột trong tệp.
- **PartitionKeys:** các cột partition, nằm riêng với Columns vì giá trị của chúng suy ra từ tên thư mục (ví dụ `month=2026-03`) chứ không lưu trong tệp.
- **Location:** đường dẫn S3 nơi chứa dữ liệu.
- **SerDe và InputFormat:** cách đọc tệp. Bảng CSV của mình dùng LazySimpleSerDe, còn bảng Parquet dùng Parquet SerDe.
- **TableType:** `EXTERNAL_TABLE`, nghĩa là bảng chỉ mô tả dữ liệu có sẵn nên xóa bảng thì tệp trong S3 vẫn còn.

Về chi phí, theo trang giá của Glue, 1 triệu object metadata đầu tiên và 1 triệu request đầu tiên mỗi tháng trong Data Catalog được miễn phí.

### Amazon Athena – Truy vấn SQL trực tiếp trên dữ liệu trong S3

Amazon Athena là dịch vụ cho phép dùng SQL chuẩn để truy vấn dữ liệu trong S3. Dịch vụ này là serverless nên không có máy chủ để tạo hay quản lý, và bảng trong Athena chỉ là lớp mô tả đặt lên tệp có sẵn (schema-on-read).

Điểm mình thấy đáng chú ý nhất là cách tính phí. Theo trang giá của Athena, truy vấn SQL tính 5 USD cho mỗi TB dữ liệu quét, ngoài ra còn phí S3 theo mức tiêu chuẩn (lưu trữ, request, truyền dữ liệu, kể cả kết quả truy vấn) và phí Glue Data Catalog theo mức tiêu chuẩn. Trang giá có ví dụ: một truy vấn quét 3 TB tốn 15 USD, nếu dữ liệu được nén gzip với tỷ lệ 3:1 thì còn 5 USD, và nếu dùng thêm định dạng cột Parquet thì còn 1,25 USD.

Điều này khiến mình quan tâm đến định dạng tệp và cách tổ chức dữ liệu, vì chúng ảnh hưởng trực tiếp đến hóa đơn.

### Khi kết hợp Athena và Glue Data Catalog, quy trình hoạt động như thế nào?

Để hiểu rõ hơn, mình thử một kịch bản với dữ liệu bán hàng mô phỏng (sinh bằng Python) gồm 1.000.000 dòng cho 6 tháng đầu năm 2026, với 8 cột: `order_id`, `order_date`, `month`, `category`, `region`, `quantity`, `unit_price`, `revenue`. Tệp CSV nặng 54,0 MiB.

Đầu tiên, mình upload CSV lên S3 và tạo bảng bằng câu lệnh `CREATE EXTERNAL TABLE` trong Athena. Khi đó Athena ghi metadata của bảng vào Glue Data Catalog, và bảng trỏ vào thư mục trên S3. Sau đó mình chạy ba truy vấn:

- **Truy vấn A:** tổng doanh thu theo nhóm sản phẩm (`SUM`, `GROUP BY`).
- **Truy vấn B:** đếm đơn theo vùng, lọc tháng 3.
- **Truy vấn C:** đếm tổng số dòng (`COUNT`).

Tiếp theo, mình chuyển dữ liệu sang Parquet (nén Snappy), chia partition theo tháng, bằng một câu CTAS ngay trong Athena, rồi chạy lại đúng ba truy vấn trên bảng mới:

```sql
CREATE TABLE lit_demo_db.sales_parquet
WITH (
  format = 'PARQUET',
  write_compression = 'SNAPPY',
  external_location = 's3://<bucket>/parquet/sales/',
  partitioned_by = ARRAY['month']
) AS
SELECT order_id, order_date, category, region,
       quantity, unit_price, revenue, month
FROM lit_demo_db.sales;
```

Cột partition (`month`) phải đứng cuối trong câu `SELECT`.

Kết quả của hai bảng khớp hoàn toàn (cùng tổng doanh thu theo nhóm sản phẩm, cùng số đơn theo vùng, cùng 1.000.000 dòng), chỉ khác lượng dữ liệu mà Athena phải quét:

- **Dung lượng trên S3:** CSV 54,0 MiB, Parquet 13,2 MiB (6 tệp), giảm khoảng 4,1 lần.
- **Truy vấn A:** quét 54,0 MiB với CSV và 4,97 MiB với Parquet, giảm khoảng 10,9 lần. Parquet lưu theo cột nên chỉ đọc hai cột cần thiết (`category`, `revenue`).
- **Truy vấn B:** quét 54,0 MiB với CSV và 42,4 KiB với Parquet, giảm khoảng 1.300 lần. Partition khiến Athena chỉ mở 1 trong 6 thư mục theo tháng, và chỉ đọc cột `region`.
- **Truy vấn C:** quét 54,0 MiB với CSV và 0 byte với Parquet. Có vẻ Parquet lưu sẵn số dòng trong metadata nên không cần đọc dữ liệu.
- **Thời gian chạy** chênh lệch nhỏ (A: 773 ms xuống 579 ms; B: 819 ms xuống 400 ms; C: 742 ms xuống 613 ms). Mỗi truy vấn chỉ chạy một lần trên dữ liệu 54 MiB nên chưa đủ để kết luận về tốc độ; lợi ích rõ nhất là lượng dữ liệu quét, tức là chi phí.

| | CSV | Parquet + partition | Mức giảm |
|---|---|---|---|
| Dung lượng trên S3 | 54,0 MiB | 13,2 MiB | ~4,1 lần |
| Truy vấn A (GROUP BY) | 54,0 MiB | 4,97 MiB | ~10,9 lần |
| Truy vấn B (lọc tháng 3) | 54,0 MiB | 42,4 KiB | ~1.300 lần |
| Truy vấn C (COUNT) | 54,0 MiB | 0 byte | — |

Một chi tiết thú vị: câu `SELECT ... LIMIT 10` trên bảng CSV chỉ quét 694 KB vì Athena dừng đọc sớm khi đủ 10 dòng, còn với `GROUP BY` hay `WHERE` thì phải đọc cả tệp.

Nếu bảng CSV nặng 1 TB và tỷ lệ giảm giữ nguyên, mỗi lần chạy truy vấn A sẽ tốn khoảng 5 USD với CSV và khoảng 0,46 USD với Parquet, còn truy vấn B khoảng 0,004 USD. Đây chỉ là phép tính tuyến tính từ giá 5 USD/TB trên dữ liệu mô phỏng, không phải số đo thực tế ở quy mô TB.

### Một khái niệm đáng chú ý: Partition

Trong quá trình tìm hiểu, mình chú ý đến partition vì đây là cơ chế giúp Athena bỏ qua dữ liệu không liên quan. Partition hoạt động như một cột ảo: khi câu truy vấn lọc theo cột partition, Athena chỉ đọc các thư mục khớp điều kiện. Trang tài liệu của Athena về partitioning và bucketing cũng nói hai kỹ thuật này đều giúp giảm lượng dữ liệu quét, từ đó cải thiện hiệu năng và giảm chi phí.

Sau khi mình `INSERT INTO` dữ liệu tháng 7 (200.000 dòng) vào bảng Parquet, Athena tự ghi tệp mới và đăng ký thêm partition 2026-07 vào Data Catalog. Kết quả `get-partitions` tăng từ 6 lên 7 partition mà mình không phải chạy thêm bước cập nhật nào, và bảng có tổng 1.200.000 dòng.

Bài viết “Top 10 performance tuning tips for Amazon Athena” của AWS có một số lưu ý về partition mà mình thấy hữu ích:

- Nên chọn khóa partition khớp với điều kiện lọc và có số giá trị khác nhau tương đối ít, ví dụ partition theo ngày với dữ liệu tăng dần theo thời gian.
- Partition quá chi tiết sẽ tạo ra nhiều tệp nhỏ. Bài viết gợi ý partition theo ngày và sắp xếp dữ liệu theo timestamp thay vì partition theo giờ.
- Trong thử nghiệm TPC-H của AWS, một truy vấn lọc theo một ngày trên bảng có partition chỉ quét 29,96 MB thay vì 74,1 GB.

Đối chiếu với dữ liệu của mình, mỗi tệp Parquet chỉ khoảng 2,1 đến 2,3 MiB, nhỏ hơn nhiều so với mức khoảng 128 MB mà bài viết khuyến nghị cho kích thước mỗi phần đọc. Với dữ liệu thử nghiệm nhỏ thì không ảnh hưởng gì, nhưng với dữ liệu lớn thì quá nhiều tệp nhỏ sẽ làm tăng chi phí liệt kê, request và metadata.

### Một số điều cần quan tâm khi triển khai thực tế

Qua việc tìm hiểu, mình nhận thấy dùng Athena không chỉ đơn giản là tạo bảng rồi chạy SQL. Có một số vấn đề cần chú ý:

- **Định dạng và nén:** theo bài viết của AWS, Athena hỗ trợ nén gzip, Snappy và zstd; các định dạng text nén thường không chia nhỏ được nên một tệp CSV nén chỉ xử lý được trên một node, trong khi Parquet và ORC thì luôn chia nhỏ được. Với định dạng cột, Athena chỉ đọc các cột được tham chiếu.
- **Chỉ chọn các cột cần thiết:** bài viết của AWS cho thấy việc chỉ chọn các cột cần thiết giúp truy vấn chạy nhanh hơn (73% trong thử nghiệm của bài). Kết hợp với định dạng cột, thói quen `SELECT *` nên tránh với dữ liệu lớn.
- **Kích thước tệp:** tránh tạo quá nhiều tệp nhỏ, như đã nói ở phần partition.
- **Cẩn thận với INSERT INTO:** Athena chỉ nối thêm dữ liệu nên chạy hai lần sẽ ghi trùng.
- **Kiểm soát chi phí:** Athena cho phép đặt giới hạn lượng dữ liệu quét cho mỗi truy vấn thông qua workgroup, giúp tránh một truy vấn lỡ tay làm hóa đơn tăng đột biến. Mình cũng nên đặt AWS Budgets để theo dõi chi phí.
- **Khi nào không phù hợp:** Athena phù hợp với truy vấn ad-hoc trên dữ liệu có sẵn trong S3, không phù hợp với ứng dụng cần ghi và cập nhật từng dòng liên tục (nên dùng cơ sở dữ liệu như Amazon RDS). Theo trang so sánh của AWS, với truy vấn lặp đi lặp lại trên cùng dữ liệu thì Amazon Redshift có thể kinh tế hơn.
- **Dữ liệu trong bài là dữ liệu mô phỏng:** tỷ lệ nén và mức giảm lượng quét trên dữ liệu thật có thể khác.
- **Dọn dẹp:** sau khi thử, nên xóa bảng, database, workgroup và dữ liệu trong S3 (kể cả thư mục chứa kết quả truy vấn).

Ngoài ra, bài tối ưu của AWS còn nhắc đến một số kỹ thuật khác như bucketing, partition projection và tái sử dụng kết quả truy vấn. Mình chưa thử các kỹ thuật này nên không đưa vào phần thử nghiệm.

### Kết luận

Qua quá trình tìm hiểu, mình nhận thấy Amazon Athena và AWS Glue Data Catalog là hai dịch vụ có vai trò bổ trợ cho nhau trong việc truy vấn dữ liệu trên S3:

- **Glue Data Catalog:** lưu metadata (cột, kiểu dữ liệu, vị trí, partition) để các công cụ biết cách đọc dữ liệu.
- **Athena:** thực thi các câu truy vấn SQL trên dữ liệu trong S3 mà không cần quản lý máy chủ, tính phí theo lượng dữ liệu quét.

Vì Athena tính phí theo dữ liệu quét, cách lưu và tổ chức dữ liệu cũng chính là cách kiểm soát chi phí. Trong thử nghiệm của mình, chỉ cần chuyển CSV sang Parquet và chia partition theo tháng đã giúp lượng dữ liệu quét giảm từ khoảng 11 lần đến khoảng 1.300 lần tùy truy vấn.

### Tài liệu tham khảo

- Amazon Athena Pricing: <https://aws.amazon.com/athena/pricing/>
- AWS Glue Pricing: <https://aws.amazon.com/glue/pricing>
- Athena and Glue compared: <https://aws.amazon.com/compare/athena-and-glue/>
- Top 10 performance tuning tips for Amazon Athena: <https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/>
- Use partitioning and bucketing (Amazon Athena User Guide): <https://docs.aws.amazon.com/athena/latest/ug/ctas-partitioning-and-bucketing.html>

`#AWS` `#AmazonAthena` `#AWSGlue` `#GlueDataCatalog` `#DataLake` `#ApacheParquet` `#AmazonS3` `#CloudComputing` `#AWSStudyGroup`
