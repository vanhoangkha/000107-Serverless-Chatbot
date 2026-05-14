---
title : "Giới thiệu"
date: 2024-01-01
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

#### Giới thiệu

Trong workshop này, bạn sẽ sử dụng kĩ thuật **Retrieval-Augmented Generation (RAG)** để xây dựng chatbox từ generative AI. Một mô hình nền tảng (Foundation Model - FM) trong Amazon Bedrock sẽ được sử dụng để trả lời các câu hỏi đã được đánh chỉ mục sẵn. 

**Amazon Bedrock** là một dịch vụ quản lý đầy đủ các FM (từ Amazon và các start up hàng đầu trong lĩnh vực trí tuyệ nhân tạo) thông qua API, từ dịch vụ trên bạn có thể chọn các FM để tìm ra mô hình phù hợp với mục đích sử dụng cá nhân.

 Để lưu trữ ,đánh chỉ mục (indexing) và lấy kết quả về các nội dung liên quan, bạn sẽ sử dụng **Amazon Kendra**, một dịch vụ được quản lý đầy đủ mà cung cấp sự tìm kiếm mang quy mô doanh nghiệp dựa trên máy học.
 
  Bạn sử dụng **AWS Lambda** như là một dịch vụ điện toán phi máy chủ (serverless) thể thực thi chương trình theo khuynh hướng dựa trên các sự kiện.

Tổng quát, trong chương giới thiệu này ta sẽ bắt đầu với các khái niệm nền tảng được sử dụng, những dịch vụ của Amazon hỗ trợ chúng và kiến trúc để gắn kết chúng lại với nhau.

![WS2_Architecture](/images/1/WS2_Architecture.svg?featherlight=false&width=70pc "The architecture that we will build")

{{%expand "Phiên bản tĩnh (Click)" %}}
![WS2_Architecture](/images/1/WS2_Architecture.png?featherlight=false "Build AI Serverless Chatbot")
{{% /expand%}}

---
#### Nội dung

- [Một số khái niệm quan trọng](1-1CoreConcept)
- [Amazon Bedrock, Amazon Kendra và Amazon Cloud9](1-2AWSBedrockkendra9)
- [AWS Lambda và AWS Amplify](1-3LambdaAmplifyS3)
- [Kiến trúc này hoạt động như thế nào?](1-4ArchitectureFlow)
