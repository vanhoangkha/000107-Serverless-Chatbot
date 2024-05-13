---
title : "AWS Lambda and AWS Amplify"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 1.3</b> "
---

### Amazon Lambda
![Amazon Lambda](/images/1/Img1_Lambda.png?featherlight=false&width=12pc "Amazon Lambda")
**Amazon Lambda** is a serverless compute service that lets you run code without provisioning or managing servers https://aws.amazon.com/serverless/videos/video-lambda-intro/. You simply upload your code, and Lambda takes care of everything else, including running the code in response to events and managing the underlying infrastructure.
Here are some of the key things you can achieve with AWS Lambda:

- **Building Serverless Applications**:
  - Create microservices: Break down complex applications into smaller, independent functions that can be developed, deployed, and scaled individually.
  - Develop backend logic: Implement functionalities like user authentication, data processing, or API integrations without managing servers.

- **Event-Driven Programming**:

  - Respond to changes in other AWS services: Trigger your Lambda function based on events like file uploads in S3, changes in DynamoDB tables, or messages in SNS queues.
  - Build real-time applications: Process data streams and react to events in near real-time, enabling applications like chat functionalities or data pipelines.

- **Cost-Effective Development**:
  - Pay-per-use model: You only pay for the compute time your code utilizes, eliminating costs for idle servers.
  - Automatic scaling: Lambda scales your functions automatically based on traffic, ensuring efficient resource allocation.

- **Integration Capabilities**:

  - Connects with various AWS services: Lambda integrates seamlessly with other AWS services like S3, DynamoDB, SNS, and API Gateway.
  - Integrates with third-party applications: Interact with external APIs and services using libraries or custom code within your Lambda functions.
### Amazon API Gateway
![Amazon Apigw](/images/1/Img1_Apigw.png?featherlight=false&width=12pc "Amazon Apigw")

**Amazon API Gateway** is a fully managed service that acts as a single entry point for applications to access data and functionalities from your backend resources. 

Using API Gateway, you can create RESTful APIs and WebSocket APIs that enable real-time two-way communication applications. API Gateway supports containerized and serverless workloads, as well as web applications.

**Amazon API Gateway and AWS Lambda are designed to work together seamlessly**. In fact, API Gateway is one of the most common ways to trigger Lambda functions. When they work together, API Gateway is a front door which provides a single endpoint URL for Lambda to performs a specific task, like processing data, interacting with databases, or sending notifications.

--- 
### Amazon Amplify
![Amazon Amplify](/images/1/Img1_Amplify.png?featherlight=false&width=12pc "Amazon Amplify")

**Amazon Amplify** is everything you need to build full-stack web and mobile apps on AWS. Build and host your frontend, add features like auth and storage, connect to real-time data sources, deploy, and scale to millions of users.

In this workshop,**Amazon Amplify** will be use for building the chatbot front-end.

---

### Amazon S3
![Amazon S3](/images/1/Img1_S3.png?featherlight=false&width=12pc "Amazon S3")

**Amazon Simple Storage Service** (Amazon S3) is an object storage service offering industry-leading scalability, data availability, security, and performance. Customers of all sizes and industries can store and protect any amount of data for virtually any use case, such as data lakes, cloud-centered applications, and mobile apps. 

In this workshop, you use an **S3 bucket to upload your use case-centric documents**. **Amazon Kendra indexes these documents** to provide Retrieval-Augmented Generation (RAG) answers to questions that you ask.