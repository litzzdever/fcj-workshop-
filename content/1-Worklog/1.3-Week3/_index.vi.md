---
title: "Worklog Tuần 3"
date: 2026-09-30
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

* Đọc kế hoạch dự án chatbot và chốt thứ tự làm của AI Engineer: dựng knowledge base RAG trước, không train model và không chờ API website.
* Thực hành đường dữ liệu RAG trên AWS: S3 → chunk → embedding → OpenSearch Serverless vector search.
* Thử Amazon Bedrock Titan Embeddings, xử lý lỗi IAM và hạn chế tài khoản mới.
* Hoàn thành retrieval demo bằng model local `BAAI/bge-m3` khi Bedrock chưa được mở.
* Đọc lab Lightsail Container và bài so sánh Lightsail với EC2 để nối phần website với phần RAG.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 4 | - Đọc planning dự án, chốt làm RAG trước thay vì train model hoặc chờ API chatbot <br> - Chuẩn bị tài liệu FAQ, chính sách đổi trả, chính sách vận chuyển; tách notebook `01_chunking`, `02_s3_upload`, `03_embed_opensearch` <br> - Tạo bucket `team-chatbot-kb-dev-999`, upload 3 file vào `knowledge-base/`; xử lý `InvalidAccessKeyId` / `InvalidClientTokenId` bằng access key mới của user `chatbot-dev` <br> - Tạo OpenSearch Serverless collection `chatbot-kb-collection` (vector search, public, Singapore) và index `kb-index` (embedding 1024 chiều, `cosinesimil`) <br> - Thử Bedrock `amazon.titan-embed-text-v2:0` và v1: thêm IAM vẫn bị `NOT_AUTHORIZED` / `Operation not allowed`; agreement không hỗ trợ model này; mở support case Account and billing | 30/09/2026 | 30/09/2026 | Planning dự án, console S3 / IAM / OpenSearch / Bedrock |
| 5 | - Đổi embedding sang `BAAI/bge-m3` trên Colab để giữ dimension 1024, không tạo index 384 <br> - Sửa quyền OpenSearch: thêm `chatbot-dev` vào data access policy `chatbot-kb-data-access`, tạo inline policy `AOSS-API-Access` (`aoss:APIAccessAll`); đổi signer từ `AWS4Auth` sang `AWSV4SignerAuth` để bulk không còn 403 <br> - Tối ưu retrieval: chunk 120 từ, overlap 20, gắn tiêu đề có dấu; xóa và tạo lại index (bỏ field `engine`); đợi shard sẵn sàng rồi ghi 6 document <br> - Search câu "Chính sách đổi trả như thế nào?" ra đúng `chinh_sach_doi_tra.txt` ở hit 1 và 2 (score 0.8171 và 0.7776) <br> - Viết lại notebook 03 bản sạch và báo cáo kiến trúc UTF-8 | 01/10/2026 | 01/10/2026 | Notebook `03_embed_opensearch.ipynb` |
| 7 | - Đọc lab Amazon Lightsail Container: Preparation, tạo Container Service, deploy public image, tự build image (instance, AWS CLI, Docker, build, push, deploy), clean up <br> - Đọc bài so sánh Lightsail và EC2: Lightsail là gói tích hợp, giá cố định, không private subnet và không auto scale; EC2 tự quản VPC, Auto Scaling, trả theo mức dùng <br> - Chốt ranh giới dự án: website có thể chạy Lightsail Container; RAG vẫn dùng S3, OpenSearch và sau này Bedrock | 03/10/2026 | 03/10/2026 | <https://000046.awsstudygroup.com/> <br> <https://repost.aws/knowledge-center/lightsail-differences-from-ec2> |

### Kết quả đạt được tuần 3:

* Chốt được hướng làm của AI Engineer: RAG trước, không fine-tune model ở tuần này và không chờ API website.

* Dựng xong knowledge base trên S3 (`team-chatbot-kb-dev-999/knowledge-base/`) và vector index `kb-index` trên OpenSearch Serverless collection `chatbot-kb-collection`.

* Xác định Bedrock Titan bị chặn ở tầng tài khoản mới (`authorizationStatus = NOT_AUTHORIZED`, agreement không hỗ trợ). Đã mở support case Account and billing, không mất thêm thời gian sửa IAM.

* Có pipeline thay thế chạy được trên Colab: đọc S3, chunk, embed `BAAI/bge-m3` 1024 chiều, bulk vào OpenSearch, search knn.

* Tối ưu chunk và tiêu đề có dấu để câu hỏi đổi trả ra đúng chính sách đổi trả, score 0.8171 và 0.7776.

* Nắm khác biệt Lightsail và EC2, và vị trí lab Lightsail Container so với phần chatbot RAG.

* Debug được chuỗi lỗi thực tế:
  * Access key sai hoặc placeholder (`InvalidClientTokenId`)
  * Console OpenSearch đang ở sai region nên không thấy collection
  * `client.info()` 404 là bình thường trên Serverless
  * 403 do thiếu data access policy và `aoss:APIAccessAll`
  * `AWS4Auth` ghi bulk bị 403, `AWSV4SignerAuth` ghi được
  * `delete_by_query` không hỗ trợ (404)
  * Create index lỗi 400 nếu để field `engine`
  * Bulk 500 ngay sau create vì shard chưa sẵn sàng (`shards_acknowledged = False`)
  * Search sai chủ đề vì file không dấu và chunk 400 từ quá lớn
