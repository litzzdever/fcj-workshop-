---
title: "Worklog Tuần 11"
date: 2026-11-24
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11:

* Tìm hiểu nhóm dịch vụ AI dựng sẵn của AWS, dùng qua API mà không cần tự train model.
* Thực hành phân tích ảnh với Amazon Rekognition và trích xuất văn bản, bảng, form với Amazon Textract.
* Thực hành xử lý ngôn ngữ tự nhiên với Amazon Comprehend và dịch máy với Amazon Translate.
* Thực hành chuyển giọng nói thành văn bản với Amazon Transcribe và văn bản thành giọng nói với Amazon Polly.
* Dựng chatbot hội thoại với Amazon Lex V2 và so sánh với chatbot RAG đã làm ở các tuần trước.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 3 | - Đọc AWS decision guide về các dịch vụ ML để phân biệt AI services dựng sẵn với SageMaker (đã học ở tuần 5) <br> - Làm Exercise 1 của Amazon Rekognition trên console: phát hiện object và scene, xem nhãn kèm confidence score <br> - Gọi `DetectLabels` bằng AWS CLI cho ảnh trên S3 và đọc kết quả JSON (Labels, Instances, Parents) <br> - Tìm hiểu `DetectModerationLabels` để lọc ảnh không phù hợp, và tutorial dùng Lambda gọi Rekognition khi có ảnh mới trên S3 <br> - Clean up: xóa ảnh thử và bucket | 24/11/2026 | 24/11/2026 | <https://docs.aws.amazon.com/rekognition/latest/dg/labels-detect-labels-image.html> |
| 4 | - Đọc tài liệu Amazon Textract: phân biệt `DetectDocumentText` (chỉ lấy chữ) và `AnalyzeDocument` (thêm Forms, Tables, Queries) <br> - Thực hành trích xuất văn bản từ ảnh scan, đọc cấu trúc block PAGE, LINE, WORD và quan hệ giữa các block <br> - Thực hành `AnalyzeDocument` với `FORMS` và `TABLES` để lấy cặp key-value và ô bảng từ hóa đơn mẫu <br> - Tìm hiểu xử lý bất đồng bộ cho PDF nhiều trang (`StartDocumentTextDetection` kết hợp SNS và SQS) <br> - Đọc tutorial đưa văn bản từ Textract sang Comprehend để phân tích tiếp | 25/11/2026 | 25/11/2026 | <https://docs.aws.amazon.com/textract/latest/dg/analyzing-document-text.html> |
| 5 | - Đọc Getting started với Amazon Comprehend: phát hiện ngôn ngữ, entity, key phrase, sentiment, PII, syntax <br> - Thực hành trên console và AWS CLI: `detect-sentiment`, `detect-entities`, `detect-key-phrases` với văn bản mẫu <br> - Đọc tutorial phân tích đánh giá khách hàng: chạy analysis job trên tài liệu ở S3 và xem kết quả <br> - Tìm hiểu Amazon Translate: dịch thời gian thực bằng `translate-text`, tự phát hiện ngôn ngữ nguồn, custom terminology <br> - Thử dịch giữa tiếng Việt và tiếng Anh, rồi đưa bản dịch tiếng Anh vào Comprehend để phân tích sentiment | 26/11/2026 | 26/11/2026 | <https://docs.aws.amazon.com/comprehend/latest/dg/getting-started.html> |
| 6 | - Làm tutorial Amazon Transcribe trên console và AWS CLI: upload file âm thanh lên S3, chạy `start-transcription-job`, đọc file transcript JSON <br> - Tìm hiểu tùy chọn tự nhận diện ngôn ngữ, phân biệt người nói (speaker diarization) và custom vocabulary <br> - Đọc How Amazon Polly works: voice, engine (standard, neural), định dạng output và SSML <br> - Thực hành `synthesize-speech` để tạo file MP3 từ văn bản và dùng SSML chỉnh tốc độ, ngắt câu <br> - Clean up: xóa transcription job, file âm thanh và transcript trên S3 | 27/11/2026 | 27/11/2026 | <https://docs.aws.amazon.com/transcribe/latest/dg/getting-started-cli.html> |
| 7 | - Đọc Amazon Lex V2 core concepts: bot, locale, intent, utterance, slot, slot type, fulfillment <br> - Làm Exercise 1: tạo chatbot từ template, build bot và kiểm thử hội thoại trong cửa sổ Test <br> - Thêm intent và slot mới, cấu hình prompt hỏi lại khi thiếu thông tin <br> - Tìm hiểu tích hợp Lambda cho fulfillment, version và alias của bot <br> - So sánh Lex (luồng hội thoại theo intent) với chatbot RAG trên OpenSearch và Bedrock đã làm ở tuần 3 và tuần 6 <br> - Clean up: xóa bot và IAM role của lab | 28/11/2026 | 28/11/2026 | <https://docs.aws.amazon.com/lexv2/latest/dg/exercise-1.html> |

### Vướng mắc và lưu ý tuần 11:

* Rekognition giới hạn kích thước ảnh (5 MB khi gửi bytes, 15 MB khi đọc từ S3), và bucket S3 phải cùng region với API được gọi.

* API đồng bộ của Textract chỉ xử lý ảnh hoặc PDF một trang. PDF nhiều trang phải dùng API bất đồng bộ (`StartDocumentTextDetection`, `StartDocumentAnalysis`).

* Comprehend không hỗ trợ phân tích sentiment cho tiếng Việt: dịch sang tiếng Anh bằng Translate trước rồi mới phân tích.

* Polly chưa có giọng đọc tiếng Việt, nên phần thử nghiệm dùng văn bản tiếng Anh. Không phải giọng nào cũng hỗ trợ engine neural.

* File âm thanh cho Transcribe phải nằm trên S3 cùng region với job. Với Lex V2, mỗi lần sửa intent hoặc slot phải bấm Build lại thì cửa sổ Test mới nhận thay đổi.

### Kết quả đạt được tuần 11:

* Phân biệt được khi nào dùng AI services dựng sẵn và khi nào cần SageMaker để tự train model.

* Biết dùng Rekognition để gán nhãn và kiểm duyệt ảnh, và Textract để trích xuất chữ, form, bảng từ tài liệu.

* Biết dùng Comprehend để phân tích sentiment, entity, key phrase, và Translate để dịch văn bản giữa nhiều ngôn ngữ.

* Biết dùng Transcribe để chuyển âm thanh thành văn bản và Polly để tạo giọng nói từ văn bản với SSML.

* Dựng được chatbot Lex V2 với intent, slot và hiểu cách tích hợp Lambda cho fulfillment.

* Nắm cách ghép nhiều AI service thành luồng xử lý, như Textract → Comprehend hoặc Translate → Comprehend.
