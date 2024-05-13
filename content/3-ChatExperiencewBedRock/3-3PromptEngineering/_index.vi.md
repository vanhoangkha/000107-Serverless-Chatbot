---
title : "Kỹ thuật Prompt Engineering trong Chatbots"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---

#### Tổng quan về Kỹ thuật Prompt Engineering

Kỹ thuật Prompt Engineering là một lĩnh vực mới nổi tập trung vào việc phát triển các prompt (nhắc nhở) được tối ưu hóa để áp dụng hiệu quả các mô hình ngôn ngữ cho các tác vụ khác nhau. Kỹ thuật Prompt Engineering giúp các nhà nghiên cứu hiểu được khả năng và giới hạn của các mô hình ngôn ngữ lớn (LLMs). Bằng cách sử dụng các kỹ thuật Prompt Engineering khác nhau, bạn thường có thể nhận được câu trả lời tốt hơn từ các mô hình nền tảng mà không cần tốn công sức và chi phí cho việc đào tạo lại hoặc tinh chỉnh chúng.

Kỹ thuật Prompt Engineering tận dụng nguyên tắc “tiền kích hoạt”: cung cấp cho mô hình ngữ cảnh của một vài ví dụ (3-5) về những gì người dùng mong đợi đầu ra sẽ như thế nào, để mô hình bắt chước hành vi “được kích hoạt trước” trước đó. Bằng cách tương tác với LLM thông qua một loạt các câu hỏi, phát biểu hoặc hướng dẫn, người dùng có thể điều hướng hiệu quả sự hiểu biết của LLM và điều chỉnh hành vi của nó theo ngữ cảnh cụ thể của cuộc trò chuyện.

Chất lượng đầu ra của mô hình phụ thuộc vào chất lượng của prompt mà chúng tôi cung cấp. Một số khía cạnh chính của kỹ thuật Prompt Engineering bao gồm:

- **Lựa chọn từ ngữ**: Chọn các từ và cụm từ trong prompt gợi ra ngữ điệu, phong cách và nội dung mong muốn từ AI. Sử dụng ngôn ngữ tích cực, mang tính xây dựng hơn thường mang lại kết quả tốt hơn.

- **Cung cấp ngữ cảnh**: Cung cấp cho AI thông tin nền tảng và các ví dụ liên quan để giúp hướng dẫn nó tới loại phản hồi mà bạn muốn.

- **Cấu trúc logic**: Xây dựng prompt theo cách rõ ràng, logic để giúp AI hiểu những gì bạn đang yêu cầu. Chia nhỏ prompt thành các bước đơn giản hơn hoặc các dấu đầu dòng có thể hữu ích.

- **Giới hạn phạm vi**: Giữ cho các prompt tập trung vào các yêu cầu hẹp, được xác định rõ ràng thay vì các yêu cầu rộng, mơ hồ. Điều này giúp giảm thiểu nhầm lẫn và cải thiện phản hồi của AI.

- **Kiểm soát độ dài**: Tìm sự cân bằng phù hợp giữa sự cô đọng và chi tiết đầy đủ. Các prompt quá dài có thể khó theo dõi, nhưng các prompt quá ngắn có thể thiếu ngữ cảnh cần thiết.

- **Diễn đạt lại**: Thử các cách diễn đạt khác nhau nếu prompt ban đầu không mang lại kết quả mong muốn. Những điều chỉnh nhỏ có thể tạo ra sự khác biệt lớn trong việc điều chỉnh phản hồi của AI.

- **Cung cấp phản hồi**: Cho AI biết khi phản hồi của nó đi đúng hướng hoặc đi chệch hướng. Điều này hướng dẫn AI tới những câu trả lời tốt hơn.



Trong workshop này, các prompt được lấy từ `s3://{S3BucketName}/prompt-engineering/`. Các mẫu prompt được thiết kế riêng cho các mô hình Claude, Titan và Jurassic. Trong phần sắp tới, bạn có cơ hội để thử nghiệm với hai mẫu prompt khác nhau, một mẫu được soạn kém và một mẫu được xây dựng tốt bằng cách sửa đổi ngữ cảnh của prompt.
#### Kỹ thuật Prompt Engineering với Mô hình Claude

