---
title : "Chatbot Experience with Amazon Bedrock"
date: 2024-01-01
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
#### Chatbot Experience with Amazon Bedrock

You have successfully deployed the **Amazon Bedrock** serverless application, using **AWS SAM** for the backend and **AWS Amplify** for the frontend. The frontend serves as a basic user interface for testing the solution with various questions and prompt parameters. In this section, you experience a concise tour of the **Amazon Bedrock** console and its features, including a Q&A session using the UI application. Following that, the focus shifts to prompt engineering.

The solution in this workshop is constructed using the **Retrieval-Augmented Generation** (RAG) approach. **RAG** is a model architecture that integrates aspects of both retrieval and generation techniques to enhance the quality and relevance of generated text. When you input a question into the question text box, the following backend steps are run to provide you with an answer derived from the document source:

**Retrieval**: This process searches through a large corpus of text data to find relevant information or context. During this stage, Amazon Kendra takes the question from the request and searches for the relevant answers and references.

**Augmentation**: After retrieving relevant information, the model uses the retrieved context to augment the generation of text. This means that the generated text is influenced by the retrieved information, ensuring that the generated content is contextually appropriate and informative.

**Generation**: Generation in RAG refers to the traditional generative aspect of the model, where it creates new text based on the retrieved and augmented context. This generated text can be in the form of answers, responses, or explanations.

**LangChain**: To orchestrate this flow, we employ the LangChain agent in this workshop. LangChain's flexible abstractions and comprehensive toolkit empower developers to harness the capabilities of foundation models (FMs).

#### Contents

- [Set up CloudFormation template](3-1SetUpBedRock)
- [Access Cloud9 and set up enviroment](3-2SimpleConversation)
- [Deploy Amazon Bedrock Serverless Application](3-3PromptEngineering)
