---
title: "Blog 2"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Amazon Athena with the AWS Glue Data Catalog – Querying data in S3 with SQL, no servers needed

*Posted on 11/11/2026 in the AWS Study Group – [View the original post](https://www.facebook.com/photo/?fbid=4673778119572839&set=gm.2333754607389498&idorvanity=660548818043427)*

In many systems, raw data such as logs or CSV files is stored in Amazon S3. S3 only stores files: it does not know which columns a file has and cannot directly answer questions like “total revenue by product category”. The familiar approach is to load the data into a database, but that takes effort to build, operate, and keep in sync.

While learning about AWS and its analytics options, I became interested in querying data right where it sits in S3 without moving it. Two services show up in this approach: Amazon Athena and the AWS Glue Data Catalog.

What I found interesting is that the two services do different jobs. The Glue Data Catalog stores metadata that describes the data (which columns, what types, where it lives), and Athena runs SQL queries based on that metadata.

![Amazon Athena reads CSV in S3, uses metadata in the AWS Glue Data Catalog, and writes Parquet partitioned by month](/images/3-BlogsPosted/3.2-Blog2/athena-glue-architecture.png)

### AWS Glue Data Catalog – Where metadata for S3 data lives

AWS Glue is AWS's data integration service. According to AWS's comparison page, Glue has several components such as the Data Catalog, ETL, and crawlers, but this post focuses only on the Data Catalog, the part Athena uses directly.

The Data Catalog stores metadata that describes data rather than the data itself. After creating a table, I used the AWS CLI (`get-table` and `get-partitions`) to look at the Catalog entry and found these main parts:

- **Columns:** the names and data types of the columns in the files.
- **PartitionKeys:** the partition columns, kept separate from Columns because their values come from folder names (for example `month=2026-03`) rather than from the files.
- **Location:** the S3 path that holds the data.
- **SerDe and InputFormat:** how the files are read. My CSV table uses LazySimpleSerDe, and the Parquet table uses the Parquet SerDe.
- **TableType:** `EXTERNAL_TABLE`, meaning the table only describes existing data, so dropping the table leaves the files in S3.

On cost, according to the Glue pricing page, the first 1 million metadata objects and the first 1 million requests per month in the Data Catalog are free.

### Amazon Athena – SQL queries directly on data in S3

Amazon Athena lets you use standard SQL to query data in S3. It is serverless, so there are no servers to create or manage, and an Athena table is just a description layered on top of existing files (schema-on-read).

What stood out most to me is the pricing. According to the Athena pricing page, SQL queries cost 5 USD per TB of data scanned, plus standard S3 charges (storage, requests, data transfer, including query results) and standard Glue Data Catalog charges. The pricing page gives an example: a query that scans 3 TB costs 15 USD, 5 USD if the data is gzip-compressed at 3:1, and 1.25 USD if it also uses the columnar Parquet format.

This made me pay attention to file formats and data layout, because they directly affect the bill.

### How does Athena work together with the Glue Data Catalog?

To understand it better, I tried a scenario with simulated sales data (generated in Python): 1,000,000 rows for the first 6 months of 2026, with 8 columns: `order_id`, `order_date`, `month`, `category`, `region`, `quantity`, `unit_price`, `revenue`. The CSV file was 54.0 MiB.

First, I uploaded the CSV to S3 and created a table with `CREATE EXTERNAL TABLE` in Athena. Athena then wrote the table's metadata to the Glue Data Catalog, and the table pointed to the folder in S3. I ran three queries:

- **Query A:** total revenue by product category (`SUM`, `GROUP BY`).
- **Query B:** order count by region, filtered to March.
- **Query C:** total row count (`COUNT`).

Next, I converted the data to Parquet (Snappy compression), partitioned by month, with a single CTAS statement in Athena, then ran the same three queries on the new table:

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

The partition column (`month`) must come last in the `SELECT`.

The results from both tables matched exactly (same revenue by category, same order counts by region, same 1,000,000 rows). Only the amount of data Athena scanned differed:

- **Size in S3:** CSV 54.0 MiB, Parquet 13.2 MiB (6 files), about 4.1 times smaller.
- **Query A:** scanned 54.0 MiB with CSV and 4.97 MiB with Parquet, about 10.9 times less. Parquet stores data by column, so it reads only the two columns needed (`category`, `revenue`).
- **Query B:** scanned 54.0 MiB with CSV and 42.4 KiB with Parquet, about 1,300 times less. Partitioning lets Athena open only 1 of the 6 monthly folders and read only the `region` column.
- **Query C:** scanned 54.0 MiB with CSV and 0 bytes with Parquet. Parquet seems to store the row count in its metadata, so no data needs to be read.
- **Run time** differed only slightly (A: 773 ms down to 579 ms; B: 819 ms down to 400 ms; C: 742 ms down to 613 ms). Each query ran once on 54 MiB of data, which is not enough to draw conclusions about speed; the clearest gain is the data scanned, which means cost.

| | CSV | Parquet + partition | Reduction |
|---|---|---|---|
| Size in S3 | 54.0 MiB | 13.2 MiB | ~4.1x |
| Query A (GROUP BY) | 54.0 MiB | 4.97 MiB | ~10.9x |
| Query B (March filter) | 54.0 MiB | 42.4 KiB | ~1,300x |
| Query C (COUNT) | 54.0 MiB | 0 bytes | — |

An interesting detail: `SELECT ... LIMIT 10` on the CSV table scanned only 694 KB because Athena stops reading once it has 10 rows, while `GROUP BY` or `WHERE` must read the whole file.

If the CSV table were 1 TB and the reduction ratios held, each run of query A would cost about 5 USD with CSV and about 0.46 USD with Parquet, and query B about 0.004 USD. This is only a linear calculation from the 5 USD/TB price on simulated data, not a real measurement at TB scale.

### A concept worth noting: Partitions

While learning, I focused on partitions because they let Athena skip irrelevant data. A partition acts like a virtual column: when a query filters on the partition column, Athena reads only the matching folders. The Athena documentation on partitioning and bucketing also says both techniques reduce the data scanned, improving performance and lowering cost.

After I ran `INSERT INTO` with July data (200,000 rows) into the Parquet table, Athena wrote the new files and registered the 2026-07 partition in the Data Catalog on its own. `get-partitions` went from 6 to 7 partitions without any extra update step, and the table reached 1,200,000 rows in total.

AWS's article “Top 10 performance tuning tips for Amazon Athena” has some useful partition advice:

- Choose a partition key that matches your filters and has relatively few distinct values, for example partitioning by day for data that grows over time.
- Partitions that are too fine-grained create many small files. The article suggests partitioning by day and sorting by timestamp rather than partitioning by hour.
- In AWS's TPC-H test, a query filtering on one day of a partitioned table scanned only 29.96 MB instead of 74.1 GB.

Compared with my data, each Parquet file is only about 2.1 to 2.3 MiB, far below the roughly 128 MB the article recommends per read split. With small test data this does not matter, but with large data too many small files increase listing, request, and metadata costs.

### Things to watch in real deployments

From what I learned, using Athena is more than creating a table and running SQL. A few points need attention:

- **Format and compression:** according to the AWS article, Athena supports gzip, Snappy, and zstd; compressed text formats usually cannot be split, so a compressed CSV file is processed on one node, while Parquet and ORC are always splittable. With columnar formats, Athena reads only the referenced columns.
- **Select only the columns you need:** the AWS article shows that selecting only the needed columns makes queries faster (73% in its test). Combined with columnar formats, avoid `SELECT *` on large data.
- **File size:** avoid creating too many small files, as noted in the partition section.
- **Be careful with INSERT INTO:** Athena only appends, so running it twice writes duplicates.
- **Cost control:** Athena lets you cap the data scanned per query through a workgroup, so one careless query cannot spike the bill. It is also worth setting AWS Budgets to track costs.
- **When it is not a fit:** Athena suits ad-hoc queries on data already in S3, not applications that continuously write and update individual rows (use a database such as Amazon RDS). According to AWS's comparison page, Amazon Redshift may be more economical for repeated queries on the same data.
- **The data in this post is simulated:** compression ratios and scan reductions on real data may differ.
- **Clean up:** after testing, drop the tables, database, and workgroup and delete the data in S3 (including the query results folder).

The AWS tuning article also mentions other techniques such as bucketing, partition projection, and query result reuse. I have not tried them yet, so they are not part of this experiment.

### Conclusion

From this exploration, Amazon Athena and the AWS Glue Data Catalog complement each other for querying data in S3:

- **Glue Data Catalog:** stores metadata (columns, data types, location, partitions) so tools know how to read the data.
- **Athena:** runs SQL queries on data in S3 without managing servers, billed by data scanned.

Because Athena charges by data scanned, how you store and organize data is also how you control cost. In my test, simply converting CSV to Parquet and partitioning by month cut the data scanned by about 11 to about 1,300 times depending on the query.

### References

- Amazon Athena Pricing: <https://aws.amazon.com/athena/pricing/>
- AWS Glue Pricing: <https://aws.amazon.com/glue/pricing>
- Athena and Glue compared: <https://aws.amazon.com/compare/athena-and-glue/>
- Top 10 performance tuning tips for Amazon Athena: <https://aws.amazon.com/blogs/big-data/top-10-performance-tuning-tips-for-amazon-athena/>
- Use partitioning and bucketing (Amazon Athena User Guide): <https://docs.aws.amazon.com/athena/latest/ug/ctas-partitioning-and-bucketing.html>

`#AWS` `#AmazonAthena` `#AWSGlue` `#GlueDataCatalog` `#DataLake` `#ApacheParquet` `#AmazonS3` `#CloudComputing` `#AWSStudyGroup`
