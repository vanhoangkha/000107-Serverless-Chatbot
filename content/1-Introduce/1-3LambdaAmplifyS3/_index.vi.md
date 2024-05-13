---
title : "AWS Lambda và AWS Amplify"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 1.3</b> "
---

### Amazon Lambda
![Amazon Lambda](/images/1/Img1_Lambda.png?featherlight=false&width=12pc "Amazon Lambda")
**Amazon Lambda** là dịch vụ điện toán không server cho phép bạn chạy code mà không cần cung cấp hoặc quản lý server (https://aws.amazon.com/serverless/videos/video-lambda-intro/). Bạn chỉ cần upload code của mình và Lambda sẽ xử lý mọi thứ khác, bao gồm chạy code để phản hồi các sự kiện và quản lý cơ sở hạ tầng bên dưới.
Dưới đây là một số tính năng chính bạn có thể đạt được với AWS Lambda:
- **Xây dựng các ứng dụng không server(serverless):**:
  - Tạo các microservice: Chia nhỏ các ứng dụng phức tạp thành các hàm độc lập, nhỏ hơn có thể được phát triển, triển khai và mở rộng riêng lẻ.
  - Phát triển logic backend: Thực hiện các chức năng như xác thực người dùng, xử lý dữ liệu hoặc tích hợp API mà không cần quản lý server.

- **Lập trình theo sự kiện**:

  - Phản hồi với các thay đổi trong các dịch vụ AWS khác: Kích hoạt hàm Lambda của bạn dựa trên các sự kiện như upload file trong S3, thay đổi trong các bảng DynamoDB hoặc message trong các hàng đợi SNS.
  - Xây dựng các ứng dụng thời gian thực: Xử lý các luồng dữ liệu và phản ứng với các sự kiện gần như theo thời gian thực, cho phép các ứng dụng như chức năng chat hoặc pipeline dữ liệu.

- **Phát triển tiết kiệm chi phí:**:
  - Mô hình theo kiểu sử dụng (pay-per-use): Bạn chỉ trả cho thời gian tính toán mà code của bạn sử dụng, loại bỏ chi phí cho các server nhàn rỗi.
  - Tự động mở rộng: Lambda tự động mở rộng các hàm của bạn dựa trên lưu lượng truy cập, đảm bảo phân bổ tài nguyên hiệu quả.

- **Khả năng tích hợp:**:

  - Kết nối với nhiều dịch vụ AWS khác nhau: Lambda tích hợp liền mạch với các dịch vụ AWS khác như S3, DynamoDB, SNS và API Gateway.
  - Tích hợp với các ứng dụng của bên thứ ba: Tương tác với các API và dịch vụ bên ngoài bằng cách sử dụng các thư viện hoặc code tùy chỉnh trong các hàm Lambda của bạn.
### Amazon API Gateway
![Amazon Apigw](/images/1/Img1_Apigw.png?featherlight=false&width=12pc "Amazon Apigw")

**Amazon API Gateway** là một dịch vụ được quản lý hoàn toàn, hoạt động như một điểm vào duy nhất cho các ứng dụng để truy cập dữ liệu và chức năng từ các tài nguyên backend của bạn.

Sử dụng API Gateway, bạn có thể tạo các API RESTful và WebSocket API cho phép các ứng dụng giao tiếp hai chiều theo thời gian thực. API Gateway hỗ trợ khối lượng công việc được container hóa và không server, cũng như các ứng dụng web.

**Amazon API Gateway và AWS Lambda được thiết kế để hoạt động cùng nhau một cách liền mạch**. Thực tế, API Gateway là một trong những cách phổ biến nhất để kích hoạt các hàm Lambda. Khi chúng hoạt động cùng nhau, API Gateway là một cửa trước cung cấp URL endpoint duy nhất cho Lambda để thực hiện một tác vụ cụ thể, chẳng hạn như xử lý dữ liệu, tương tác với cơ sở dữ liệu hoặc gửi thông báo.

--- 
### Amazon Amplify
![Amazon Amplify](/images/1/Img1_Amplify.png?featherlight=false&width=12pc "Amazon Amplify")

**Amazon Amplify** là tất cả những gì bạn cần để xây dựng các ứng dụng web và di động đầy đủ trên AWS. Xây dựng và lưu trữ frontend của bạn, thêm các tính năng như xác thực và lưu trữ, kết nối với các nguồn dữ liệu thời gian thực, triển khai và mở rộng cho hàng triệu người dùng.

Trong workshop này, **Amazon Amplify** sẽ được sử dụng để xây dựng giao diện người dùng chatbot.

---

### Amazon S3
![Amazon S3](/images/1/Img1_S3.png?featherlight=false&width=12pc "Amazon S3")

**Amazon Simple Storage Service** (Amazon S3) là một dịch vụ lưu trữ các object cung cấp khả năng mở rộng cấp doanh nghiệp, khả năng availabilty của dữ liệu, bảo mật, và hiệu quả. Khách hàng từ khắp các quy mô có thể lưu trữ và bảo mật dữ liệu theo tất cả các cách trường hợp sử dụng khác nhau, như là data lakes, ứng dụng tập trung đám mây, và ứng dụng di động. 

Trong workshop này, bạn sẽ sử dụng  **S3 bucket** để lưu trữ các tài liệu liên quan đến trường hợp sử dụng của mình. **Amazon Kendra Amazon Kendra sẽ lập chỉ mục các tài liệu này để cung cấp câu trả lời được** để cung cấp câu trả lời Retrieval-Augmented Generation (RAG) cho các câu hỏi bạn đặt ra.