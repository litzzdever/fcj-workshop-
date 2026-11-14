---
title: "Week 9 Worklog"
date: 2026-11-10
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:

* Learn AWS Step Functions to coordinate several processing steps as one stateful workflow.
* Practice error handling in workflows with Retry, Catch, and the main state types.
* Learn Amazon Kinesis Data Streams and basic stream operations with the AWS CLI.
* Build a flow that delivers streaming data to S3 with Amazon Data Firehose.
* Query data in S3 with SQL using Amazon Athena.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Tue | - Read the Step Functions Getting started section: state machines, states, executions, and definitions in Amazon States Language (JSON) <br> - Compared Standard workflows (long-running, exactly-once) with Express workflows (high-volume, short-lived) <br> - Followed the tutorial to build a state machine in Workflow Studio: added Pass, Choice, and Task states, ran an execution, and inspected each step's input and output in Graph view <br> - Studied the IAM role the state machine uses to call other services <br> - Cleaned up: deleted the state machine and the lab IAM role | 11/10/2026 | 11/10/2026 | <https://docs.aws.amazon.com/step-functions/latest/dg/getting-started.html> |
| Wed | - Followed the tutorial for a state machine that calls Lambda: wrote a Lambda function, attached it to a Task state, passed input, and read the output <br> - Followed the error handling tutorial: configured `Retry` (attempts, `IntervalSeconds`, `BackoffRate`) and `Catch` to route to an error-handling state <br> - Studied the Parallel and Map states for running branches in parallel or iterating over a list <br> - Looked at building the same workflow with CloudFormation to compare with the console <br> - Cleaned up: deleted the state machine, the Lambda function, and the log group | 11/11/2026 | 11/11/2026 | <https://docs.aws.amazon.com/step-functions/latest/dg/tutorial-handling-error-conditions.html> |
| Thu | - Read Kinesis Data Streams concepts: data stream, shard, partition key, sequence number, and retention period <br> - Compared On-demand and Provisioned capacity modes <br> - Followed the basic AWS CLI tutorial: `create-stream`, `describe-stream-summary`, `put-record`, `get-shard-iterator`, `get-records` <br> - Learned the producer and consumer roles, and how the partition key decides which shard gets a record <br> - Cleaned up: ran `delete-stream` to stop the stream's hourly charge | 11/12/2026 | 11/12/2026 | <https://docs.aws.amazon.com/streams/latest/dev/fundamental-stream.html> |
| Fri | - Studied Amazon Data Firehose (formerly Kinesis Data Firehose): delivers streaming data to a destination without writing your own consumer <br> - Created a Firehose stream with Direct PUT or Kinesis Data Streams as the source and an S3 bucket as the destination <br> - Configured buffering (size and interval) and a time-based prefix for S3 objects <br> - Studied record transformation with Lambda and format conversion to Parquet <br> - Sent test records and checked that files appeared in S3 after the buffer interval <br> - Cleaned up: deleted the Firehose stream, then emptied and deleted the S3 bucket | 11/13/2026 | 11/13/2026 | <https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html> |
| Sat | - Read the Amazon Athena Get started guide: query data in S3 directly with SQL, with no servers to manage <br> - Set the query result location in S3 for the workgroup <br> - Followed the tutorial: created a database, created a table over sample data with `CREATE EXTERNAL TABLE`, ran a `SELECT` query, and reviewed the results <br> - Learned that Athena charges by data scanned, and why partitions and columnar formats (Parquet) lower the cost <br> - Linked back to Friday: planned to use Athena to query the data Firehose writes to S3 <br> - Cleaned up: dropped the table and database and deleted query results in S3 | 11/14/2026 | 11/14/2026 | <https://docs.aws.amazon.com/athena/latest/ug/getting-started.html> |

### Week 9 Issues and Notes:

* The Lambda function in a Task state fails with AccessDenied if the state machine's IAM role lacks `lambda:InvokeFunction`: add a policy for the function's ARN.

* Kinesis `get-records` returns nothing when the shard iterator type is `LATEST` and the records were put earlier: use `TRIM_HORIZON` to read from the start. A shard iterator is valid for only 5 minutes, so get a new one when it expires.

* AWS CLI v2 treats `--data` as base64 by default: add `--cli-binary-format raw-in-base64-out` when sending plain strings with `put-record`.

* Firehose files do not appear in S3 right away because Firehose waits for the buffer size or buffer interval. JSON records run together if the producer adds no newline, and Athena then reads them incorrectly.

* Athena needs a query result location before the first query. A table's `LOCATION` must point to an S3 prefix (folder), not to a single file.

### Week 9 Achievements:

* Understood how Step Functions coordinates multiple steps as a workflow, and the difference between Standard and Express workflows.

* Learned to use Retry and Catch so a workflow retries or branches when a step fails, along with the Choice, Parallel, and Map states.

* Learned the shard, partition key, producer, and consumer concepts of Kinesis Data Streams, and operated a stream with the AWS CLI.

* Understood how Firehose buffers streaming data and writes it to S3, with optional transformation through Lambda.

* Learned to query data in S3 with Athena and how to reduce the cost of scanned data.

* Cleaned up resources after each lab to avoid hourly stream charges and storage costs.