Chúng ta hãy tiến hành bằng cách cập nhật mẫu prompt cho mô hình Claude và quan sát cách các phản hồi khác nhau. Đầu tiên, hãy bắt đầu với một prompt được xây dựng kém. Thực hiện theo các bước sau để cập nhật mẫu prompt:

1. Mở môi trường AWS Cloud9 và mở tệp  `/bedrock-serverless-workshop/prompt-engineering/claude-prompt-template.txt` .

2. Sao chép mẫu prompt được xây dựng kém sau đây, sau đó cập nhật tệp  `/prompt-engineering/claude-prompt-template.txt`.
```text
Human: I got a question here: {question}.
{context}.

Assistant:
```
3. Lưu tệp và trong terminal AWS Cloud9, chạy lệnh sau để sao chép tệp prompt thử nghiệm vào thư mục bucket S3..

```bash
cd ~/environment/bedrock-serverless-workshop/
aws s3 cp prompt-engineering/claude-prompt-template.txt s3://${S3BucketName}/prompt-engineering/
```
   ![3_5](/images/3/3_5.png "Upload prompt template to S3")

**Note**: If the upload fails because of {S3BucketName}, run the export command you used in an earlier step to set the environment variable.

1. Return to the Amplify app browser tab  (switch to Cladue Model) and ask the following questions.

Câu hỏi 1:
```text
What is the federal funds rate as of September 2024?

```
Câu hỏi này không có ý định có câu trả lời vì nó liên quan đến tương lai. Tuy nhiên, do prompt được xây dựng kém, câu hỏi tạo ra một phản hồi sai lầm giả định rằng tỷ lệ sẽ vẫn như cũ từ năm 2023.

   ![3_6](/images/3/3_6.png "Sample question")


Câu hỏi   2:
```text
What is the federal funds rate as of September 2023?

```
   ![3_7](/images/3/3_7.png "Sample question")

Lần này, bạn nhận được câu trả lời chính xác, nhưng hãy lưu ý rằng nó có thể không được tinh chỉnh hoặc trau chuốt như mong muốn.

Bây giờ, hãy thử với một prompt hiệu quả. Quay lại cửa sổ AWS Cloud9 và thay thế prompt trong tệp claude-prompt-template.txt bằng prompt được xây dựng tốt sau.

```text
Human: We are conducting a generative AI workshop. Please carefully construct your response for the question from the given context only - {context}.

It's very important that your answer is within the context. If you cannot derive a satisfying response, please respond "Sorry, I don't know the answer."

Here is the question you should process: {question}

Assistant:
```
1. Lưu tệp và quay lại terminal AWS Cloud9. Để tải lên tệp đã cập nhật lên thư mục bucket S3, hãy chạy lệnh sao chép S3.

2. Quay lại tab trình duyệt ứng dụng Amplify và thử nghiệm Câu hỏi 1 và 2.

Với một prompt hiệu quả, phản hồi của bạn được tinh chỉnh hơn đáng kể và phù hợp với ngữ cảnh.
   ![3_8](/images/3/3_8.png "Sample question")
   ![3_9](/images/3/3_9.png "Sample question")

Bạn có thể tiếp tục tinh chỉnh mẫu prompt của mình và thử nghiệm các câu hỏi của mình. Kỹ thuật Prompt Engineering hiệu quả đóng vai trò quan trọng trong việc hướng dẫn hành vi mô hình và đạt được các phản hồi mong muốn.

{{% notice note %}}
Khi bạn làm việc với Mô hình Jurassic 2, hãy sử dụng  `bedrock-serverless-workshop/prompt-engineering/jurassic2-prompt-template.txt` và chuyển đổi tùy chọn để chọn nó..
{{% /notice %}}