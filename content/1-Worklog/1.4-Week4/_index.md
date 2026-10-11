---
title: "Week 4 Worklog"
date: 2026-10-07
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Learn Amazon DynamoDB as a fully managed NoSQL service: core components, primary key, secondary indexes, read consistency, and capacity modes.
* Configure the AWS CLI on a local Windows machine and use least-privilege credentials instead of the root user.
* Practice the DynamoDB data lifecycle with boto3: create a table, write, read, update, delete, batch load, Query, Scan, and delete the table.
* Handle new-account verification blocks on CloudFront, Bedrock, and CloudShell through AWS Support.
* Build a scheduled EC2 stop/start flow with Lambda, EventBridge, and a Slack webhook to reduce idle instance cost.
* Complete Secrets Manager with RDS and Fargate, the CloudWatch workshop, and a serverless DynamoDB document API with Lambda.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Wed | - Studied DynamoDB core components: Table, Item, Attribute, partition key, sort key, GSI and LSI, scalar/set/document types <br> - Compared eventually consistent, strongly consistent, and transactional reads; On-demand versus Provisioned capacity <br> - Reviewed backup, PITR for 35 days, TTL, encryption at rest, and 2026 features such as vector search and global tables <br> - CloudShell failed with account verification in progress; same class of block as CloudFront and Bedrock. Opened a support case and used local AWS CLI instead <br> - Configured AWS CLI v2 on Windows with Python 3.13 and boto3, region `us-east-1` <br> - Created table `Movies` (`year` number, `title` string, `PAY_PER_REQUEST`); put, get, update, and delete an item; loaded 4 sample movies with `Decimal`; Query by year and Scan by rating; deleted the table | 10/07/2026 | 10/07/2026 | <https://000060.awsstudygroup.com/1-introduce/1.1-corecomponentsofamazondynamodb/> <br> <https://000060.awsstudygroup.com/3-gettingstartedwithawssdk/> |
| Thu | - Read the EC2 cost lab: Lambda plus EventBridge stops and starts tagged instances, then posts status to Slack <br> - Created a VPC, security group, and instance `lambda-lab-instance`; tagged it `environment_auto=true` <br> - Created role `dc-common-lambda-role` with least privilege for describe, stop, start, and logs <br> - Wrote `dc-common-lambda-auto-stop` and the start function on Python 3.14, x86_64; EventBridge schedule rules call them <br> - Tested stop and start in the EC2 console and received the Slack messages; stored the webhook URL outside the shared code <br> - Cleaned up EventBridge rules, Lambda functions, the role, the instance, the security group, and the VPC | 10/08/2026 | 10/08/2026 | <https://000022.awsstudygroup.com/1-introduce/> |
| Fri | - Completed the level 300 lab: Secrets Manager with Amazon RDS and AWS Fargate. Passwords stay out of code and environment variables <br> - Lab network: VPC with 2 subnets, Amazon Linux 2 bastion, private RDS, credentials in Secrets Manager <br> - Accessed RDS through the secret, then ran secret rotation and checked it with shell scripts <br> - Built the app image, pushed it to ECR, and ran it on ECS Fargate so the container reads the secret and connects to private RDS <br> - Mapped the lab to CSF Prevention and CAF Preventative controls | 10/09/2026 | 10/09/2026 | <https://000096.awsstudygroup.com/> |
| Sat | - Completed the CloudWatch workshop: one place for metrics, logs, alarms, and dashboards <br> - Created CloudFormation stack `AwsWorkshopStack` from `template.yml` <br> - Reviewed metrics and dimensions, then log-based metric `/var/log/messages - ERROR` <br> - Created an alarm, period 1 minute, threshold Greater than 10, with an SNS email topic <br> - Added the alarm to a dashboard and cleaned up the stack, alarm, topic, and dashboard | 10/10/2026 | 10/10/2026 | <https://000036.awsstudygroup.com/1-introduce/> |
| Sun | - Completed the serverless document lab: Lambda reads and writes DynamoDB <br> - Created table `Documents`, partition key `user_id`, sort key `file`, on-demand capacity <br> - Created `list_documents`, `upload_document`, and `delete_document` <br> - Loaded sample data with `aws dynamodb batch-write-item` and `documentData.json` <br> - Tested list for user `abcd1234`, upload of `aws_serverless.doc`, and delete; each returned succeeded and the table matched <br> - Reviewed the wider DMS path: Amplify, Cognito, S3, DynamoDB, OpenSearch, CloudFront, X-Ray, CloudWatch; cleaned up the table and functions | 10/11/2026 | 10/11/2026 | <https://000133.awsstudygroup.com/> |

### Week 4 Achievements:

* Can explain DynamoDB core pieces and the difference between Query and Scan, and between On-demand and Provisioned capacity.

* Configured AWS CLI on Windows and completed the Movies table lifecycle in boto3, including `Decimal` for numbers.

* Replaced blocked CloudShell with local CLI, and grouped CloudFront, Bedrock, and CloudShell verification into one support case.

* Built and tested a tagged EC2 stop/start schedule with Lambda, EventBridge, and Slack, then removed the lab resources.

* Used Secrets Manager for RDS credentials, including rotation, and connected a Fargate task to a private database.

* Created a CloudWatch alarm from a log metric and placed it on a dashboard.

* Exposed DynamoDB through three Lambda functions for list, upload, and delete, with passing test events.
