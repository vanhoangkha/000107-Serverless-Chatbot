---
title : "Index the Sample Data by Using Amazon Kendra"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---

#### Index the Sample Data by Using Amazon Kendra

Download sample documents
Download a few sample documents to test this solution. The first document pertains to the September 2023 meeting minutes of the **Federal Open Market Committee (FOMC)**. The second document is the 2022 Amazon Sustainability Report. The third document is the 10K report for Henry Schein, a provider of dental services. You can download or use any document to test this solution, or you can bring your own data to conduct tests.

To copy the prompt templates and sample documents that you downloaded to the S3 bucket, run the following command.

```bash
cd ~/environment/bedrock-serverless-workshop
mkdir sample-documents
curl https://www.federalreserve.gov/monetarypolicy/files/fomcminutes20230920.pdf --output sample-documents/fomcminutes20230920.pdf
curl https://sustainability.aboutamazon.com/2022-sustainability-report.pdf --output sample-documents/2022-sustainability-report-amazon.pdf
curl https://investor.henryschein.com/static-files/fcf569ec-fdbb-4591-b73d-0d1d849efd78 --output sample-documents/2022-hs1-10k.pdf
```
Those command will create sample-documents directory in our **Cloud9** enviroment and fetch some of sample document to it using `curl` command.
   ![2_19](/images/2/2_19.png "Curl command image")

#### Upload sample documents and prompt templates

To copy prompt templates and sample documents you downloaded to the **S3 bucket**, run the following command.

```bash
cd ~/environment/bedrock-serverless-workshop
aws s3 cp prompt-engineering s3://$S3BucketName/prompt-engineering/ --recursive
aws s3 cp sample-documents s3://$S3BucketName/sample-documents/ --recursive

```
   ![2_20](/images/2/2_20.png "Upload to S3")

After a successful upload, review the **Amazon S3** console and open the bucket. You should see something like this:
   ![2_21](/images/2/2_21.png "S3 Bucket")
   ![2_22](/images/2/2_22.png "S3 Bucket storage")


#### Index the sample documents by using Amazon Kendra

**Amazon Kendra** is an intelligent search service powered by machine learning (ML). Amazon Kendra reimagines enterprise searches for your websites and applications, so your employees and customers can find the content they’re looking for, even when it’s scattered across multiple locations and content repositories within your organization.

The Amazon Kendra index and Amazon S3 data source were created during the initial provisioning for this workshop. In this task, you index all the documents in the **S3 data source**.

1. Sign in to the AWS Management Console, and then type Kendra in the console search box. Review to ensure that your AWS account has access to Amazon Kendra, and then select the AWS Region where this workshop is performing.
   ![2_23](/images/2/2_23.png "Kendra")

   ![2_24](/images/2/2_24.png "Kendra enviroment")
2. At the upper-left of the Amazon Kendra console, choose the menu (hamburger) icon, and then, in the navigation pane, choose Indexes.
   ![2_25](/images/2/2_25.png "Kendra enviroment")

3. Choose the enviroment. Click on the index name to see the left data source pane.
   ![2_26](/images/2/2_26.png "Kendra enviroment")

4. To start indexing all the documents from the sample-documents folder, select the `S3DocsDataSource`, and then choose **Sync now**. The indexing might take a couple minutes. Wait for it to be completed.
   ![2_27](/images/2/2_27.png "Kendra enviroment")

5. To query the **Amazon Kendra** index with a few sample questions, in the left navigation pane, choose Search indexed content, and then ask a question.
   ![2_28](/images/2/2_28.png "Sucessful annoucement")
**Sample question**:

```text
What is the federal funds rate as of September 2023?

```
   ![2_29](/images/2/2_29.png "Kendra query")
