---
title : "Trò chuyện đơn giản với Giao diện người dùng Chatbot"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---
 #### Các tham số Prompt
Các tham số Prompt đề cập đến các hướng dẫn, nguyên tắc hoặc đầu vào cụ thể được cung cấp cho một FM để tác động đến đầu ra được tạo. Các tham số này rất cần thiết để tinh chỉnh phản hồi của mô hình và đảm bảo rằng văn bản được tạo ra phù hợp với ngữ cảnh hoặc mục tiêu mong muốn.

**Prompt**: đầu vào văn bản được cung cấp cho mô hình để bắt đầu tạo phản hồi. Một prompt có thể ở dạng câu hỏi, câu lệnh hoặc câu chưa hoàn thành. Chất lượng và tính cụ thể của prompt có thể ảnh hưởng rất lớn đến đầu ra. Trong tác vụ tiếp theo, bạn sẽ trải nghiệm các phương pháp kỹ thuật prompt khác nhau.

**Model temperature**:một tham số kiểm soát tính ngẫu nhiên của văn bản được tạo. Giá trị cao hơn (chẳng hạn như 0,9 và 1) làm cho đầu ra đa dạng và sáng tạo hơn. Giá trị thấp hơn (chẳng hạn như 0 và 0,1) làm cho đầu ra tập trung và xác định hơn.
**Max tokens**:  một tham số giới hạn độ dài của phản hồi được tạo. Bạn có thể đặt số token tối đa để kiểm soát độ dài của đầu ra.
 
 #### Trò chuyện đơn giản với Giao diện người dùng Chatbot

Mở trình duyệt và truy cập vào liên kết **Amplify** UI trước đó. Nếu bạn chưa đăng nhập, hãy sử dụng thông tin xác nhận đã lấy trước đó để đăng nhập. Giao diện người dùng được hiển thị như sau:


Khi bạn nhập câu hỏi, bạn có thể mong đợi một câu trả lời kèm theo các tài liệu tham khảo có liên quan. Tuy nhiên, nếu không có tài liệu hỗ trợ hoặc nguồn dữ liệu nào khả dụng, bạn có thể nhận được phản hồi: "Xin lỗi, tôi không có câu trả lời."



Thử một số câu hỏi mẫu sau đây và xem lại các phản hồi được tạo bởi các FM của **Amazon Bedrock**.

```text
What is the role of FOMC?
```
   ![3_1](/images/3/3_1.png "Sample question")

```text
What is the federal funds rate as of September 2023?
```
   ![3_2](/images/3/3_2.png "Sample question")

```text
What are demographic trends in the dental space?
```
   ![3_3](/images/3/3_3.png "Sample question")

```text
What are the solar energy trends in 2023?
```
   ![3_4](/images/3/3_4.png "Sample question")

Bạn có thể thử nghiệm với các tham số prompt khác nhau (chẳng hạn như nhiệt độ mô hình và số token tối đa) để so sánh và tinh chỉnh phản hồi của mình.
