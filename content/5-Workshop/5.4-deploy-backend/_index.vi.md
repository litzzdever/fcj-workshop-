---
title : "Triển khai & Kiểm thử Backend"
date : 2026-11-22
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

Trong phần này, chúng ta sẽ cấu hình khóa bí mật trên **AWS Secrets Manager**, đóng gói và triển khai toàn bộ **12 hàm AWS Lambda Backend** (5 REST Microservices, 3 Event-Driven Workers và 4 Lambda của cụm Chatbot RAG), sau đó kiểm thử luồng mua sắm E-Commerce và luồng tư vấn AI thời gian thực qua WebSocket.

#### Nội dung chính
1. [Triển khai Backend (Microservices, Workers & Chatbot RAG)](5.4.1-deploy-backend/)
2. [Kiểm thử Backend Website Serverless](5.4.2-test-severless-website/)
3. [Kiểm thử Chatbot AI (RAG Full-Duplex)](5.4.3-test-chatbot/)
