---
title: "Proposal"
date: 2026-11-03
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Conversational Commerce Agent
## A RAG Chatbot for Motorcycle-Parts Advice on AWS Serverless

### 1. Overview of the Problem and the Solution

Our team's motorcycle-parts e-commerce website needs an online sales assistant that works 24/7: it should answer questions about price and stock, tell customers which part fits their bike, explain the warranty, return and shipping policies, add items to the cart, and hand the conversation to a human agent when needed.

The solution is a RAG (Retrieval-Augmented Generation) chatbot that runs entirely on pay-per-use AWS services (serverless) together with the Google Gemini API. For each question, the chatbot retrieves the relevant products and policy passages, then asks Gemini to write a Vietnamese answer based only on what was retrieved, so it does not invent prices or stock levels. The website connects to the chatbot through a single WebSocket endpoint.

What changed from the first design: the first version used Amazon OpenSearch Serverless and an embedding model running inside Lambda (a fixed cost of roughly USD 175–350 per month). The current version drops OpenSearch, keeps vectors in JSON files on S3 and uses Gemini to create embeddings, so the fixed cost is close to zero and the cost grows with real usage.

Current status: version v6 runs in the team's AWS account (region ap-southeast-1) and passed the full end-to-end test on 30 Oct 2026. What remains are three improvements requested by our team leader (section 5).

### 2. Goals and Scope

**Context and users**

- Customers of the motorcycle-parts website, who need fast advice based on their bike model and year.
- Customer-support staff, who want to receive only the requests the bot cannot solve, with full context.
- The development team, who need a cheap, easy-to-operate system that plugs into the existing backend and frontend.

**Specific goals and completion criteria**

| Goal | Measurable criterion | Current result |
|---|---|---|
| Correct policy answers | 6/6 test questions on warranty, returns and shipping answered correctly with the source document | 6/6 |
| Product advice grounded in data | 12/12 vehicle-based test questions return the right product IDs | 12/12 |
| No made-up answers off topic | 4/4 off-topic questions (weather, poem, bitcoin, cooking) get no product suggestions and create no ticket | 4/4 |
| Safe cart handling | Quantities above stock or above 10 are refused, and the bot never changes the customer's quantity | Passed |
| Human handover | Staff receive exactly one Gmail message with a ticket ID and context | Passed |
| Fast responses | About 2–3 seconds per question when Gemini is healthy | About 2–3 seconds |
| Low cost | At most USD 10 per month at 10,000 questions per month | About USD 9.4 (see section 6) |

**In scope**

- Product and policy Q&A, matching the customer's saved vehicle (Garage), cart (add, view), email handover to staff, monitoring and alerts, abuse limits, and an operations guide.

**Out of scope**

- Payment, creating or cancelling orders, image input from customers, and languages other than Vietnamese and English.

**Fit with the Cloud and AWS direction.** The whole chatbot uses pay-per-use serverless services: API Gateway, Lambda, DynamoDB, S3, EventBridge, Systems Manager Parameter Store and CloudWatch.

### 3. Problems to Solve

1. **Customers struggle to pick the right part.** The same kind of part differs by bike model and year. Customers often buy the wrong item and return it, and staff keep answering the same questions.
2. **Nobody is available after hours.** Questions about price, stock and policy arrive at any time.
3. **AI chatbots tend to make things up.** In sales, a wrong price or a false "in stock" causes real damage. The system must answer only from the shop's own data.
4. **Infrastructure cost can exceed the value of the chatbot.** The first version used OpenSearch Serverless and a private network (VPC, NAT) with a fixed cost of hundreds of USD per month, which does not suit a small project. Our leader set a target of USD 5–10 per month.
5. **A human must step in at the right moment.** When a customer complains or the bot cannot help, staff need the context without asking again.
6. **The system must be operable.** The free Gemini tier is often overloaded in the evening, so the system needs fallbacks, rate limits and alerts.

### 4. Preliminary Solution Architecture

![Frame 3: RAG Chatbot](/images/2-Proposal/chatbot_architecture.png)

**Question-answering flow (left to right in the diagram):**

