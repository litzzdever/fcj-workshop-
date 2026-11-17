---
title: "Blog 3"
date: 2026-11-14
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Amazon Rekognition with Amazon Athena – Analyzing images with AI and querying the results with SQL

*Posted on 14/11/2026 in the AWS Study Group – [View the original post](https://www.facebook.com/photo/?fbid=4678263929124258&set=gm.2337636550334637&idorvanity=660548818043427)*

A photo library with hundreds or thousands of files is hard to search without information describing what each image contains. The manual approach is to tag images yourself, but that takes time and is hard to keep up as the data grows.

While exploring AWS's AI services, I wanted to use a ready-made service to recognize image content automatically, then turn the results into data I could analyze. The two services I looked at were Amazon Rekognition and Amazon Athena. Rekognition analyzes images with deep learning and returns labels describing their content, and Athena queries and aggregates those labels with SQL.

![Images in S3 are labeled by Amazon Rekognition through the AWS CLI in CloudShell; the JSON Lines results are stored back in S3 and queried by Amazon Athena with SQL, using metadata in the AWS Glue Data Catalog](/images/3-BlogsPosted/3.3-Blog3/rekognition-athena-architecture.png)

### Amazon Rekognition – Image analysis with deep learning

According to the AWS documentation, Amazon Rekognition is a deep learning–based image and video analysis service that requires no machine learning expertise to use. It offers a simple API for analyzing images or videos stored in Amazon S3, with capabilities such as detecting objects, text, unsafe content, and faces.

In this post I used `DetectLabels`, the API that detects objects, events, and concepts in JPEG or PNG images. Each returned label includes its name (`Name`), a confidence score from 0 to 100 (`Confidence`), its parent labels in the taxonomy (`Parents`), and bounding boxes for the objects (`Instances`). I used two parameters: `MaxLabels` to cap the number of labels and `MinConfidence` to drop low-confidence labels (the default is 55).

On cost, according to the Rekognition pricing page, `DetectLabels` costs 0.001 USD per image in the first tier (1.00 USD per 1,000 images). The free tier covers 1,000 images per month for the first 12 months, and running several APIs on the same image counts as several images.

### Amazon Athena – Querying and aggregating the results

Rekognition returns JSON for each image. To analyze across many images, I combined the results into JSON lines in S3 and let Athena query them directly with SQL, the same way I did in my post on [Athena and the Glue Data Catalog](../3.2-blog2/). According to the Athena pricing page, queries cost 5 USD per TB of data scanned.

### How does Rekognition work together with Athena?

I tried a scenario with 30 images from the Imagenette dataset (160 px version, Apache-2.0 license): 3 images for each of 10 classes such as church, garbage truck, gas pump, parachute, and golf ball. I chose this dataset because the image content is known in advance, which makes it easy to check whether labels are right.

- I uploaded the 30 images to S3, then used the AWS CLI to call `DetectLabels` on each image with `MaxLabels` set to 10 and `MinConfidence` set to 80.
- The results were turned into JSON lines (image, label, confidence, parent labels), combined into one file, and uploaded to S3.
- I created an external table in Athena pointing to the folder with that file, then ran aggregate queries in SQL.

What I recorded:

- **Label count:** 166 image–label pairs from 30 images at threshold 80.
- **Most common labels:** Animal (7 images), Plant (5 images), then Gas Pump, Grass, Machine, and Pump (4 images each), with average confidence around 95 to 97.
- **Search by parent label:** filtering for images with the parent label Vehicle returned 3 images.
- **Athena cost:** each query scanned 18.87 KB and ran in about 300 ms.
- **Rekognition cost:** 60 calls (30 images at two thresholds), within the free tier; about 0.06 USD at list price.

### Confidence and MinConfidence

I ran one more pass with `MinConfidence` set to 55 on the same images: threshold 80 gave 166 labels (84 distinct), and threshold 55 gave 243 labels (124 distinct). A lower threshold gives more labels but may include less accurate ones; a higher threshold gives fewer but more reliable labels. This is the familiar trade-off between recall and precision.

I also did a quick check: each class has exactly 3 images, but the Gas Pump label appeared on 4 images, so at least one image from another class received it, while Brass Section appeared on 3 images, matching the 3 French horn images. So labels should be treated as model predictions, and samples should be checked in real use.

### Notes

- **Cost:** each API call on an image counts as one image, so rerunning with a different threshold is charged again.
- **Privacy:** use public datasets or your own images, and do not upload other people's photos without their consent.
- **IAM:** grant only what is needed, such as `rekognition:DetectLabels` and read access to the specific S3 folder.
- **Clean up:** after testing, delete the images and result files in S3 and the table in Athena.

### Conclusion

Rekognition and Athena complement each other in turning image data into information you can analyze: Rekognition analyzes images and returns labels with confidence scores without requiring machine learning expertise, and Athena queries the results with SQL directly on S3 without servers. With S3 holding both the images and the results, you can build a simple pipeline from raw images to summary tables, then extend it into automation when new images arrive.

### References

- What is Amazon Rekognition?: <https://docs.aws.amazon.com/rekognition/latest/dg/what-is.html>
- DetectLabels (Amazon Rekognition API Reference): <https://docs.aws.amazon.com/rekognition/latest/APIReference/API_DetectLabels.html>
- aws rekognition detect-labels (AWS CLI Command Reference): <https://docs.aws.amazon.com/cli/latest/reference/rekognition/detect-labels.html>
- Amazon Rekognition Pricing: <https://aws.amazon.com/rekognition/pricing/>
- Imagenette (Hugging Face dataset): <https://huggingface.co/datasets/LibreYOLO/imagenette160>
- Amazon Athena Pricing: <https://aws.amazon.com/athena/pricing/>

`#AWS` `#AmazonRekognition` `#AmazonAthena` `#ComputerVision` `#DeepLearning` `#ArtificialIntelligence` `#CloudComputing` `#AWSStudyGroup`
