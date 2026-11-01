---
title: "Week 7 Worklog"
date: 2026-10-27
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

* Learn AWS AppSync and how to build GraphQL APIs connected to Amazon DynamoDB.
* Practice official tutorials: mutations, queries, blank APIs, subscriptions, and merged APIs.
* Complete the Post application tutorial with JavaScript resolvers (CRUD, vote, tags).
* Practice Lambda resolvers, local resolvers, combining resolvers, and DynamoDB transactions.
* Study VTL resolver tutorials and AppSync Configuration & Settings.
* Master Authorization & Authentication to secure GraphQL APIs (API Key, Lambda, IAM, OpenID Connect, Cognito User Pools).

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Tue | - Practiced `createTodo` mutation and `getTodo` / `listTodos` queries on the sample Todo API <br> - Created blank API `MyBlankAPI`, wrote schema, created DynamoDB table, attached data source, and wrote JavaScript unit resolvers for `addObj` / `getObj` <br> - Added Subscription `onAddObj` with `@aws_subscribe` and tested realtime over WebSocket <br> - Created a Merged API combining Todo API and MyBlankAPI <br> - Completed the Post application tutorial: `addPost`, `getPost`, `updatePost`, `vote`, `deletePost`, `allPost`, `allPostsByAuthor`, `addTag` / `removeTag`, `allPostsByTag` <br> - Cleaned up unused APIs and DynamoDB tables | 10/27/2026 | 10/27/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html> |
| Wed | - Created a Lambda function (Node.js 22.x) and API `LambdaTutorialAPI`; attached Lambda data source and wrote `Invoke` resolvers for `getPost`, `allPosts`, `addPost` <br> - Created API `LocalResolverAPI` with a None data source and local resolver for `publish`; tested pub/sub subscription <br> - Studied Combining GraphQL resolvers: mixing DynamoDB, Lambda, and OpenSearch on the same schema <br> - Practiced DynamoDB Transactions: created three tables `savingAccounts`, `checkingAccounts`, `transactionHistory`; wrote `TransactWriteItems` for `populateAccounts` and `transferMoney`, and `TransactGetItems` for `getAccounts` | 10/28/2026 | 10/28/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/tutorials-js.html> |
| Thu | - Studied VTL resolver tutorials: post application, Lambda, OpenSearch, local, combining, batch, transactions, HTTP, Aurora Serverless, pipeline, Delta Sync <br> - Compared VTL mapping templates with JavaScript resolvers (APPSYNC_JS); noted that AWS recommends the JavaScript runtime for new projects | 10/29/2026 | 10/29/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/tutorials.html> |
| Fri | - Studied Configuration and Settings: Caching, Versioning GraphQL objects, Custom domain names, Private APIs (VPC), Sharing APIs via AWS RAM <br> - Introspection and limits (query depth, resolver count), Environment variables, Logging (CloudTrail), Monitoring (CloudWatch), Tracing (X-Ray) | 10/30/2026 | 10/30/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/configuration-and-settings.html> |
| Sat | - Completed Configuring authorization and authentication <br> - Covered five authorization types: API_KEY, AWS_LAMBDA, AWS_IAM, OPENID_CONNECT, AMAZON_COGNITO_USER_POOLS <br> - Multiple authorization modes, fine-grained access control via identity context in resolvers, owner/role-based filtering, and least-privilege data source access <br> - Linked back to Cognito User Pools from week 5 | 10/31/2026 | 10/31/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/security-authz.html> |

### Week 7 Achievements:

* Understood AWS AppSync architecture: Schema, Data Source, Resolver, and how they work with DynamoDB.

* Mastered CRUD through GraphQL mutations/queries; wrote JavaScript unit resolvers for PutItem, GetItem, UpdateItem, DeleteItem, Scan, and Query.

* Practiced realtime Subscriptions with `@aws_subscribe` and local resolvers (None data source) for pub/sub.

* Created a Merged API; completed the Post application with vote, tags (DynamoDB String Set), and GSI-based queries.

* Implemented Lambda resolvers and DynamoDB Transactions (`TransactWriteItems`, `TransactGetItems`) across multiple tables.

* Studied VTL tutorials, Configuration & Settings (caching, private API, monitoring), and the five authorization types for securing GraphQL APIs.

* Connected knowledge from DynamoDB (week 4) and Cognito (week 5) to a full GraphQL API layer with security.
