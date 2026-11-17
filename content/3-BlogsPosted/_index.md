---
title: "Blogs Posted"
date: 2026-11-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

This section lists the blogs I posted to the [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj) during my internship.

###  [Blog 1 - AMAZON SAGEMAKER AI – HOW AWS HELPS AI ENGINEERS TRAIN, TUNE, AND DEPLOY MODELS](3.1-Blog1/)
*Posted on 01/11/2026.* This blog introduces Amazon SageMaker AI, a fully managed machine learning service that helps AI engineers take models from notebook to production: Feature Store, flexible training with instance preference lists, tuning with serverless model customization (SFT, DPO, RLAIF), choosing a deployment configuration with inference recommendations, and monitoring 100+ metrics in CloudWatch. It also covers use cases, cost and region caveats, and a common SageMaker SDK v3 error.

###  [Blog 2 - Amazon Athena with the AWS Glue Data Catalog – Querying data in S3 with SQL, no servers needed](3.2-Blog2/)
*Posted on 11/11/2026.* This blog explores how the Glue Data Catalog stores metadata while Athena runs SQL directly on S3 data and charges by data scanned. A test with 1,000,000 rows of simulated sales data shows that converting CSV to Parquet and partitioning by month cut the data scanned by about 11 to about 1,300 times depending on the query, with notes on partitioning, file size, and cost control.

###  [Blog 3 - Amazon Rekognition with Amazon Athena – Analyzing images with AI and querying the results with SQL](3.3-Blog3/)
*Posted on 14/11/2026.* This blog uses Amazon Rekognition (`DetectLabels`) to label 30 images from the Imagenette dataset automatically, stores the results as JSON Lines in S3, and aggregates them with SQL in Amazon Athena. It compares `MinConfidence` thresholds of 80 and 55 (166 and 243 labels), checks label accuracy, and covers cost, privacy, and IAM notes.
