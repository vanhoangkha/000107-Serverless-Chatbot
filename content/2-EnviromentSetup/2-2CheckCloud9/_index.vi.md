---
title : "Truy cập Cloud9 và thiết lập môi trường"
date: 2024-01-01
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

#### Truy cập Cloud9

Đê thiết lập môi trường,bạn mở **AWS Cloud9** theo hướng dẫn sau:

1. Từ **AWS Management Console**, ta chọn **AWS Cloud9**.
   ![2_7](/images/2/2_7.png?featherlight=false "Clou9Search")

2. Trong mục **Environments**, với `bedrock-workshop-environment`, dưới **Cloud9** IDE, chọn **Open**.
   ![2_8](/images/2/2_8.png?featherlight=false "Open Cloud 9")

3. Xác nhận môi trường **AWS Cloud9** đã hoạt động hoàn tòa.

  - Bạn có thể đóng tab Welcome.

  - Bạn có thể kéo các tab đến bất kì vị trí nào bạn thích:
  
   ![2_10](/images/2/2_10.png?featherlight=false "Cloud 9 Main Interface")


#### Thiết lập môi trường

Sau khi đã có tên stack từ bước vừa rồi, thay thế <your-startup-stack-name> với tên đã được copy,chạy các câu lệnh sau. Thêm nữa, đảm bảo biến môi trường **S3BucketName** đã được gán đúng, đây là bucket được sử dụng để lưu trữ dữ liệu và kiểm tra trong workshop màu.

```bash
export CFNStackName=<you-startup-stack-name>
export S3BucketName=$(aws cloudformation describe-stacks --stack-name ${CFNStackName} --query "Stacks[0].Outputs[?OutputKey=='S3BucketName'].OutputValue" --output text)
echo "S3 bucket name: $S3BucketName"
```
Ví dụ:
   ![2_11](/images/2/2_11.png?featherlight=false "Enviroment variable")
