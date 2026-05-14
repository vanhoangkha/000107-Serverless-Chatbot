---
title : "Tạo ứng dụng Chatbot Serverless Chatbot với Amazon Bedrock, Amazon Kendra và Dữ liệu của chính bạn"
date: 2024-01-01
weight : 1
chapter : false
---

# Tạo ứng dụng Chatbot Serverless Chatbot với Amazon Bedrock, Amazon Kendra và Dữ liệu của chính bạn.

#### Giới thiệu

**Trí tuệ nhân tạo tạo sinh** (generative AI) là một phát hiện đột phá của nhân loại có tiềm tăng ứng dụng trong mọi ngành xã hội. Không giống như các mô hình máy học truyền thống được huấn luyện để nhận ra các quy luật trong dữ liệu, trí tuệ nhân tạo tạo sinh có thể tạo ra dữ liệu hoàn toàn mới mà khó có thể phân biệt được đối với dữ liệu được tạo ra bởi con người.

**Một số ví dụ của AI tạo sinh**: sinh ra các đoạn văn bản (như là GPT-3, Anthropic Claude, và Amazon Titan), tạo ảnh (như là DALL-E 2 và Stable Diffusion), và sáng tác nhạc. Những mô hình trên chỉ cần một lượng mẫu các đoạn văn, hình ảnh hoặc nhạc để có thể tạo ra những kết quả dựa hoàn toàn mới và sát với thực tế dựa vào các quy luật mà chúng được học. Điều này khiến AI từ chỉ có thể phân tích thế giới của chúng ta đến sáng tạo bên trong thế giới ấy. Sau đó, AI có thể ứng dụng kiến thức đó để giải quyết vấn đề mới theo cách gần giống con người nhất. Ví dụ, công nghệ AI có thể trả lợi một cách có ý nghĩa cuộc giao tiếp của con người, tạo ra các bức ảnh và văn bản gốc, và đưa ra các quyết định dựa trên dữ liệu nhập vào thời gian thực. **Tổ chức của các bạn có thể tích hợp khả năng AI** trong các ứng dụng để tối ưu tiến trình kinh doanh, tăng cường trải nghiệm người dùng, và khuyến khích đổi mới.

**Dịch vụ web của Amazon** (AWS) cung cấp các dịch vụ, công cụ và tài nguyên toàn diện nhất để đáp ứng các yêu cầu về công nghệ AI của bạn. AWS giúp AI dễ dàng tiếp cận với các tổ chức thuộc mọi quy mô để bất kỳ ai cũng có thể xây dựng công nghệ mới, sáng tạo mà không cần lo lắng về tài nguyên cơ sở hạ tầng. [Trí tuệ nhân tạo và máy học AWS](https://aws.amazon.com/machine-learning/) cung cấp hàng trăm phương thức để xây dựng và mở rộng ứng dụng AI cho từng trường hợp sử dụng các dịch vụ serverlessLambda

Trong **workshop này**, bạn sẽ được học cách để xây dụng chatbot AI tạo sinh từ **Amazon Bedrock** và **Amazon Kendra**. Bạn cũng có thể học được cách xây dựng chatbot này bằng cách sử dụng Bedrock

- **Câp độ**: Trung cấp (200)
- **Thời gian thực hiện**: 90 minutes
- **Chi phí**: Hãy chắc chắn rằng bạn tạo workshop này trong tài khoản AWS của bạn, phần CleanUp trong thanh điều hướng bên trái có thể giúp bạn xóa các tài nguyên từ môi trường của bạn khi thao tác xong.
- **Các region hỗ trợ** : Tôi đề xuất cacs bạn nên chạp workshop này này trong  us-* AWS Region.
- **Target Audience**: Ưu triên us-east- *
- **Supported Region**: Solutions Architects, Software Engineers, ML Engineers





#### Content

1. [Giới thiệu](1-introduce/)
2. [Cài đặt môi trường](2-enviromentsetup/)
3. [Trải nghiệm giao tiếp với Bedrock](3-chatexperiencewbedrock/)
4. [Tổng kết](4-Summary/)


