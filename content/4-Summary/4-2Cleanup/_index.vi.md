---
title : "Dọn dẹp tài nguyên"
date: 2024-01-01
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

#### Xóa ứng dụng AWS SAM và ngăn xếp CloudFormation khởi động
Điều hướng đến terminal AWS **Cloud9**, sau đó chạy các lệnh sau:

1. Để xóa stack **SAM**, hãy chạy lệnh sau.
```bash
cd ~/environment/bedrock-serverless-workshop
sam delete 
```
2. Để xóa ngăn xếp khởi động, hãy chạy lệnh sau.

```bash
aws cloudformation delete-stack --stack-name $CFNStackName
```
   ![4_1](/images/4/4_1.png "Clean up Resources")
Hãy nhớ kiểm tra cả chỉ mục **Kendra**.
   ![4_2](/images/4/4_2.png "Clean up Resources")
