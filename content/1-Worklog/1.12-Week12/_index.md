---
title: "Week 12 Worklog"
date: 2026-12-01
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Week 12 Objectives:

* Build a CI/CD flow on AWS with AWS CodeBuild and AWS CodePipeline.
* Learn data encryption with AWS KMS and web application protection with AWS WAF.
* Track configuration compliance with AWS Config and aggregate security findings with AWS Security Hub CSPM.
* Learn multi-account governance with AWS Organizations and IAM Identity Center, and cost control with AWS Budgets.
* Review the 12 weeks of study and check and clean up every remaining resource in the account.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Tue | - Read Getting started with CodeBuild: build projects, build environments, and the `buildspec.yml` file with `install`, `pre_build`, `build`, `post_build` phases and `artifacts` <br> - Connected a GitHub repo as the source, chose a managed build image, and created a CodeBuild service role <br> - Wrote a `buildspec.yml` for a Node.js app: install dependencies, run tests, build, and output artifacts to S3 <br> - Ran the build and read each phase's log in the console and CloudWatch Logs | 12/01/2026 | 12/01/2026 | <https://docs.aws.amazon.com/codebuild/latest/userguide/getting-started-overview.html> |
| Thu | - Read CodePipeline concepts: pipeline, stage, action, artifact, execution <br> - Followed the tutorial to build a pipeline: a Source stage (GitHub through a connection, or an S3 bucket), a Build stage using Tuesday's CodeBuild project, and a Deploy stage using Amazon S3 as the deployment provider for a static website <br> - Pushed a new commit and watched the pipeline run again from Source to Deploy <br> - Studied manual approval stages and pipeline-level variables <br> - Cleaned up: deleted the pipeline, build project, artifact bucket, and connection | 12/03/2026 | 12/03/2026 | <https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-s3deploy.html> |
| Sat | - Read AWS KMS cryptography essentials: KMS keys, AWS managed versus customer managed keys, symmetric and asymmetric keys <br> - Created a symmetric customer managed key, set an alias, and read the default key policy <br> - Practiced `aws kms encrypt` and `aws kms decrypt` on small data <br> - Studied envelope encryption: create a data key with `generate-data-key`, encrypt large data with the data key, and let KMS encrypt only the data key <br> - Studied how S3, EBS, and DynamoDB use KMS for encryption at rest, and automatic key rotation <br> - Cleaned up: scheduled the key for deletion (minimum 7-day waiting period) | 12/05/2026 | 12/05/2026 | <https://docs.aws.amazon.com/kms/latest/developerguide/kms-cryptography.html> |
| Tue | - Read Getting started with AWS WAF: protection packs (web ACLs), rules, rule groups, AWS Managed Rules, and the Allow, Block, and Count actions <br> - Created a web ACL attached to a test API Gateway REST API or Application Load Balancer <br> - Added managed rule groups (Core rule set, Known bad inputs) and a rate-based rule that limits requests per IP <br> - Tested with normal and blocked requests, and reviewed sampled requests and CloudWatch metrics <br> - Studied AWS Shield Standard (on by default at no cost) and how it differs from Shield Advanced <br> - Cleaned up: detached the web ACL, then deleted it and the test resources | 12/08/2026 | 12/08/2026 | <https://docs.aws.amazon.com/waf/latest/developerguide/setup-existing-console.html> |
| Thu | - Read AWS Config: configuration recorder, configuration items, managed rules, custom rules, conformance packs <br> - Turned on Config in the working region and added managed rules, such as checking that S3 buckets block public access and EBS volumes are encrypted <br> - Reviewed Compliant and Noncompliant status and each resource's configuration history <br> - Turned on AWS Security Hub CSPM, enabled the AWS Foundational Security Best Practices standard, and reviewed the security score and findings by severity <br> - Learned that Security Hub CSPM aggregates findings from GuardDuty, Inspector, and Config <br> - Cleaned up: turned off Security Hub CSPM, deleted the rules, and stopped the configuration recorder to avoid charges | 12/10/2026 | 12/10/2026 | <https://docs.aws.amazon.com/config/latest/developerguide/setting-up-aws-config-rules-with-console.html> |
| Sat | - Followed the AWS Organizations tutorial: created an organization and learned the management account, member accounts, and organizational units (OUs) <br> - Studied service control policies (SCPs) that cap the permissions of accounts in an OU, such as blocking use outside allowed regions <br> - Read Enable IAM Identity Center: created users, groups, and permission sets, assigned access to accounts, and signed in through the AWS access portal instead of long-lived IAM users <br> - Created a monthly cost budget in AWS Budgets with email alerts when actual or forecasted cost passes a threshold <br> - Cleaned up: deleted the test users and permission sets | 12/12/2026 | 12/12/2026 | <https://docs.aws.amazon.com/organizations/latest/userguide/orgs_tutorials_basic.html> |
| Mon | - Reviewed the 12 weeks: organized the service groups studied (compute, storage, database, serverless, containers, analytics, AI/ML, DevOps, security, governance) <br> - Audited the account in every region used: EC2, EBS, Elastic IP, NAT Gateway, Load Balancer, RDS, Redshift, Kinesis, CloudFormation stacks, S3 buckets <br> - Checked Billing and Cost Explorer to confirm no unexpected charges remained <br> - Updated the worklog and noted the labs to redo or study in more depth | 12/14/2026 | 12/14/2026 | <https://docs.aws.amazon.com/cost-management/latest/userguide/create-cost-budget.html> |

### Week 12 Issues and Notes:

* CodeBuild looks for `buildspec.yml` at the repo root by default. The service role needs write access to the artifact bucket, or the build fails at the artifact upload step.

* A GitHub connection in CodePipeline is created in Pending status: finish the GitHub authorization in the console before the pipeline can run.

* A KMS key cannot be deleted right away: it can only be scheduled for deletion with a 7 to 30 day waiting period, during which the key is unusable but the deletion can be cancelled.

* A web ACL for CloudFront must be created with Global scope (us-east-1). AWS WAF attaches to API Gateway REST APIs but not to HTTP APIs.

* Security Hub CSPM needs AWS Config recording to run its security controls. Config charges per configuration item recorded, so stop the recorder after the lab.

* IAM Identity Center can be enabled in only one region per organization, so choose the region first. The account that creates the organization becomes the management account.

### Week 12 Achievements:

* Understood CI/CD on AWS: CodeBuild runs builds from `buildspec.yml`, and CodePipeline automates the flow from source to deploy.

* Learned how KMS manages keys, how envelope encryption works, and how storage services use KMS to encrypt data.

* Learned to use AWS WAF with managed rules and rate-based rules to protect APIs and web applications.

* Learned to use AWS Config to track configuration compliance and Security Hub CSPM to aggregate security findings.

* Understood multi-account governance with Organizations, SCPs, and IAM Identity Center, and how to set budget alerts with AWS Budgets.

* Completed the 12-week program review and cleaned up and checked costs across the whole account.
