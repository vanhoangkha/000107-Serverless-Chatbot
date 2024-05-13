---
title : "Trải nghiệm Chatbot với Amazon Bedrock"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
#### Trải nghiệm Chatbot với Amazon Bedrock

Bạn đã triển khai thành công ứng dụng serverless **Amazon Bedrock**, sử dụng **AWS SAM** cho back-end và **AWS Amplify** cho front-end. Giao diện người dùng phục vụ như một giao diện người dùng cơ bản để kiểm tra giải pháp với các câu hỏi và tham số prompt khác nhau. Trong phần này, bạn sẽ trải nghiệm một chuyến tham quan ngắn gọn về bảng điều khiển **Amazon Bedrock** và các tính năng của nó, bao gồm phiên hỏi đáp bằng ứng dụng UI. Sau đó, trọng tâm sẽ chuyển sang kỹ thuật prompt.
Giải pháp trong workshop này được xây dựng dựa trên phương pháp Retrieval-Augmented Generation (**RAG**). RAG là một kiến trúc mô hình tích hợp các khía cạnh của cả kỹ thuật truy hồi và tạo để nâng cao chất lượng và tính liên quan của văn bản được tạo. Khi bạn nhập câu hỏi vào hộp văn bản câu hỏi, các bước back-end sau đây sẽ được chạy để cung cấp cho bạn câu trả lời lấy từ nguồn tài liệu:

**Truy hồi** (Retrieval): Quá trình này tìm kiếm thông qua một kho dữ liệu văn bản lớn để tìm thông tin hoặc ngữ cảnh liên quan. Trong giai đoạn này, Amazon Kendra lấy câu hỏi từ yêu cầu và tìm kiếm các câu trả lời và tài liệu tham khảo có liên quan.

**Tăng cường** (Augmentation): Sau khi truy xuất thông tin có liên quan, mô hình sử dụng ngữ cảnh đã truy xuất được để tăng cường việc tạo văn bản. Điều này có nghĩa là văn bản được tạo ra bị ảnh hưởng bởi thông tin được truy xuất, đảm bảo rằng nội dung được tạo ra phù hợp với ngữ cảnh và cung cấp thông tin.

**Tạo** (Generation): Tạo trong RAG đề cập đến khía cạnh tạo truyền thống của mô hình, nơi nó tạo ra văn bản mới dựa trên ngữ cảnh đã truy xuất và tăng cường. Văn bản được tạo này có thể dưới dạng câu trả lời, phản hồi hoặc giải thích.

**LangChain**: Để dàn xếp luồng này, chúng tôi sử dụng tác nhân LangChain trong workshop này. Các phép trừu tượng linh hoạt và bộ công cụ toàn diện của LangChain cho phép các nhà phát triển tận dụng các khả năng của các mô hình nền tảng (FM).

#### Nội dung

- [Bật các Mô hình Nền tảng trong Amazon Bedrock](3-1SetUpBedRock)
- [Trò chuyện đơn giản với Giao diện người dùng Chatbot](3-2SimpleConversation)
- [Kỹ thuật Prompt Engineering trong Chatbots](3-3PromptEngineering)