1. The website sends the question through **API Gateway (WebSocket)**.
2. **ChatReceiverHandler** receives it and invokes **ChatOrchestratorHandler** asynchronously, so the connection is not held while the AI works.
3. **ChatOrchestratorHandler** checks rate limits, creates a vector for the question with **Gemini Embedding**, searches the vector store on **S3** (loaded into memory), adds the latest price and stock, the customer's Garage and the conversation history from **DynamoDB**.
4. The orchestrator calls **Gemini** to write the answer. Gemini can call tools for the cart or for human handover, and every tool is checked again in code.
5. The answer is pushed back to the customer's connection through API Gateway.
6. The Gemini key is read from **SSM Parameter Store**, never from code.

**Handover flow:** the orchestrator emits an event to **EventBridge** → **HandoverMailerHandler** builds the email → it is sent through **Gmail SMTP** to the staff mailbox.

**Monitoring:** CloudWatch watches Lambda, DynamoDB and WebSocket errors, latency and Gemini failures, and alerts the team by email through SNS.

**AWS services used**

| Service | Role |
|---|---|
| Amazon API Gateway (WebSocket) | Receives messages and pushes answers back to the customer |
| AWS Lambda (3 functions) | `chat-receiver`, `chat-orchestrator`, `chat-handover-mailer` |
| Amazon S3 | Vector store for documents and products (`kb.json`, `products.json`) |
| Amazon DynamoDB | Products, carts, users, conversation history, rate-limit counters |
| AWS Systems Manager Parameter Store | Holds the Gemini key and the Gmail app password (free, replaces Secrets Manager) |
| Amazon EventBridge | Routes human-handover events |
| Amazon CloudWatch and Amazon SNS | Alerts for errors, latency and Gemini overload |
| AWS IAM | One role per Lambda with least privilege |
| Google Gemini API (outside AWS) | Creates embeddings and writes answers |

**Three improvements in progress, following our leader's feedback**

- **Package Lambda as a .zip instead of Docker and ECR.** After removing torch, I measured the real package: 39 MB unzipped and 10.9 MB zipped, well within Lambda's limits (250 MB unzipped, 50 MB zipped). This removes ECR and the image build step.
- **Frame 4: automatic vector sync.** Instead of running `build_vector_snapshot.py` by hand, enable DynamoDB Streams on the Products table so that a sync Lambda creates embeddings with Gemini and updates the vector files on S3 whenever a product changes. Policy documents uploaded to S3 trigger the same update.
- **Write the infrastructure in Terraform** following the project's structure (adding chatbot files such as `lambda_chatbot.tf`, `iam_chatbot.tf`, `eventbridge.tf` and `monitoring.tf`), replacing the CloudFormation files used today.

### 5. Plan and Timeline

| Phase | When | Result |
|---|---|---|
| Design and first version (OpenSearch Serverless) | Until 24 Oct 2026 | Working chatbot, but high fixed cost |
| Move to v2 without OpenSearch, deployed in the team account | 25–27 Oct | Vectors on S3, Gemini embeddings, 3 Lambdas running |
| Gmail SMTP handover and cleanup of the old account | 28 Oct | Real email instead of SNS, OpenSearch removed from the old account |
| Prompt and fallback tuning (v6), end-to-end testing | 30 Oct | 39 offline tests pass; WebSocket tests pass |
| Operations guide and this proposal | 30 Oct–3 Nov | Step-by-step guide following Frame 3, this document |
| Leader's feedback: .zip packaging, Frame 4 auto sync, Terraform | Planned for the week of 3–9 Nov | Infrastructure in the project's standard, no ECR, vectors update themselves |
| Connect the chatbot to the website and test with real users | After the steps above | Running on the team's website |
| Authentication with a Lambda authorizer (Cognito); enable Gemini billing before the demo | Before the demo | Customer IDs cannot be spoofed; no overload at peak time |

### 6. Budget Estimation and Cost (Cost Estimation)

