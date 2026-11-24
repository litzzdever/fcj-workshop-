---
title: "Workshop"
date: 2026-11-22
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building a Serverless E-Commerce Platform & Full-Duplex AI RAG Chatbot on AWS

#### Overview

In this workshop, you will build and deploy an end-to-end **100% Serverless Motorcycle Spare Parts E-Commerce Platform** integrated with a **Real-Time Full-Duplex AI RAG Chatbot** on AWS in the **ap-southeast-1 (Singapore)** region.

The entire cloud infrastructure is automated via **Terraform (Infrastructure as Code)** following **Least Privilege** security principles and **Event-Driven Architecture (EDA)** best practices.

Throughout this lab, you will implement three core modern cloud pillars on AWS:
+ **Serverless Web & Microservices:** Deliver a **Next.js SSR** storefront via **Amazon CloudFront (+ Edge WAF)**, **Amazon S3**, and **AWS Lambda**; backed by **Amazon Cognito**, **API Gateway (REST API)**, and **Amazon DynamoDB**.
+ **Event-Driven Architecture (EDA):** Decouple asynchronous order fulfillment workflows using **Amazon EventBridge** and **Amazon SQS** (Inventory Worker for stock deduction, Shipping Worker integrated with GHN Sandbox, and Notification Worker sending emails via Gmail SMTP).
+ **Full-Duplex AI RAG Chatbot & CDC:** Build a real-time two-way chat channel using **API Gateway WebSocket** with a **Lambda Authorizer** verifying Cognito JWTs, in-memory vector search on **Amazon S3** powered by **Google Gemini 2.0 Flash**, and automated knowledge sync via **DynamoDB Streams**.

#### Contents

1. [Introduction & Architecture Overview](5.1-workshop-overview/)
2. [Prerequisites & Terraform Infrastructure Provisioning](5.2-prerequiste/)
3. [Deploy & Test Frontend on CloudFront](5.3-deploy-frontend/)
4. [Deploy & Test Backend, Event-Driven Pipeline & RAG Chatbot](5.4-deploy-backend/)
5. [Cleanup Resources](5.5-cleanup/)
