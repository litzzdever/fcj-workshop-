---
title: "Week 11 Worklog"
date: 2026-11-24
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:

* Learn AWS's ready-made AI services, used through APIs without training your own model.
* Practice image analysis with Amazon Rekognition and text, table, and form extraction with Amazon Textract.
* Practice natural language processing with Amazon Comprehend and machine translation with Amazon Translate.
* Practice speech-to-text with Amazon Transcribe and text-to-speech with Amazon Polly.
* Build a conversational chatbot with Amazon Lex V2 and compare it with the RAG chatbot from earlier weeks.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Tue | - Read the AWS decision guide for ML services to separate ready-made AI services from SageMaker (covered in week 5) <br> - Did Amazon Rekognition Exercise 1 in the console: detected objects and scenes and reviewed labels with confidence scores <br> - Called `DetectLabels` with the AWS CLI on an image in S3 and read the JSON result (Labels, Instances, Parents) <br> - Studied `DetectModerationLabels` for filtering inappropriate images, and the tutorial that uses Lambda to call Rekognition when a new image lands in S3 <br> - Cleaned up: deleted the test images and the bucket | 11/24/2026 | 11/24/2026 | <https://docs.aws.amazon.com/rekognition/latest/dg/labels-detect-labels-image.html> |
| Wed | - Read the Amazon Textract docs: `DetectDocumentText` (text only) versus `AnalyzeDocument` (adds Forms, Tables, Queries) <br> - Extracted text from a scanned image and read the PAGE, LINE, and WORD block structure and the relationships between blocks <br> - Ran `AnalyzeDocument` with `FORMS` and `TABLES` to get key-value pairs and table cells from a sample invoice <br> - Studied asynchronous processing for multi-page PDFs (`StartDocumentTextDetection` with SNS and SQS) <br> - Read the tutorial that sends Textract output to Comprehend for further analysis | 11/25/2026 | 11/25/2026 | <https://docs.aws.amazon.com/textract/latest/dg/analyzing-document-text.html> |
| Thu | - Read Getting started with Amazon Comprehend: language detection, entities, key phrases, sentiment, PII, syntax <br> - Practiced in the console and AWS CLI: `detect-sentiment`, `detect-entities`, `detect-key-phrases` on sample text <br> - Read the customer review analysis tutorial: ran an analysis job on documents in S3 and reviewed the results <br> - Studied Amazon Translate: real-time translation with `translate-text`, automatic source language detection, and custom terminology <br> - Translated between Vietnamese and English, then sent the English text to Comprehend for sentiment analysis | 11/26/2026 | 11/26/2026 | <https://docs.aws.amazon.com/comprehend/latest/dg/getting-started.html> |
| Fri | - Followed the Amazon Transcribe tutorial in the console and AWS CLI: uploaded an audio file to S3, ran `start-transcription-job`, and read the transcript JSON <br> - Studied automatic language identification, speaker diarization, and custom vocabulary <br> - Read How Amazon Polly works: voices, engines (standard, neural), output formats, and SSML <br> - Practiced `synthesize-speech` to create an MP3 from text and used SSML to adjust speed and pauses <br> - Cleaned up: deleted the transcription job, the audio file, and the transcript in S3 | 11/27/2026 | 11/27/2026 | <https://docs.aws.amazon.com/transcribe/latest/dg/getting-started-cli.html> |
| Sat | - Read Amazon Lex V2 core concepts: bot, locale, intent, utterance, slot, slot type, fulfillment <br> - Did Exercise 1: created a chatbot from a template, built it, and tested the conversation in the Test window <br> - Added a new intent and slot, and configured a prompt that asks again when information is missing <br> - Studied Lambda integration for fulfillment, and bot versions and aliases <br> - Compared Lex (intent-based conversation) with the RAG chatbot on OpenSearch and Bedrock from weeks 3 and 6 <br> - Cleaned up: deleted the bot and the lab IAM role | 11/28/2026 | 11/28/2026 | <https://docs.aws.amazon.com/lexv2/latest/dg/exercise-1.html> |

### Week 11 Achievements:

* Learned when to use ready-made AI services and when SageMaker is needed to train a custom model.

* Learned to use Rekognition to label and moderate images, and Textract to extract text, forms, and tables from documents.

* Learned to use Comprehend for sentiment, entity, and key phrase analysis, and Translate to translate text between languages.

* Learned to use Transcribe to turn audio into text and Polly to generate speech from text with SSML.

* Built a Lex V2 chatbot with intents and slots, and understood how Lambda handles fulfillment.

* Learned to chain AI services into a pipeline, such as Textract → Comprehend or Translate → Comprehend.
