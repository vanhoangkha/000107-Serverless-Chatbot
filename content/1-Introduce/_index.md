---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

#### Introduction

For this workshop, you will use the **Retrieval-Augmented Generation (RAG)** technique to build a generative AI-powered chatbot. A foundation model (FM) in Amazon Bedrock is used to answer your chatbot questions through pre-indexed content. **Amazon Bedrock** is a fully managed service that makes FMs (from Amazon and leading AI startups) available through an API, so you can choose from various FMs to find the model that's best suited to your use case. For storing (indexing) and retrieving relevant content, you use **Amazon Kendra**, a fully managed service that provides intelligent enterprise searches powered by machine learning. You use AWS Lambda as the serverless compute for running the application code in an event-driven manner.

To begin with, in this introduction chapter we will walk through the core concepts for the workshop, Amazon services that support them and the architecture that ties everything together.

![WS2_Architecture](/images/1/WS2_Architecture.svg?featherlight=false&width=70pc "Build AI Serverless Chatbot")

---

{{%expand "Static version (Click)" %}}
![WS2_Architecture](/images/1/WS2_Architecture.png?featherlight=false "Build AI Serverless Chatbot")
{{% /expand%}}

---
#### Contents

- [The core concept](1-1CoreConcept)
- [Amazon Bedrock, Amazon Kendra and Amazon Cloud9](1-2AWSBedrockkendra9)
- [AWS Lambda and AWS Amplify](1-3LambdaAmplifyS3)
- [How the architecture work?](1-4ArchitectureFlow)
