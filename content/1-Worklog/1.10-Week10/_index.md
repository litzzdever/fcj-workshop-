---
title: "Week 10 Worklog"
date: 2026-11-17
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:

* Learn AWS Glue: the Data Catalog, crawlers, and ETL jobs for preparing data for analytics.
* Practice visual ETL in AWS Glue Studio and read a Glue for Spark script.
* Learn AWS Lake Formation for managing data lake access at the database, table, and column level.
* Get started with the Amazon Redshift Serverless data warehouse and query it with query editor v2.
* Build a data visualization dashboard with Amazon Quick Sight.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Tue | - Read AWS Glue concepts: Data Catalog, database, table, crawler, classifier, connection, job, trigger <br> - Learned that the Data Catalog is the shared metadata store for Athena, Redshift Spectrum, and EMR <br> - Created an IAM role for Glue with read access to the S3 bucket holding sample data <br> - Created a crawler over CSV/JSON data in S3, ran it, and reviewed the table schema it created in the Data Catalog <br> - Queried the new table with Athena to check the schema | 11/17/2026 | 11/17/2026 | <https://docs.aws.amazon.com/glue/latest/dg/components-key-concepts.html> |
| Wed | - Built a visual ETL job in AWS Glue Studio: Data Catalog source, transforms (change types, drop columns, filter rows), and a Parquet target in S3 <br> - Read the tutorial on writing a Glue for Spark script: `GlueContext`, `DynamicFrame`, `ApplyMapping` <br> - Studied DPUs, worker types, and job bookmarks for processing only new data <br> - Ran a second crawler on the Parquet output and compared the data scanned when querying with Athena <br> - Cleaned up: deleted the job, crawler, Data Catalog database, and output data in S3 | 11/18/2026 | 11/18/2026 | <https://docs.aws.amazon.com/glue/latest/dg/aws-glue-programming-intro-tutorial.html> |
| Thu | - Read Set up AWS Lake Formation: the data lake administrator, and registering an S3 location as a data lake location <br> - Learned how Lake Formation permissions work together with IAM, and the default setting that uses IAM access control for new tables <br> - Granted `SELECT` at the database, table, and column level to another IAM user, then checked it with Athena <br> - Read the tutorials on tag-based access control (LF-Tags) and row-level security <br> - Cleaned up: revoked permissions and deregistered the data lake location | 11/19/2026 | 11/19/2026 | <https://docs.aws.amazon.com/lake-formation/latest/dg/initial-lf-config.html> |
| Fri | - Read Get started with Amazon Redshift Serverless: the namespace (data) and the workgroup (compute) <br> - Created a workgroup with default settings, and learned RPUs and billing based on query time <br> - Opened query editor v2, loaded the built-in sample data, and ran the sample SQL queries <br> - Studied the `COPY` command for loading data from S3 and Redshift Spectrum for querying Data Catalog tables <br> - Compared Redshift (data warehouse) with Athena (direct queries on S3) <br> - Cleaned up: deleted the workgroup and namespace to stop charges | 11/20/2026 | 11/20/2026 | <https://docs.aws.amazon.com/redshift/latest/gsg/new-user-serverless.html> |
| Sat | - Read Getting started with Amazon Quick Sight (now part of Amazon Quick Suite): datasets, analyses, visuals, dashboards <br> - Checked the subscription plan and cost before signing up for Quick Sight <br> - Followed the tutorial to create an analysis from sample data: added bar, line, and table visuals, and a filter <br> - Published the analysis as a dashboard and learned how to share it with other users <br> - Studied connecting Quick Sight to Athena as a data source <br> - Cleaned up: deleted the dataset, analysis, and dashboard, and cancelled the subscription if no longer needed | 11/21/2026 | 11/21/2026 | <https://docs.aws.amazon.com/quicksight/latest/user/getting-started.html> |

### Week 10 Achievements:

* Understood how a Glue crawler infers a schema and stores metadata in the Data Catalog shared by analytics services.

* Learned to build a visual ETL job in Glue Studio to clean data and convert it to Parquet.

* Understood how Lake Formation controls data lake access at the database, table, and column level and through LF-Tags.

* Learned the Redshift Serverless architecture (namespace and workgroup) and querying with query editor v2.

* Learned to build an analysis and publish a dashboard in Amazon Quick Sight.

* Connected the analytics flow end to end: S3 → Glue → Athena or Redshift → Quick Sight.

* Cleaned up services billed by run time, such as Glue jobs, the Redshift workgroup, and the Quick Sight subscription.
