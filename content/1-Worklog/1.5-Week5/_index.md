---
title: "Week 5 Worklog"
date: 2026-10-14
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Learn Amazon Cognito User Pools and how two websites can share the same identity system.
* Practice AWS CDK: bootstrap an environment, deploy a stack, and destroy it after the lab.
* Configure two local React apps on Windows and test cross-site login and logout.
* Complete the SageMaker Immersion Day path from feature engineering to train, tune, and deploy.
* Build a serverless order flow with API Gateway, Lambda, SQS, SNS, and DynamoDB.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Wed | - Studied Cognito User Pool as the shared identity store for web apps, and the difference between a shared account and SSO session <br> - Prepared the `aws_cognito_cross_sites` repo on Windows PowerShell, with Node.js, npm, and AWS CLI profile `cognito-lab` <br> - Ran `cdk bootstrap` for account and region `ap-southeast-1`, then deployed `CognitoStack` from the `cdk` folder <br> - Recorded User Pool ID `ap-southeast-1_YJMBULPFi`, App Client ID `3fr8lkifba3p3in0oi1oomo22h`, region `ap-southeast-1` <br> - Wrote `.env.local` for `app` and `app2` with `REACT_APP_USERPOOL_ID` and `REACT_APP_CLIENT_ID`; ran Website 1 on `localhost:3000` and Website 2 on `localhost:3001` <br> - Registered on Website 1, then signed in to both sites with the same username and password <br> - After login on Website 1, Website 2 recognized the session without asking for the password again. After SIGN OUT WEB1 and Ctrl+F5 on Website 2, Website 2 required login again <br> - Ran `npx cdk destroy` in `cdk`; stack returned `CognitoStack: destroyed`. Stopped both React apps. Left `CDKToolkit` in place for later CDK work <br> - Fixed PowerShell errors by putting env vars in `.env.local`, retried an `ECONNRESET` during CDK deploy, and noted the Node.js v24 warning without blocking deploy or destroy | 10/14/2026 | 10/14/2026 | <https://000141.awsstudygroup.com/1-intro/> |
| Fri | - Completed the SageMaker Immersion Day workshop: end-to-end ML from feature engineering to built-in algorithms, training, tuning, and deploy <br> - Used Data Wrangler to prepare the dataset, analyze it, check correlation, transform features, write to Feature Store, and export to S3 <br> - Trained, tuned, and deployed an XGBoost model, then called the endpoint for predictions <br> - Cleaned up in order: deleted the endpoint, deleted each Studio app, deleted the user, then deleted Studio. Typed `delete` to confirm | 10/16/2026 | 10/16/2026 | <https://000200.awsstudygroup.com/> |
| Sat | - Completed the serverless order lab. Checkout writes to a queue and publishes a notice; handling the order is a separate step <br> - Compared SQS and SNS: SQS is a queue between producer and consumer; SNS is pub/sub from one topic to many subscribers. The lab uses both <br> - Deployed the SAM app: edited `template.yaml`, ran `sam validate`, `sam build`, and `sam deploy --guided` as stack `fcaj-book-shop` in `us-east-1` <br> - Created DynamoDB table `OrdersTable` and four Lambda functions: `checkout_order`, `order_management`, `handle_order`, `delete_order` <br> - Flow: `POST /books/order` puts a message on SQS `checkout-queue` and publishes SNS topic `order-notice`. Admin lists orders with `GET /books/order`. Handle writes the order to DynamoDB and removes it from the queue. Delete removes it from the queue <br> - Built the React frontend, set `APP_API_URL` to the API Gateway staging Invoke URL, and uploaded `build/` to S3 <br> - Tested: created books Java and Let's Go, added them to the cart, checked out, saw the message in `checkout-queue` and the email notice, handled the order to status Processed, confirmed the item in `OrdersTable`, then deleted the order <br> - Cleaned up the Route 53 hosted zone if created, ran `sam delete` for `fcaj-book-store` and `aws-sam-cli-managed-default`, emptied related S3 buckets, and deleted queue `checkout-queue` and topic `order-notice` | 10/17/2026 | 10/17/2026 | <https://000083.awsstudygroup.com/> |

### Week 5 Achievements:

* Deployed a Cognito User Pool with AWS CDK and used the same account on two local React sites.

* Tested cross-site behavior: Website 2 recognized the Website 1 session, and logout on Website 1 forced Website 2 to ask for login again after a hard refresh.

* Completed the SageMaker path from Data Wrangler and Feature Store to a tuned and deployed XGBoost endpoint, then removed Studio and the endpoint.

* Built a decoupled order flow: checkout goes to SQS and SNS, and the order is written to DynamoDB only when an admin handles it.

* Practiced infrastructure as code twice, with CDK for Cognito and SAM for the book shop, including destroy and `sam delete` after the labs.
