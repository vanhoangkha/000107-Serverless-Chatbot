---
title : "Lập chỉ mục dữ liệu mẫu bằng cách sử dụng Amazon Kendra"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---

#### Lập chỉ mục dữ liệu mẫu bằng cách sử dụng Amazon Kendra

Tải xuống dữ liệu mẫu: 
Tải xuống một vài tài liệu mẫu để kiểm tra giải pháp này. Tài liệu đầu tiên liên quan đến biên bản cuộc họp tháng 9 năm 2023 của **Ủy ban Thị trường Mở Liên bang (FOMC)**. Tài liệu thứ hai là **Báo cáo Phát triển Bền vững của Amazon năm 2022**. Tài liệu thứ ba là **báo cáo 10K của Henry Schein**, một nhà cung cấp dịch vụ nha khoa. Bạn có thể tải xuống hoặc sử dụng bất kỳ tài liệu nào để kiểm tra giải pháp này, hoặc bạn có thể mang dữ liệu của riêng mình để tiến hành kiểm tra.

Để sao chép các mẫu prompt và tài liệu mẫu bạn đã tải xuống vào S3 Bucket, hãy chạy lệnh sau

```bash
cd ~/environment/bedrock-serverless-workshop
mkdir sample-documents
curl https://www.federalreserve.gov/monetarypolicy/files/fomcminutes20230920.pdf --output sample-documents/fomcminutes20230920.pdf
curl https://sustainability.aboutamazon.com/2022-sustainability-report.pdf --output sample-documents/2022-sustainability-report-amazon.pdf
curl https://investor.henryschein.com/static-files/fcf569ec-fdbb-4591-b73d-0d1d849efd78 --output sample-documents/2022-hs1-10k.pdf
```
Các lệnh này sẽ tạo thư mục `sample-documents` trong môi trường **Cloud9** của chúng tôi và sử dụng lệnh `curl` để lấy một số tài liệu mẫu vào đó.



   ![2_19](/images/2/2_19.png "Curl command image")

#### Tải lên tài liệu mẫu và mẫu prompt

Để sao chép các mẫu prompt và tài liệu mẫu bạn đã tải xuống vào **S3 Bucket**, hãy chạy lệnh sau.

```bash
cd ~/environment/bedrock-serverless-workshop
aws s3 cp prompt-engineering s3://$S3BucketName/prompt-engineering/ --recursive
aws s3 cp sample-documents s3://$S3BucketName/sample-documents/ --recursive

```
   ![2_20](/images/2/2_20.png "Upload to S3")

Sau khi tải lên thành công, hãy xem lại bảng điều khiển **Amazon S3** và mở bucket. Bạn sẽ thấy nội dung tương tự như hình ảnh này:
   ![2_21](/images/2/2_21.png "S3 Bucket")
   ![2_22](/images/2/2_22.png "S3 Bucket storage")


#### Lập chỉ mục các tài liệu mẫu bằng cách sử dụng Amazon Kendra

**Amazon Kendra** ià dịch vụ tìm kiếm thông minh được hỗ trợ bởi Machine Learning (ML). Amazon Kendra giúp bạn định nghĩa lại tìm kiếm doanh nghiệp cho các trang web và ứng dụng của mình, để nhân viên và khách hàng của bạn có thể tìm thấy nội dung họ đang tìm kiếm, ngay cả khi nội dung đó nằm rải rác trên nhiều vị trí và kho lưu trữ nội dung trong tổ chức của bạn.

Chỉ mục Amazon Kendra và nguồn dữ liệu Amazon S3 đã được tạo trong quá trình cung cấp ban đầu cho workshop này. Trong tác vụ này, bạn sẽ lập chỉ mục tất cả các tài liệu trong **nguồn dữ liệu S3**( **S3 data source**).

1. Đăng nhập vào AWS Management Console, sau đó nhập Kendra vào hộp tìm kiếm trên bảng điều khiển. Kiểm tra để đảm bảo tài khoản AWS của bạn có quyền truy cập vào Amazon Kendra, sau đó chọn Vùng AWS nơi workshop này đang hoạt động.
   ![2_23](/images/2/2_23.png "Kendra")

   ![2_24](/images/2/2_24.png "Kendra enviroment")
2. Ở phía trên bên trái của bảng điều khiển Amazon Kendra, hãy chọn biểu tượng menu (dấu ba chấm), sau đó, trong ngăn điều hướng, chọn Chỉ mục (Indexes).
   ![2_25](/images/2/2_25.png "Kendra enviroment")

3. Chọn enviroment. Nhấn vào tên chỉ mục.
   ![2_26](/images/2/2_26.png "Kendra enviroment")

4. Để bắt đầu đánh chỉ mục từ thư mục `sample-documents` , chọn `S3DocsDataSource`, và chon **Sync now** (Đồng bộ ngay lập tức). Quá trình đánh chỉ mục sẽ tốn vài phút. Hãy đợi nó hoàn thành.
   ![2_27](/images/2/2_27.png "Kendra enviroment")

   5. Để query chỉ mục của **Amazon Kendra**  với một số tài liệu mẫu ở phái bảng bên phải, chọn `Search indexed content`, và bắt đầu câu hỏi.
   ![2_28](/images/2/2_28.png "Sucessful annoucement")
**Câu hỏi mẫu**:

```text
What is the federal funds rate as of September 2023?

```
   ![2_29](/images/2/2_29.png "Kendra query")
