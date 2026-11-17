---
title: "Proposal"
date: 2026-10-30
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Phu Tung Xe May Platform
## A serverless motorbike parts e-commerce platform on AWS with an AI sales chatbot

Project source code: [github.com/lointt/phutungxemay-platform](https://github.com/lointt/phutungxemay-platform)

### 1. Executive Summary

Phu Tung Xe May Platform is a website for genuine motorbike parts. Customers find parts by bike model, place orders, pay with VNPay or cash on delivery, track Giao Hang Nhanh (GHN) shipments, and receive confirmation emails. An AI chatbot advises on parts, answers store policy questions, checks stock, adds items to the cart, and hands over to staff when needed.

The system is **100% serverless on AWS** in Singapore (`ap-southeast-1`): a Next.js 16 frontend on AWS Lambda and Amazon CloudFront, 5 Python microservices behind Amazon API Gateway, asynchronous order processing with Amazon EventBridge and Amazon SQS, data in Amazon DynamoDB, and sign-in with Amazon Cognito. All infrastructure is managed with **Terraform** and deployed automatically with **GitHub Actions**. With no always-on servers, cost grows with real traffic and stays close to zero when usage is low.

### 2. Problem Statement

**Current problems**

- Customers struggle to find the right part for their bike; small shops usually advise manually by phone or chat.
- After an order is placed, manual steps (reduce stock, create the shipment, send email) are error-prone, and concurrent orders can drive stock negative.
- Renting servers that run 24/7 adds fixed cost even though a small shop's traffic is uneven.
- An AI chatbot built on a dedicated vector database (such as Amazon OpenSearch Serverless) costs hundreds of USD per month in fixed fees, which does not fit a shop of this size.

**The solution**

- A full e-commerce website: parts catalog filtered by bike model, cart, checkout, a customer "Garage" of saved bikes, and an admin area.
- A backend split into Lambda microservices; post-order steps run asynchronously through EventBridge and SQS with dead-letter queues (DLQs), so one failing step does not break the whole order.
- Atomic stock updates using the DynamoDB condition `stock >= :qty`, so stock never goes negative under concurrent orders.
- A RAG chatbot without OpenSearch: vectors are stored as JSON files in S3, loaded into Lambda memory, and refreshed automatically from DynamoDB Streams when product data changes.

**Benefits and value**

- Customers browse and buy parts 24/7; the chatbot reduces repeated questions for staff.
- Orders flow automatically from payment to shipment and email, cutting manual work.
- Estimated running cost is about 12–13 USD per month at medium scale (section 6), mostly for the AI model, with a budget alert in place.
- Infrastructure as code (Terraform) rebuilds the whole environment quickly and consistently.

### 3. Solution Architecture

![Phu Tung Xe May Platform architecture](/images/2-Proposal/phutungxemay_architecture.png)

**Website request flow**

1. The browser hits Amazon CloudFront. `/_next/*` and product images come from Amazon S3; dynamic pages are server-rendered (SSR) by a Lambda function running Next.js through the OpenNext adapter.
2. `/api/*` passes through a CloudFront Function that strips the `/api` prefix, then reaches API Gateway REST with a Cognito Authorizer.
3. API Gateway calls 5 microservices: Catalog, Cart, Order, Payment Webhook (VNPay IPN), and User Profile; data lives in the DynamoDB tables Products, Orders, Carts, and Users.

**Order processing flow (event-driven)**

1. The Order Service and Payment Webhook publish events to an EventBridge custom bus.
2. EventBridge rules route events to 3 SQS queues, each with its own DLQ.
3. Three workers handle them: the Inventory Worker reduces or restores stock, the Shipping Worker creates GHN API v2 shipments (normalizing Vietnamese addresses with fuzzy matching), and the Notification Worker sends HTML invoice emails through Gmail SMTP.

**AI chatbot flow (RAG)**

1. Customers chat over API Gateway WebSocket, authenticated with a Cognito JWT through a Lambda Authorizer.
2. The Chat Receiver Lambda accepts the message and asynchronously invokes the Chat Orchestrator Lambda (packaged as a container image).
3. The Orchestrator runs hybrid search (Gemini embeddings plus BM25) over the vectors in S3, reads stock and carts from DynamoDB, then calls Gemini to answer, with a fallback mode when Gemini is overloaded.
4. When a customer needs a person, the Orchestrator publishes a `HumanHandoverRequested` event to EventBridge so the Notification Worker emails staff.
5. The Sync-to-KB Lambda reads DynamoDB Streams and updates the product vectors in S3 whenever data changes.

**AWS services used**

| Service | Role |
|---|---|
| Amazon CloudFront | CDN routing static, SSR, and API traffic; a CloudFront Function strips `/api` |
| Amazon S3 | Next.js static assets, part images, chatbot vector files |
| AWS Lambda | Next.js SSR (Node.js 22), 5 microservices, 3 workers, 4 chatbot functions (Python 3.12) |
| Amazon API Gateway | REST API for the website, WebSocket API for the chatbot |
| Amazon Cognito | Sign-up, sign-in, Admins and Customers groups |
| Amazon DynamoDB | Products, Orders, Carts, Users, chatbot sessions; on-demand mode |
| Amazon EventBridge | Event bus for order events and staff handover requests |
| Amazon SQS | 3 processing queues and 3 DLQs |
| AWS Secrets Manager | VNPay and GHN keys, SMTP password, Gemini key (KMS-encrypted) |
| Amazon CloudWatch, Amazon SNS | DLQ and error alarms, monitoring dashboard, email alerts |
| AWS Budgets | Alert when monthly cost passes a threshold (10 USD by default) |
| IAM, IAM Access Analyzer | Per-Lambda roles and automated permission review |

**External services:** VNPay Sandbox (payments, HMAC-SHA512 signatures), Giao Hang Nhanh API v2 (shipping), Gmail SMTP (email), Google Gemini API (embeddings and language model).

### 4. Technical Implementation

**Technology**

- *Frontend:* Next.js 16 (App Router, Server and Client Components, streaming SSR), Tailwind CSS v4, TypeScript, deployed with `@opennextjs/aws`.
- *Backend:* Python 3.12 on Lambda, with REST routes scoped by role (Public, Customer, Admin).
- *Chatbot:* Gemini `gemini-embedding-001` (768 dimensions) for embeddings, BM25 combined with cosine similarity, function calling for the `add_to_cart` and `request_human_agent` tools, and rate limits per session, connection, and day.
- *Infrastructure:* Terraform manages every resource (about 3,600 lines of HCL, including 13 Lambda functions, 6 DynamoDB tables, 7 SQS queues, 4 secrets, and 6 CloudWatch alarms).
- *CI/CD:* GitHub Actions with 3 independent workflows: `frontend.yml` (OpenNext build, S3 sync, SSR Lambda update, CloudFront invalidation), `backend.yml` (package and update the Python Lambdas), and `terraform.yml`.

**Key technical mechanisms**

- Atomic stock updates with a `ConditionExpression` so stock never goes negative.
- VNPay signature verification with HMAC-SHA512 and idempotent IPN handling to avoid duplicate orders.
- Every queue has a DLQ and a CloudWatch alarm when failed messages arrive.
- Secrets moved from SSM Parameter Store to KMS-encrypted Secrets Manager; S3 buckets block public access and require HTTPS.
- Offline tests for the chatbot and security tests for the microservices.

### 5. Timeline and Milestones

| Phase | Dates | Scope |
|---|---|---|
| Kickoff | 30/10/2026 | Next.js frontend, frontend Terraform infrastructure, and CI/CD |
| Backend and event-driven | 30/10 – 10/11/2026 | 5 microservices, EventBridge, SQS with DLQs, 3 workers, VNPay, GHN, and Gmail SMTP integration |
| Chatbot | 01/11 – 13/11/2026 | Bring the chatbot into Terraform, CDC sync from DynamoDB to S3 vectors, merge handover emails into the Notification Worker |
| Security and monitoring | 10/11 – 16/11/2026 | Require sign-in at checkout, Cognito Authorizer for WebSocket, IAM Access Analyzer, move secrets to Secrets Manager |
| Finalization | 17/11 – 14/12/2026 | End-to-end and load testing, cost tuning, demo preparation, and handover documentation |

### 6. Budget Estimation

The estimate uses a medium-scale monthly scenario: **30,000 page views, 300,000 API calls, 1,000 orders, and 10,000 chatbot questions**, in Singapore. The chatbot figures come from the team's AWS Pricing Calculator estimate ([estimate link](https://calculator.aws/#/estimate?id=d87e34b9f9a1e9f419182c4da7f6281aa720795f)); the other services use AWS's published prices and should be confirmed in the Pricing Calculator before finalizing.

| Service | Assumption | Estimate (USD/month) |
|---|---|---|
| Amazon CloudFront | About 60 GB and 1 million requests, within the free 1 TB and 10 million requests per month | 0.00 |
| Amazon S3 | A few GB of static assets and images | 0.10 |
| AWS Lambda (website) | About 0.4 million invocations and 70,000 GB-seconds, within the free 1 million invocations and 400,000 GB-seconds | 0.00 |
| Amazon API Gateway REST | 300,000 requests at 3.50 USD per million | 1.05 |
| Amazon DynamoDB | On-demand, about 600,000 reads and 50,000 writes | 0.20 |
| Amazon SQS, EventBridge | A few thousand events, within the SQS free tier | 0.00 |
| Amazon Cognito | Under 10,000 monthly active users (free) | 0.00 |
| AWS Secrets Manager | 4 secrets at 0.40 USD each | 1.60 |
| CloudWatch, SNS, Budgets | 6 alarms, 1 dashboard, small logs, within the free tier | 0.00 |
| Chatbot on AWS | WebSocket, Lambda, DynamoDB, S3 at 10,000 questions (Pricing Calculator, before free tier) | 1.92 |
| **AWS subtotal** | | **about 4.9** |
| Google Gemini API (outside AWS) | Embeddings and answers for 10,000 questions at assumed ceiling prices | about 7.5 |
| **Total** | | **about 12–13** |

- Terraform already creates an AWS Budgets alert with a default threshold of **10 USD per month** for the AWS part. Gemini cost sits outside AWS and must be tracked separately with Google.
- At low traffic almost every service stays within the free tier; the main fixed cost is Secrets Manager (1.60 USD).
- Compared with OpenSearch Serverless for the chatbot (about 175–350 USD per month in fixed cost alone), the S3-based vector design saves most of the cost.

### 7. Risk Assessment

| Risk | Impact | Likelihood | Mitigation |
|---|---|---|---|
| Gemini overload or rate limits (429, 503, 504 errors) | Medium | Medium | Model fallback chain, fallback answers from local data, warm up Lambda before demos |
| Cost overrun | Medium | Low | AWS Budgets, daily chatbot question cap, DynamoDB on-demand |
| Order processing failures (GHN or SMTP errors) | High | Low | SQS with DLQs, CloudWatch alarms through SNS, redrive messages from DLQs |
| Overselling under concurrent orders | High | Low | Conditional atomic stock updates in DynamoDB |
| Leaked API keys or payment secrets | High | Low | KMS-encrypted Secrets Manager, least-privilege IAM, IAM Access Analyzer, no keys in the repo |
| High latency for users in Vietnam | Low | Medium | CloudFront uses `PriceClass_100` (edges only in North America and Europe); move to `PriceClass_200` to use Asian edges |
| Chatbot cold start (about 7 seconds) | Low | Medium | Right-sized Lambda memory, scheduled warm-up before peak hours |

**Suggested improvement:** CI/CD currently uses IAM access keys stored in GitHub Secrets; switching to **OIDC with an IAM role** removes long-lived keys.

### 8. Expected Outcomes

- A fully working e-commerce site from browsing to payment, shipment, and email, running entirely serverless.
- A chatbot that answers policy and product questions correctly, adds to the cart, and hands over to staff. Version v6 scored KB 6/6, products 12/12, and out-of-scope 4/4 in end-to-end testing, averaging about 2.5 seconds per question.
- Infrastructure fully reproducible with Terraform, deployed automatically with GitHub Actions, with monitoring and cost alerts.
- Running cost of about 12–13 USD per month at medium scale, close to zero at low traffic.
- A base for future growth: more payment methods, more shipping carriers, or moving the chatbot to Amazon Bedrock when needed.
