---
title : "How the architecture work?"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 1.4 </b> "
---

### The Architecture Flow
The architecture that we building provided below: 
![WS2_Architecture_Num](/images/1/WS2_Architecture_Num.svg?featherlight=false&width=70pc "Build AI Serverless Chatbot")

1. A **client/user** asks questions to the chatbot through an application that is hosted through **AWS Amplify** and **Amazon CloudFront**.
2. The question is sent to the **AWS Lambda RAG function** through **Amazon API Gateway**.
3. To retrieve the relevant context from the document source, the RAG function calls the **Amazon Kendra** API.
4. The RAG function then sends the API returned content, along with a prebuilt prompt, to the foundation model (FM) in **Amazon Bedrock**.
5. The FM returned response is then sent back to the **client/user**.