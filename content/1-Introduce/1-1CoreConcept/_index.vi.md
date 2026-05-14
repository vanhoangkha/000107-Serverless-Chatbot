---
title : "Một số khái niệm quan trọng"
date: 2024-01-01
weight : 1 
chapter : false
pre : " <b> 1.1 </b> "
---

#### Ai tạo sinh hoạt động như thế nào?

**Các mô hình AI tạo sinh** (đặc biệt là các mô hình ngôn ngữ lớn) try to **predict the next word** (hoặc token, là một khối văn bản) dựa trên chuỗi từ hiện tại. Các mô hình thực hiện điều này bằng cách tạo ra điểm số xác suất cho mỗi từ trong vốn từ của chúng và chọn từ có điểm số xác suất cao nhất cho lần tạo tiếp theo. Quá trình này được lặp lại cho đến khi toàn bộ câu hoặc đoạn văn bản được tạo.

![How do generative AI work?](/images/1/Img1_1.png?featherlight=false "How do generative AI work?")

#### AI tạo sinh khác với AI truyền thống như thế nào

Các mô hình AI truyền thống thực hiện các tác vụ ngôn ngữ tự nhiên (chẳng hạn như tạo văn bản, phát hiện cảm xúc, trả lời câu hỏi và phân loại văn bản) và các tác vụ dựa trên hình ảnh (chẳng hạn như phát hiện đối tượng và phân vùng ngữ nghĩa). . **Các mô hình truyền thống này cần được đào tạo với các bộ dữ liệu được gắn nhãn cho một nhiệm vụ cụ thể mỗi lần. Mặt khác, các mô hình AI tạo sinh được đào tạo với một lượng lớn dữ liệu không được gắn nhãn một lần và chúng có thể được sử dụng lại cho các nhiệm vụ khác nhau.** Do đó, các mô hình tạo sinh có nhiều kiến thức ngôn ngữ hơn và thể hiện khả năng học hỏi với một vài dữ liệu mẫu, điều mà các mô hình truyền thống không có. Chỉ với một vài thao tác,  các mô hình này có thể tạo ra văn bản mạch lạc theo phong cách mới hoặc về chủ đề mới.

![How is generative AI different from traditional AI?](/images/1/Img1_2.png?featherlight=false "How is generative AI different from traditional AI?")

#### Làm cách nào có thể sử dụng các mô hình AI tạo sinh?
Chúng ta **tương tác vwói các mô hình nền tảng (Foundation Model -FM) bằng các prompts - hiểu đơn giản là câu hỏi để tương tác hệ thống**. Prompt được sử dụng chủ yếu để giao tiếp với các FM văn bản sang văn bản. Trong prompt, chúng ta **phúng tôi cung cấp hướng dẫn cho FM để tạo nội dung mới.** Viết các prompt tốt  giúp nhận được phản hồi tốt nhất từ ​​các FM. Ví dụ về prompt bao gồm câu hỏi, hướng dẫn hoặc mô tả chi tiết. Kỹ thuật Prompt có thể liên quan đến việc diễn đạt truy vấn, chỉ định một phong cách xuất ra nhất định, cung cấp ngữ cảnh liên quan và gán một vai trò (chẳng hạn như "hành động như một trợ lý con người") cho AI. Bạn sẽ tìm hiểu thêm về kỹ thuật Prompt trong workshop này.

#### Retrieval-Augmented Generation (RAG)
Xem xét một Q&A dựa trên cơ sở kiến thức của một tổ chức cụ thể. Mặc dù các FM được đào tạo trên các bộ dữ liệu rất lớn, chúng có thể không được đào tạo trên dữ liệu hoặc cơ sở kiến thức cụ thể của một tổ chức nào đó.. Về bản chất, **chúng ta có thể cung cấp cơ sở dữ liệu kiến thức của mình trong prompt và hướng dẫn FM trả lời một câu hỏi dựa trên nội dung được cung cấp đó**.

Chúng ta có thể gửi tất cả cơ sở dữ liệu kiến thức trong một lần và yêu cầu mô hỉnh trả lời? Bởi vì các mô hình thường có giới hạn về số lượng văn bản có thể xử lý cùng một lúc, **chúng ta cần một cách để lưu trữ nội dung ở một nơi khác, truy xuất nội dung có liên quan nhất và gửi nội dung có liên quan đến FM để tăng cường khả năng tạo văn bản của nó. Do đó có thuật ngữ Retrieval-Augmented Generation (RAG).**
![Retrieval-Augmented Generation (RAG)](/images/1/Img1_3.png?featherlight=true "Retrieval-Augmented Generation (RAG)")
