---
title : "Bật các Mô hình Nền tảng trong Amazon Bedrock"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
Sử dụng **Amazon Bedrock** để xây dựng và mở rộng các ứng dụng AI sinh thành với **FM** (Foundation Models - Mô hình Nền tảng). **Amazon Bedrock** là một dịch vụ được quản lý hoàn toàn, giúp các FM từ các công ty khởi nghiệp AI hàng đầu và Amazon có sẵn thông qua API. Bạn có thể lựa chọn từ nhiều loại FM khác nhau để tìm mô hình phù hợp nhất với trường hợp sử dụng của mình. Với trải nghiệm serverless của **Amazon Bedrock**, bạn có thể bắt đầu nhanh chóng, tùy chỉnh riêng các FM với dữ liệu của mình và tích hợp, triển khai chúng nhanh chóng vào các ứng dụng của mình bằng cách sử dụng các công cụ AWS mà không cần phải quản lý bất kỳ cơ sở hạ tầng nào.




Đăng nhập vào **AWS Management Console**, sau đó nhập `Bedrock` vào hộp tìm kiếm trên bảng điều khiển. Kiểm tra để đảm bảo rằng tài khoản AWS của bạn có quyền truy cập vào Amazon Bedrock, sau đó chọn Vùng AWS nơi workshop này đang chạy; ví dụ: US East (N. Virginia).
   ![2_30](/images/2/2_30.png "Request Bedrock model access")

#### Kích hoạt quyền truy cập mô hình

Theo mặc định, tài khoản AWS của bạn không có quyền truy cập vào bất kỳ mô hình nào và bạn cần quyền truy cập vào các mô hình **Claude** và **Jurassic** để hoàn thành workshop này.

1.Ở phía trên bên trái của bảng điều khiển **Amazon Bedrock**, chọn biểu tượng menu. Trong ngăn điều hướng, chọn Truy cập mô hình (**Model access**), sau đó chọn Quản lý Truy cập Mô hình (**Manage Model Access**).
   ![2_31](/images/2/2_31.png "Request Bedrock model access")

ChỌN **Anthropic Claude, AI21 Labs Jurassic-2 Ultra models**.

{{% notice note %}}
Bạn có thể cần xác minh chi tiết trường hợp sử dụng với mô hình Amazon Claude.
{{% /notice %}}
   ![2_32](/images/2/2_32.png "Request Bedrock model access")

Chọn Yêu cầu quyền truy cập mô hình (**Request model access**).
   ![2_33](/images/2/2_33.png "Request Bedrock model access")

#### Trò chuyện với mô hình

Ở phía trên bên trái của bảng điều khiển **Amazon Bedrock**, chọn biểu tượng menu. Trong Khu vui chơi (**Playgrounds**), chọn Trò chuyện (**Chat**).

Bạn cũng có thể dành thời gian này để điều hướng đến các tùy chọn khác nhau trên menu bên trái.
   ![2_34](/images/2/2_34.png "Select Bedrock model access")

Trên menu dropdown Nhà cung cấp (**Selection provider**), chọn mô hình **Anthropic** và **Claude 2 v2**.
   ![2_35](/images/2/2_35.png "Select Bedrock model access")


Bên cạnh Con người (**Human**), nhập câu hỏi vào hộp văn bản, rồi chọn Chạy (**Run**). Ví dụ:  **"What is the distance to the moon from Earth?"**
   ![2_36](/images/2/2_36.png "Select Bedrock model access")

Mô hình trả về phản hồi tương tự như sau:
   ![2_37](/images/2/2_37.png "Select Bedrock model access")
