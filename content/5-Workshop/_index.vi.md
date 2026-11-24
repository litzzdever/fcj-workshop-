---
title: "Workshop"
date: 2026-11-22
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng Nền tảng Thương mại Điện tử Serverless & Chatbot AI RAG Full-Duplex trên AWS

#### Tổng quan

Trong bài workshop này, bạn sẽ từng bước xây dựng và triển khai hoàn chỉnh một **Nền tảng Thương mại Điện tử Phụ tùng Xe máy hoàn toàn Serverless** kết hợp **Trợ lý ảo AI RAG thời gian thực (Full-Duplex WebSocket)** trên nền tảng AWS tại region **ap-southeast-1 (Singapore)**.

Toàn bộ hạ tầng được tự động hóa 100% bằng **Terraform (Infrastructure as Code)** theo chuẩn bảo mật **Least Privilege (Đặc quyền tối thiểu)** và **Event-Driven Architecture (Kiến trúc hướng sự kiện)**.

Trong suốt bài lab, bạn sẽ thực hành 3 trụ cột kiến trúc hiện đại trên AWS:
+ **Serverless Web & Microservices:** Phân phối giao diện **Next.js SSR** qua **Amazon CloudFront (+ Edge WAF)**, **Amazon S3** và **AWS Lambda**; kết hợp **Amazon Cognito**, **API Gateway (REST API)** và **Amazon DynamoDB**.
+ **Event-Driven Architecture (EDA):** Tách rời các tác vụ xử lý đơn hàng bằng **Amazon EventBridge** và **Amazon SQS** (Inventory Worker trừ kho, Shipping Worker tích hợp GHN Sandbox, Notification Worker gửi email qua Gmail SMTP).
+ **Full-Duplex AI RAG Chatbot & CDC:** Xây dựng kênh chat 2 chiều thời gian thực qua **API Gateway WebSocket** với **Lambda Authorizer** xác thực JWT Cognito, tra cứu tri thức vector trên **Amazon S3** kết hợp **Google Gemini 2.0 Flash**, và đồng bộ dữ liệu tự động qua **DynamoDB Streams**.

#### Nội dung

1. [Giới thiệu & Tổng quan kiến trúc](5.1-workshop-overview/)
2. [Điều kiện tiên quyết & Khởi tạo hạ tầng Terraform](5.2-prerequiste/)
3. [Triển khai & Kiểm thử Frontend trên CloudFront](5.3-deploy-frontend/)
4. [Triển khai & Kiểm thử Backend, Event-Driven và Chatbot RAG](5.4-deploy-backend/)
5. [Dọn dẹp tài nguyên](5.5-cleanup/)
