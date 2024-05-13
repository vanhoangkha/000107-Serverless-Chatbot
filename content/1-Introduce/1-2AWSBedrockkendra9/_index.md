---
title : "Amazon Bedrock, Amazon Kendra and Amazon Cloud9"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 1.2 </b> "
---

### Amazon Bedrock

![Amazon Bedrock](/images/1/Img1_Bedrock.png?featherlight=false&width=12pc "Amazon Bedrock")

 **Amazon Bedrock** is a fully managed service that offers a choice of high-performing foundation models (FMs) from leading AI companies like AI21 Labs, Anthropic, Cohere, Meta Mistral AI, Stability AI, and Amazon **via a single API**, along with a broad set of capabilities you need to build generative AI applications with

Amazon Bedrock specializes in building generative AI applications. Here's a breakdown of it workflow:

1. **Foundational Model Selection**: You start by choosing a pre-trained foundation model (FM) from Amazon or a third-party provider like Anthropic or Cohere. These FMs act as the building blocks for your application.

2. **Customization**: Bedrock allows you to customize the chosen FM using your organization's data. This can involve fine-tuning the model on your specific task or incorporating your company's knowledge base.

3. **Building Blocks**: Bedrock offers two main building blocks to craft your application:

     - **Knowledge Bases**: This lets you integrate your company's data with the FM. Bedrock automates the process, including data ingestion and retrieval, so you don't need to write complex code.

     - **Agents**:  These are programs that can interact with various systems and data sources. You can design agents to answer customer questions, process orders, or complete other tasks using a combination of the FM, your knowledge base, and other AWS services.

4. **Deployment**:  Bedrock is a fully managed service, so you don't need to worry about managing infrastructure. Once you've built your application, Bedrock handles deployment and scaling. The service takes care of the underlying infrastructure, allowing you to focus on building your application.

---

### Amazon Kendra

![Amazon Kendra](/images/1/Img1_Kendra.png?featherlight=false&width=12pc "Amazon Kendra")

**Amazon Kendra** is an intelligent search service that uses natural language processing and advanced machine learning algorithms to return specific answers to search questions from your data.

**Unlike traditional keyword-based search**, Amazon Kendra uses its semantic and contextual understanding capabilities to decide whether a document is relevant to a search query. It returns specific answers to questions, giving users an experience that's close to interacting with a human expert.

With Amazon Kendra, you can **create a unified search experience by connecting multiple data repositories to an index** and ingesting and crawling documents. You can use your document metadata to create a feature-rich and customized search experience for your users, helping them efficiently find the right answers to their queries.

Moreover, with the **Amazon Kendra Intelligent Ranking plugin for OpenSearch**, developers can use the Amazon Kendra ML-powered semantic ranking technology to quickly improve the quality of their OpenSearch search results, with no ML expertise necessary. The plugin is designed to work best on document search applications, such as help pages, documentation, informational websites, and internal wikis. Learn more about how to get started with Intelligent Ranking in the [documentation](https://docs.aws.amazon.com/kendra/latest/dg/opensearch-rerank.html).

![Amazon Kendra Flow](/images/1/Img1_KendraFlow.png?featherlight=false "Amazon Kendra Flow")

---
### Amazon Cloud9
![Amazon Cloud9](/images/1/Img1_C9.png?featherlight=false&width=12pc "Amazon Cloud9")

 **AWS Cloud9** is a cloud-based integrated development environment (IDE) that you can use to write, run, and debug your code with just a browser. It includes a code editor, debugger, and terminal. In this workshop, you use AWS Cloud9 to deploy a backend application, which is built by using AWS Serverless Application Model (AWS SAM). You use AWS Amplify to deploy the frontend.
