---
title : "Amazon Bedrock, Amazon Kendra và Amazon Cloud9"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 1.2 </b> "
---

### Amazon Bedrock

![Amazon Bedrock](/images/1/Img1_Bedrock.png?featherlight=false&width=12pc "Amazon Bedrock")

**Amazon Bedrock** là một dịch vụ được quản lý hoàn toàn cung cấp nhiều lựa chọn mô hình nền tảng (FM) hiệu suất cao từ các công ty AI hàng đầu như AI21 Labs, Anthropic, Cohere, Meta Mistral AI, Stability AI và Amazon thông qua một API duy nhất, cùng với một bộ rộng rãi các khả năng cần thiết để xây dựng các ứng dụng AI tạo sinh.

Amazon Bedrock chuyên xây dựng các ứng dụng AI sinh thành. Dưới đây là phân tích quy trình làm việc của nó:

1. **Lựa chọn mô hình nền tảng**: Bạn bắt đầu bằng cách chọn một mô hình nền tảng (FM) được đào tạo sẵn từ Amazon hoặc nhà cung cấp bên thứ ba như Anthropic hoặc Cohere. Các FM này hoạt động như khối xây dựng cho ứng dụng của bạn..

2. **Tùy chỉnh**:Bedrock cho phép bạn tùy chỉnh FM đã chọn bằng dữ liệu của tổ chức bạn. Điều này có thể liên quan đến việc tinh chỉnh mô hình trên tác vụ cụ thể của bạn hoặc kết hợp cơ sở kiến thức của công ty bạn.

3. **Ghép khối xây dựng**: Bedrock cung cấp hai khối xây dựng chính để tạo ứng dụng của bạn:
   
     - **Cơ sở dữ liệu kiến thức**:  Cho phép bạn tích hợp dữ liệu của công ty với FM. Bedrock tự động hóa quy trình, bao gồm thu thập và truy xuất dữ liệu, vì vậy bạn không cần viết mã phức tạp.

     - **Agents**: Đây là các chương trình có thể tương tác với nhiều hệ thống và nguồn dữ liệu khác nhau. Bạn có thể thiết kế các tác nhân để trả lời câu hỏi của khách hàng, xử lý đơn đặt hàng hoặc hoàn thành các tác vụ khác bằng cách kết hợp FM, cơ sở kiến thức của bạn và các dịch vụ AWS khác.

4. **Triển khai**:  Bedrock là một dịch vụ được quản lý hoàn toàn, vì vậy bạn không cần lo lắng về việc quản lý cơ sở hạ tầng. Sau khi xây dựng ứng dụng của bạn, Bedrock sẽ xử lý việc triển khai và mở rộng quy mô. Dịch vụ này chăm sóc cơ sở hạ tầng nền tảng, cho phép bạn tập trung vào việc xây dựng ứng dụng của mình.

---

### Amazon Kendra

![Amazon Kendra](/images/1/Img1_Kendra.png?featherlight=false&width=12pc "Amazon Kendra")

**Amazon Kendra** là một dịch vụ tìm kiếm thông minh sử dụng xử lý ngôn ngữ tự nhiên và thuật toán học máy tiên tiến để trả về các câu trả lời cụ thể cho các câu hỏi tìm kiếm từ dữ liệu của bạn..

**Không giống như tìm kiếm dựa trên từ khóa truyền thống**, Amazon Kendra sử dụng khả năng hiểu ngữ nghĩa và theo ngữ cảnh để quyết định xem một tài liệu có liên quan đến truy vấn tìm kiếm hay không. Nó trả về các câu trả lời cụ thể cho các câu hỏi, mang đến cho người dùng trải nghiệm gần giống như tương tác với một chuyên gia..

Với Amazon Kendra, bạn có thể **ctạo trải nghiệm tìm kiếm thống nhất bằng cách kết nối nhiều kho lưu trữ dữ liệu với một chỉ mục** và thu thập dữ liệu. Bạn có thể sử dụng siêu dữ liệu tài liệu của mình để tạo ra trải nghiệm tìm kiếm phong phú và được tùy chỉnh cho người dùng, giúp họ tìm thấy câu trả lời phù hợp cho các truy vấn của mình một cách hiệu quả.

Hơn nữa, với  **plugin Xếp hạng thông minh Amazon Kendra cho OpenSearch**, dcác nhà phát triển có thể sử dụng công nghệ xếp hạng ngữ nghĩa được hỗ trợ bởi ML của Amazon Kendra để nhanh chóng cải thiện chất lượng kết quả tìm kiếm OpenSearch của họ, mà không cần chuyên môn về ML. Plugin được thiết kế để hoạt động tốt nhất trên các ứng dụng tìm kiếm tài liệu, chẳng hạn như trang trợ giúp, tài liệu, trang web thông tin và wiki nội bộ. Tìm hiểu thêm về cách bắt đầu sử dụng Xếp hạng thông minh trong [tài liệu này](https://docs.aws.amazon.com/kendra/latest/dg/opensearch-rerank.html).

![Amazon Kendra Flow](/images/1/Img1_KendraFlow.png?featherlight=false "Amazon Kendra Flow")

---
### Amazon Cloud9
![Amazon Cloud9](/images/1/Img1_C9.png?featherlight=false&width=12pc "Amazon Cloud9")

 **AWS Cloud9** một môi trường lập trình tích hợp trên đám mây (IDE) mà bạn có thể viết, chạy hoặc sửa lỗi code của bạn ở cùng một nơi. Nó bao gồm trình soạn mã, trình sửa lỗi, và terminal. Trong workshop này,bạn sử dụng AWS Cloud9 một ứng dụng backend, được xây dựng bởi AWS Serverless Application Model (AWS SAM). Bạn sẽ sử dụng AWS Amplify để triển khai phần frontend.
