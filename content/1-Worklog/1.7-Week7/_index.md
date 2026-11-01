---
title: "Worklog Tuần 7"
date: 2026-10-27
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

* Tìm hiểu AWS AppSync và cách xây dựng GraphQL API kết nối với Amazon DynamoDB.
* Thực hành các tutorial chính thức: mutation, query, blank API, subscription, merged APIs.
* Hoàn thành tutorial Post application với JavaScript resolvers (CRUD, vote, tags).
* Thực hành Lambda resolvers, local resolvers, combining resolvers và DynamoDB transactions.
* Tìm hiểu VTL resolver tutorials và Configuration & Settings của AppSync.
* Nắm vững Authorization & Authentication để bảo mật GraphQL API (API Key, Lambda, IAM, OpenID Connect, Cognito User Pools).

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 3 | - Thực hành mutation `createTodo` và query `getTodo` / `listTodos` trên Todo API mẫu <br> - Tạo blank API `MyBlankAPI`, viết schema, tạo bảng DynamoDB, gắn data source và JavaScript unit resolver cho `addObj` / `getObj` <br> - Thêm Subscription `onAddObj` với `@aws_subscribe`, kiểm thử realtime qua WebSocket <br> - Tạo Merged API kết hợp Todo API và MyBlankAPI <br> - Hoàn thành tutorial Post application: `addPost`, `getPost`, `updatePost`, `vote`, `deletePost`, `allPost`, `allPostsByAuthor`, `addTag` / `removeTag`, `allPostsByTag` <br> - Clean up các API và bảng DynamoDB không còn dùng | 27/10/2026 | 27/10/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html> |
| 4 | - Tạo Lambda function (Node.js 22.x) và API `LambdaTutorialAPI`; gắn Lambda data source, viết resolver `Invoke` cho `getPost`, `allPosts`, `addPost` <br> - Tạo API `LocalResolverAPI` với data source None, local resolver cho `publish`, kiểm thử subscription pub/sub <br> - Nghiên cứu Combining GraphQL resolvers: kết hợp DynamoDB, Lambda, OpenSearch trên cùng schema <br> - Thực hành DynamoDB Transactions: tạo 3 bảng `savingAccounts`, `checkingAccounts`, `transactionHistory`; viết `TransactWriteItems` cho `populateAccounts` và `transferMoney`, `TransactGetItems` cho `getAccounts` | 28/10/2026 | 28/10/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/tutorials-js.html> |
| 5 | - Tìm hiểu VTL resolver tutorials: post application, Lambda, OpenSearch, local, combining, batch, transactions, HTTP, Aurora Serverless, pipeline, Delta Sync <br> - So sánh VTL mapping templates với JavaScript resolvers (APPSYNC_JS); ghi nhận AWS ưu tiên JavaScript runtime cho dự án mới | 29/10/2026 | 29/10/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/tutorials.html> |
| 6 | - Tìm hiểu Configuration and Settings: Caching, Versioning GraphQL objects, Custom domain names, Private APIs (VPC), Sharing APIs qua AWS RAM <br> - Introspection và limits (query depth, resolver count), Environment variables, Logging (CloudTrail), Monitoring (CloudWatch), Tracing (X-Ray) | 30/10/2026 | 30/10/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/configuration-and-settings.html> |
| 7 | - Hoàn thành Configuring authorization and authentication <br> - Nắm 5 authorization types: API_KEY, AWS_LAMBDA, AWS_IAM, OPENID_CONNECT, AMAZON_COGNITO_USER_POOLS <br> - Multiple authorization modes, fine-grained access control qua identity context trong resolver, filtering theo owner/role, data source access theo least privilege <br> - Kết nối với Cognito User Pools đã học ở tuần 5 | 31/10/2026 | 31/10/2026 | <https://docs.aws.amazon.com/appsync/latest/devguide/security-authz.html> |

### Kết quả đạt được tuần 7:

* Hiểu kiến trúc AWS AppSync: Schema, Data Source, Resolver và cách phối hợp với DynamoDB.

* Thành thạo CRUD qua GraphQL mutation/query; viết JavaScript unit resolvers cho PutItem, GetItem, UpdateItem, DeleteItem, Scan, Query.

* Thực hành Subscription realtime với `@aws_subscribe` và local resolvers (None data source) cho pub/sub.

* Tạo Merged API; hoàn thành Post application với vote, tags (DynamoDB String Set) và query theo GSI.

* Triển khai Lambda resolvers và DynamoDB Transactions (`TransactWriteItems`, `TransactGetItems`) trên nhiều bảng.

* Tìm hiểu VTL tutorials, Configuration & Settings (caching, private API, monitoring) và năm loại authorization để bảo mật GraphQL API.

* Kết nối kiến thức với DynamoDB (tuần 4), Cognito (tuần 5) và mở rộng sang GraphQL API layer có bảo mật.
