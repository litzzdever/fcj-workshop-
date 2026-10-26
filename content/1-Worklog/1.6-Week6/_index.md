---
title: "Week 6 Worklog"
date: 2026-10-21
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

* Maintain and improve the RAG chatbot for a motorcycle-parts shop on AWS, using API Gateway WebSocket, Lambda, OpenSearch Serverless, DynamoDB, and Gemini.
* Fix an infrastructure incident and keep the WebSocket endpoint stable for the team.
* Measure the latency of the first question after an idle period (cold start).
* Redesign the chatbot framework to match the running system and test the full operating flow.
* Record documentation, diagrams, and costs based on the system as it actually runs.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Wed | - Tried adding a Mock `$connect` route to the API Gateway WebSocket API; every connection returned a 500 error, and the `chatbot-ws` API was then deleted by mistake <br> - Recreated the `chatbot-ws` API: route selection `$request.body.action`, route `sendMessage` attached to Lambda `chat-receiver`, stage `dev`; switched to the new WebSocket URL <br> - Retested after recreation: a policy question took 3.7 seconds and a product question took 3.6 seconds on a warm instance <br> - Measured cold start after several idle days: 150.8 seconds in total, with model load 10.9 seconds, embedding 127.3 seconds, OpenSearch search 19.7 seconds, and Gemini 3.6 seconds. The cart question stayed fast (3.1 seconds) because its shortcut runs before embedding <br> - Read OpenSearch Serverless cost from Cost Explorer: about $16.98 from 9/30 to 10/17; light-use days were near $0, the two heavy-test days 10/16 and 10/17 cost $6.02 and $9.06, and on 10/19 the whole account was about $0.40 <br> - Set an ECR lifecycle policy that keeps the 3 newest images and a 2-week CloudWatch Logs retention <br> - Updated `README.md` (the message contract), the worklog, and the architecture diagram to match the real infrastructure: Lambda is not in a VPC and there is no NAT Gateway <br> - Lesson learned: do not try new configuration on a running stage; to try `$connect`, create a separate API | 10/21/2026 | 10/21/2026 | Internal chatbot project documents |
| Thu | - Redrew the chatbot framework diagram to match the running system: API Gateway WebSocket `chatbot-ws` → Lambda `chat-receiver` → Lambda `chat-orchestrator-img` (container image in ECR) → OpenSearch Serverless (`kb-index` for policy documents, `products-index` for products) → Gemini <br> - Listed the supporting components: DynamoDB (Products, Carts, Orders, Users, chatbot-sessions), Secrets Manager for the Gemini key, SNS for the staff-handover email, and EventBridge Scheduler for warmup <br> - Defined the processing paths: cart actions use a shortcut that skips embedding; policy and product-advice questions go through bge-m3 embedding and hybrid search (0.7 vector + 0.3 keyword); a request to talk to staff creates a ticket <br> - Compared with the team's VPC diagram: the chatbot is not in a VPC, so it has no NAT Gateway, VPC endpoint, or always-on OpenSearch node; the VPC diagram is labeled as the production target architecture <br> - Tested operations through the real WebSocket with the notebook `test_chatbot_answers.ipynb`, with response times on a warm system: return policy question 8.0 seconds; brake-pad advice by vehicle 5.1 seconds; follow-up about another vehicle to check context memory 5.1 seconds; Garage personalization 6.1 seconds; view cart 0.7 seconds; adding 20 front forks when stock is 8 was refused in 5.7 seconds; a guest adding to the cart was told to log in in 9.7 seconds; an out-of-scope question (weather) was politely declined in 6.1 seconds; a request for staff created a ticket and sent an email through SNS in 14.2 seconds <br> - Recorded three findings: the test cart of `USR_98765` still holds data from earlier tests and must be cleaned before the demo; an out-of-scope question still returns `low_confidence = false` even though the bot declines correctly; cold start must be handled before the demo | 10/22/2026 | 10/22/2026 | Internal chatbot project documents |

### Week 6 Achievements:

* Restored the WebSocket endpoint after the incident and retested it: a policy question took 3.7 seconds and a product question 3.6 seconds on a warm system.

* Measured a cold start of 150.8 seconds and found that most of the time is spent in embedding and OpenSearch search.

* Redrew the chatbot framework to match the running infrastructure (Lambda outside a VPC, no NAT) and compared it with the team's target VPC diagram.

* Tested 9 operating scenarios through the real WebSocket: policy, advice by vehicle, Garage, cart, over-stock, not logged in, out of scope, and staff handover; warm response times ranged from 0.7 to 14.2 seconds.

* Recorded OpenSearch Serverless cost from Cost Explorer and updated the README, worklog, and diagram to match the real system.
