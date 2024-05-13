---
title : "Kiến trúc này hoạt động như thế nào?"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 1.4 </b> "
---

### Luồng đi kiến trúc
Kiến trúc chúng ta xây dựng được minh họa bên dưới:
![WS2_Architecture_Num](/images/1/WS2_Architecture_Num.svg?featherlight=false&width=70pc "Build AI Serverless Chatbot")

1. **Người dùng/khách hàng** đặt câu hỏi cho chatbot thông qua một ứng dụng được lưu trữ trên **AWS Amplify** và **Amazon CloudFront**.
2. Câu hỏi được gửi đến **Hàm RAG AWS Lambda** thông qua **Amazon API Gateway**.
3. Để truy xuất ngữ cảnh liên quan từ nguồn tài liệu, hàm RAG gọi API **Amazon Kendra**.
4. Sau đó, hàm RAG gửi nội dung trả về từ API cùng với một prompt được xây dựng trước đó tới model nền tảng (FM) trong **Amazon Bedrock**.
5. Phản hồi trả về từ FM sau đó được gửi lại cho **người dùng/khách hàng**.