**Method.** The estimate uses the published AWS and Google price lists, a realistic load scenario and measurements from the running chatbot. AWS unit prices come from the AWS pricing pages (US East), plus a 15% buffer for the difference to the Singapore region; final numbers should be cross-checked with the [AWS Pricing Calculator](https://calculator.aws/).

**AWS Pricing Calculator estimate (Singapore, 10,000 questions/month scenario):** [https://calculator.aws/#/estimate?id=d87e34b9f9a1e9f419182c4da7f6281aa720795f](https://calculator.aws/#/estimate?id=d87e34b9f9a1e9f419182c4da7f6281aa720795f)

**Assumptions per question (measured on the running chatbot)**

| Parameter | Value |
|---|---|
| WebSocket messages | 2 (1 in, 1 out) |
| WebSocket connection minutes | 1 (conservative: one connection per question) |
| Orchestrator Lambda | 1,024 MB, about 2.5 s, i.e. 2.5 GB-seconds |
| Other Lambdas | 2 invocations, negligible |
| DynamoDB | 5 writes, 6 reads (including rate-limit counters, history and cart) |
| Gemini | About 2,500 input tokens, 200 output tokens, 30 embedding tokens |

**Unit prices used**

| Service | Unit price |
|---|---|
| Lambda | USD 0.20 per 1 million requests; USD 0.0000166667 per GB-second; free tier of 1 million requests and 400,000 GB-seconds per month |
| API Gateway WebSocket | USD 1.00 per 1 million messages; USD 0.25 per 1 million connection minutes |
| DynamoDB on-demand | USD 0.625 per 1 million writes; USD 0.125 per 1 million reads; 25 GB of storage free |
| S3 | Vector data is under 1 MB so storage is almost free; about 8,640 version-check requests per month |
| SSM Parameter Store (Standard), SNS email, the first 10 CloudWatch alarms, EventBridge (a few dozen events) | Within the free tier or negligible |
| Gemini (ceiling assumption, to be re-checked on Google's pricing page) | USD 0.25 per 1 million input tokens; USD 1.50 per 1 million output tokens; embeddings USD 0.15 per 1 million tokens |

**Estimate for three scenarios (USD per month)**

| Item | Demo: 1,000 questions | Trial: 10,000 questions | Growth: 100,000 questions |
|---|---|---|---|
| Lambda (within the free tier) | 0.00 | 0.00 | 0.00 |
| API Gateway WebSocket | 0.00 | 0.02 | 0.23 |
| DynamoDB | 0.00 | 0.04 | 0.39 |
| S3 | 0.01 | 0.01 | 0.01 |
| CloudWatch, SNS, SSM, EventBridge | 0.00 | 0.00 | 0.00 |
| Region buffer (+15%) | 0.00 | 0.01 | 0.09 |
| **AWS subtotal** | **about 0.02** | **about 0.09** | **about 0.72** |
| Gemini (ceiling price) | 0.93 | 9.30 | 92.95 |
| **Total** | **about 0.95** | **about 9.38** | **about 93.67** |

How to read the table:

- The AWS part is almost negligible; Gemini is the main cost. With a cheaper model (assuming USD 0.10 and 0.40 per 1 million input and output tokens) the totals are about 0.36, 3.43 and 34.17.
- If only the free Gemini tier is used during the demo phase, the Gemini cost is zero, but that tier is often overloaded in the evening.
- The Lambda free tier is shared by the whole account. If the team's other Lambdas have used it up, the chatbot's Lambda cost is about 0.05, 0.43 and 4.25 for the three scenarios.
- The 12-month free tier of API Gateway is only for new AWS accounts; the table uses full prices.

**Comparison with the old architecture.** OpenSearch Serverless costs USD 0.24 per OCU-hour, with a minimum of 2 OCUs for production (about USD 350 per month) or 1 OCU for dev/test (about USD 175 per month), before counting a NAT Gateway and the private network. The current version brings the fixed cost close to zero, and at 10,000 questions per month it still falls within our leader's USD 5–10 target.

**Cost optimization strategy**

1. **Drop OpenSearch and keep vectors on S3** (done): avoids USD 175–350 per month.
2. **No VPC and no NAT Gateway** (done): Lambda calls Gemini directly over the Internet.
3. **SSM Parameter Store instead of Secrets Manager** (done): Secrets Manager charges per secret per month and per API call, while SSM Standard is free.
4. **Do not call the AI when it is not needed** (done): off-topic questions get a built-in reply; viewing the cart reads DynamoDB directly without Gemini.
5. **Fewer tokens per question**: only 3 products and 3 document passages are sent, and the last 6 messages are kept; questions are capped at 500 characters (done).
6. **.zip packaging removes ECR and the image build** (in progress).
7. **Spending cap that matches the budget.** The current cap of 2,000 questions per day means up to about USD 18 per day at the ceiling Gemini price. To stay within USD 10 per month, lower the cap to about 350–400 questions per day with the `RL_GLOBAL_PER_DAY` environment variable, and enable AWS Budgets (not yet enabled because of missing permissions).
8. **Data lifecycle.** Conversation sessions, rate-limit counters and carts expire automatically with DynamoDB TTL (the `expires_at` attribute). Set CloudWatch log retention to 14–30 days and add an S3 Lifecycle Policy that deletes old Lambda packages after 30 days (to be added in Terraform).
9. **Use the free Gemini tier while testing**, enable billing only before the real demo, and consider a cheaper model if load grows.

**Clean-up procedure after the workshop**

Follow these steps in order to avoid unexpected charges. Do not delete the `Products`, `Users` and `Carts` tables because they are shared with the team's application.

1. Delete the stacks (currently CloudFormation):
   ```bash
   aws cloudformation delete-stack --stack-name chatbot-ops
   aws cloudformation delete-stack --stack-name chatbot-mailer
   aws cloudformation delete-stack --stack-name chatbot-v2
   ```
   If the project has moved to Terraform, run `terraform destroy` inside the `terraform/` folder.
2. Empty and delete the vector bucket and the Lambda package bucket:
   ```bash
   aws s3 rb s3://<vector-bucket-name> --force
   ```
3. Delete the log groups (stacks do not remove logs created by Lambda):
   ```bash
   aws logs delete-log-group --log-group-name /aws/lambda/chat-receiver
   aws logs delete-log-group --log-group-name /aws/lambda/chat-orchestrator-v2
   aws logs delete-log-group --log-group-name /aws/lambda/chat-handover-mailer
   ```
4. Delete the Gemini key and the Gmail app password in SSM: `aws ssm delete-parameter --name /chatbot/gemini_api_key` (and the Gmail password parameter).
5. If Docker is still used: `aws ecr delete-repository --repository-name chat-orchestrator-v2 --force`.
6. If a budget was created: `aws budgets delete-budget`.
7. Check that nothing billable is left:
   ```bash
   aws ec2 describe-nat-gateways
   aws ec2 describe-addresses
   aws opensearchserverless list-collections
   ```
8. Revoke the Gemini key in Google AI Studio and turn off Gemini billing if it was enabled.

### 7. Risk Assessment

| Risk | Impact | Probability | Mitigation |
|---|---|---|---|
| Gemini overloaded or out of quota (especially the free tier in the evening) | High | High | Chain of 3 fallback models, temporary circuit breaker, a fallback answer that only shows search results; alert on repeated Gemini errors; enable billing before the demo |
| Cost exceeds the estimate (Gemini is billed per token) | Medium | Medium | Lower the daily question cap, enable AWS Budgets, cap tokens per question, review weekly |
| Bot invents prices or stock | High | Low | Answer only from retrieved data; re-read price and stock from DynamoDB on every question; off-topic tests |
| Bot recommends a part that does not fit | Medium | Medium | The prompt requires checking the "fits vehicle" field; 12 vehicle-based tests; future work: a code-level compatibility check before adding to the cart |
| Vector data out of date | Medium | Medium | Price and stock are always read fresh; Frame 4 syncs vectors when a product changes |
| Customer ID spoofing to use someone else's cart | High | Low in the demo, high in production | A Lambda authorizer (Cognito JWT) is ready to take the customer ID from the token; enable it before going live |
| Abuse or flooding with questions | Medium | Medium | Limits per session, per connection and per day; question length limit |
| API key leak | High | Low | Keys live only in SSM, never in code or the repository; rotate the key if a leak is suspected |
| Missing permissions in the team's AWS account (for example Budgets, Streams) | Low | Medium | Use `preflight.sh` to check permissions before deploying; create each part independently |
| Lost or duplicated handover emails | Medium | Low | Only one request per session every 10 minutes; ticket ID for tracing; Lambda error alarm |

**Contingency plan.** If Gemini or AWS has a long outage, the website still shows the chat box and the bot switches to a fallback answer with the support hotline; important requests go through the human channel.

### 8. Expected Outcomes

- Customers get advice that matches their bike, the right price and the real stock within 2–3 seconds, 24/7.
- Support staff handle only the requests that truly need a person, with full context.
- Low running cost that grows with real usage: about USD 9.4 per month at 10,000 questions.
- Infrastructure managed as code (Terraform) in the same standard as the rest of the project, easy to rebuild and to clean up.
