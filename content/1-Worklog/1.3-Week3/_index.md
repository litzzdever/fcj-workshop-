---
title: "Week 3 Worklog"
date: 2026-09-30
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Read the chatbot project plan and set the AI Engineer order of work: build the RAG knowledge base first, without training a model or waiting for the website API.
* Practice the RAG data path on AWS: S3 → chunk → embedding → OpenSearch Serverless vector search.
* Try Amazon Bedrock Titan Embeddings and handle IAM errors plus the new-account restriction.
* Complete a retrieval demo with the local model `BAAI/bge-m3` while Bedrock remains blocked.
* Read the Lightsail Container lab and the Lightsail vs EC2 comparison to connect the website tier with the RAG tier.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Wed | - Read the project plan and chose RAG first instead of training a model or waiting for the chatbot API <br> - Prepared FAQ, return policy, and shipping policy files; split notebooks `01_chunking`, `02_s3_upload`, `03_embed_opensearch` <br> - Created bucket `team-chatbot-kb-dev-999` and uploaded 3 files under `knowledge-base/`; fixed `InvalidAccessKeyId` / `InvalidClientTokenId` with a new access key for user `chatbot-dev` <br> - Created OpenSearch Serverless collection `chatbot-kb-collection` (vector search, public, Singapore) and index `kb-index` (1024-dimension embedding, `cosinesimil`) <br> - Tried Bedrock `amazon.titan-embed-text-v2:0` and v1: IAM was not enough; still `NOT_AUTHORIZED` / `Operation not allowed`; agreement is not supported for this model; opened an Account and billing support case | 09/30/2026 | 09/30/2026 | Project planning, S3 / IAM / OpenSearch / Bedrock consoles |
| Thu | - Switched embeddings to `BAAI/bge-m3` on Colab to keep the 1024 dimension and avoid a new 384-dimension index <br> - Fixed OpenSearch access: added `chatbot-dev` to data access policy `chatbot-kb-data-access`, created inline policy `AOSS-API-Access` (`aoss:APIAccessAll`); switched the signer from `AWS4Auth` to `AWSV4SignerAuth` so bulk no longer returned 403 <br> - Tuned retrieval: 120-word chunks, overlap 20, accented titles; deleted and recreated the index without the `engine` field; waited for shards, then indexed 6 documents <br> - Query "Chính sách đổi trả như thế nào?" returned `chinh_sach_doi_tra.txt` as hit 1 and hit 2 (scores 0.8171 and 0.7776) <br> - Rewrote notebook 03 as a clean notebook and wrote a UTF-8 architecture report | 10/01/2026 | 10/01/2026 | Notebook `03_embed_opensearch.ipynb` |
| Sat | - Read the Amazon Lightsail Container lab: Preparation, create a Container Service, deploy a public image, build and deploy a custom image (instance, AWS CLI, Docker, build, push, deploy), clean up <br> - Read the Lightsail vs EC2 comparison: Lightsail is an integrated fixed-price product with no private subnet and no auto scaling; EC2 uses a self-managed VPC, Auto Scaling, and pay-as-you-go pricing <br> - Set the project boundary: the website can run on Lightsail Container; RAG still uses S3, OpenSearch, and later Bedrock | 10/03/2026 | 10/03/2026 | <https://000046.awsstudygroup.com/> <br> <https://repost.aws/knowledge-center/lightsail-differences-from-ec2> |

### Week 3 Achievements:

* Set the AI Engineer direction: RAG first, no model fine-tuning this week, and no wait for the website API.

* Built the knowledge base on S3 (`team-chatbot-kb-dev-999/knowledge-base/`) and the vector index `kb-index` on OpenSearch Serverless collection `chatbot-kb-collection`.

* Identified that Bedrock Titan is blocked at the new-account layer (`authorizationStatus = NOT_AUTHORIZED`, agreement not supported). Opened an Account and billing support case instead of spending more time on IAM.

* Have a working Colab fallback: read S3, chunk, embed with `BAAI/bge-m3` at 1024 dimensions, bulk into OpenSearch, and run knn search.

* Tuned chunk size and accented titles so the return-policy question returns the return policy, with scores 0.8171 and 0.7776.

* Understood the Lightsail vs EC2 difference and where the Lightsail Container lab sits relative to the chatbot RAG work.

* Debugged a real error chain:
  * Wrong or placeholder access key (`InvalidClientTokenId`)
  * OpenSearch console on the wrong region, so the collection was not visible
  * `client.info()` 404 is normal on Serverless
  * 403 caused by a missing data access policy and missing `aoss:APIAccessAll`
  * `AWS4Auth` bulk returned 403; `AWSV4SignerAuth` succeeded
  * `delete_by_query` is not supported (404)
  * Create-index 400 when the `engine` field is set
  * Bulk 500 right after create because shards were not ready (`shards_acknowledged = False`)
  * Wrong search topic because the source files had no accents and 400-word chunks were too large
