---
title : "Thiết lập CloudFormation template"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---
#### Thiết lập CloudFormation template

Để thực hiện các bước tiếp theo trong workshop này, bạn cần tạo một số tài nguyên trong AWS region đã được chọn sử dụng template *CloudFormation** .

1. Tải **CloudFormation** template: {{% button href="https://static.us-east-1.prod.workshops.aws/public/6013a24d-678a-4774-81ee-46b66fb4fcab/static/template/ws-startup-stack.yaml" icon="fas fa-download" icon-position="right" %}}Tải về{{% /button %}}
 

 {{%expand "(Nhấn vào) CloudFormation Template này làm những gì? (với giải thích)"%}}
1. **Xác định Tham số**: Bước này xác định các tùy chọn cấu hình mà bạn có thể tùy chỉnh khi khởi chạy stack **CloudFormation** . ác tùy chọn này bao gồm tên môi trường, chi tiết instance **Cloud9** (tên, loại, kích thước, chủ sở hữu) và thời gian dừng tự động cho Cloud9.

2. **Tạo VPC** (Không bắt buộc): Nếu không có VPC mặc định, bước này sẽ tạo một VPC mới với các subnet công cộng và riêng tư, internet gateway và bảng định tuyến để cho phép truy cập internet.

3. **Tạo IAM Roles**: Bước này tạo ba **IAM roles**:

- `C9Role`:  Quyền này cung cấp quyền truy cập đầy đủ (chính sách AdministratorAccess) cho instance **Cloud9**.
- `KendraS3DocsRole` và `KendraIndexRole`: : Các quyền này cấp quyền cho dịch vụ Kendra truy cập các bucket S3, nhật ký **CloudWatch** và mô tả các nhóm nhật ký.

5. **Tạo instance Cloud9**: Bước này tạo một môi trường phát triển **Cloud9** trong subnet VPC được chỉ định. Loại instance, kích thước và chủ sở hữu có thể được cấu hình trong quá trình khởi chạy.

6. **Tạo S3 Bucket**:  Bước này tạo một **S3 bucket** iêng tư để lưu trữ tài liệu cho việc lập chỉ mục **Kendra**. Truy cập công khai vào bucket bị vô hiệu hóa.

7. **Tạo Chỉ mục Kendra:** Bước này tạo một chỉ mục Kendra được đặt theo tên ngăn xếp CloudFormation với **"DEVELOPER_EDITION"**.

8. **Tạo Nguồn Dữ liệu S3**: Bước này tạo một nguồn dữ liệu Kendra được liên kết với bucket S3 bạn đã tạo trước đó. Nó chỉ định tiền tố "sample-documents/" để bao gồm các tài liệu từ thư mục đó trong bucket S3 để lập chỉ mục.

9. **Kết quả**:  Bước này xác định các đầu ra sẽ khả dụng sau khi ngăn xếp CloudFormation được triển khai. Các đầu ra này bao gồm tên bucket S3, ID chỉ mục Kendra, vùng AWS nơi ngăn xếp được triển khai và URL môi trường Cloud9.

```yaml
##This CloudFormation template creates an Amazon Kendra index. It adds a webcrawler datasource
##to the index and crawls the online AWS Documentation for Amazon Kendra, Amazon Lex and Amazon SageMaker
##After the datasource is configured, it triggers a datasource sync, i.e. the process to crawl the sitemaps
##and index the crawled documents.
##The output of the CloudFormation template shows the Kendra index id and the AWS region it was created in.
##It takes about 30 minutes to create an Amazon Kendra index and about 15 minutes more to crawl and index
##the content of these webpages to the index. Hence you might need to wait for about 45 minutes after
##launching the CloudFormation stack

Parameters:
 
  EnvironmentName:
    Description: An environment name that is tagged to the resources.
    Type: String
    Default: Workshop
  InstanceName:
    Description: Cloud9 instance name.
    Type: String
    Default: Workshop
  InstanceType:
    Description: The memory and CPU of the EC2 instance that will be created for Cloud9 to run on.
    Type: String
    Default: t3.medium
    AllowedValues:
      - t3.medium
      - t3.large
      - t3.xlarge
      - t3.2xlarge
      - m5.large
    ConstraintDescription: Must be a valid Cloud9 instance type
  InstanceVolumeSize:
    Description: The size in GB of the Cloud9 instance volume
    Type: Number
    Default: 16
  InstanceOwner:
    Type: String
    Description: Assumed role username of Cloud9 owner, in the format 'Role/username'. Leave blank to assign leave the instance assigned to the role running the CloudFormation template.
    Default: ""
  AutomaticStopTimeMinutes:
    Description: How long Cloud9 can be inactive (no user input) before auto-hibernating. This helps prevent unnecessary charges.
    Type: Number
    Default: 30

Metadata:
  AWS::CloudFormation::Interface:
    ParameterGroups:
      - Label:
          default: General configuration
        Parameters:
          - EnvironmentName
      - Label:
          default: Cloud9 configuration
        Parameters:
          - InstanceName
          - InstanceType
          - InstanceVolumeSize
          - InstanceOwner
          - AutomaticStopTimeMinutes
    ParameterLabels:
      EnvironmentName:
        default: Environment name
      InstanceName:
        default: Name
      InstanceType:
        default: Instance type
      InstanceVolumeSize:
        default: Attached volume size
      InstanceOwner:
        default: Role and username
      AutomaticStopTimeMinutes:
        default: Timeout

Conditions: 
  AssignCloud9Owner: !Not [!Equals [!Ref InstanceOwner, ""]]

Resources:
  ## Creating a VPC for Cloud9, this is required if no default VPC available. 
  LabVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
      Tags:
        - Key: Name
          Value: Lab VPC

  InternetGateway:
    Type: AWS::EC2::InternetGateway

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref LabVPC
      InternetGatewayId: !Ref InternetGateway

  PublicSubnet:
    Type: AWS::EC2::Subnet
    DependsOn: AttachGateway
    Properties:
      VpcId: !Ref LabVPC
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
      AvailabilityZone: !Select
        - 0
        - !GetAZs
      Tags:
        - Key: Name
          Value: Public Subnet

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    DependsOn: PublicSubnet
    Properties:
      VpcId: !Ref LabVPC
      Tags:
        - Key: Name
          Value: Public Route Table

  PublicRoute:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    DependsOn: PublicRoute
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable

################## PERMISSIONS AND ROLES #################
  C9Role:
    Type: AWS::IAM::Role
    Properties:
      Tags:
        - Key: Environment
          Value: AWS Example
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - ec2.amazonaws.com
          Action:
          - sts:AssumeRole
      ManagedPolicyArns:
      - arn:aws:iam::aws:policy/AdministratorAccess
      Path: "/"

################## INSTANCE #####################
  C9InstanceProfile:
    Type: AWS::IAM::InstanceProfile
    Properties:
      Path: "/"
      Roles:
      - Ref: C9Role

  C9Instance:
    DependsOn: PublicSubnetRouteTableAssociation
    Description: "-"
    Type: AWS::Cloud9::EnvironmentEC2
    Properties:
      Description: AWS Cloud9 instance for Examples
      Name: !Join
      - ''
      - - !Ref 'AWS::StackName'
        - '-c9'
      InstanceType: !Ref 'InstanceType'
      ImageId: 'ubuntu-22.04-x86_64'
      AutomaticStopTimeMinutes: !Ref 'AutomaticStopTimeMinutes'
      SubnetId: !Ref PublicSubnet
      OwnerArn: 
        Fn::If:
          - AssignCloud9Owner
          - !Sub arn:${AWS::Partition}:iam::${AWS::AccountId}:assumed-role/${InstanceOwner}
          - Ref: AWS::NoValue
      Tags: 
        - 
          Key: Environment
          Value: !Join
          - ''
          - - !Ref 'AWS::StackName'
            - '-BedrockServerlessC9'

    
  ## Create S3 bucket for Documents and disable version
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      AccessControl: Private
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        IgnorePublicAcls: true
        BlockPublicPolicy: true
        RestrictPublicBuckets: true 
  S3BucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref S3Bucket
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Action:
              - 's3:GetObject'
            Effect: Allow
            Resource: !Join
              - ''
              - - 'arn:aws:s3:::'
                - !Ref S3Bucket
                - '/*'
            Principal:
              Service: "kendra.amazonaws.com"
          
      
  #Create a different index
  S3DocsKendraIndex:
    Type: 'AWS::Kendra::Index'
    Properties:
      Name: !Join
        - ''
        - - !Ref 'AWS::StackName'
          - '-S3-Index'
      Edition: 'DEVELOPER_EDITION'
      RoleArn: !GetAtt KendraIndexRole.Arn

  #Docs Data Source
  S3DataSource:
    DependsOn: S3DocsKendraIndex
    Type: 'AWS::Kendra::DataSource'
    Properties: 
      IndexId: !Ref S3DocsKendraIndex
      Name: 'S3DocsDataSource'
      Type: 'S3'
      DataSourceConfiguration:
        S3Configuration:
          BucketName: !Ref S3Bucket
          InclusionPrefixes: 
            - sample-documents/
      RoleArn: !GetAtt KendraS3DocsRole.Arn
 
  ##Create the Role needed to create a Kendra Index
  KendraIndexRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Sid: ''
            Effect: Allow
            Principal:
              Service: kendra.amazonaws.com
            Action: 'sts:AssumeRole'
      Policies:
        - PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Resource: '*'
                Condition:
                  StringEquals:
                    'cloudwatch:namespace': 'Kendra'
                Action:
                  - 'cloudwatch:PutMetricData'
              - Effect: Allow
                Resource: '*'
                Action: 'logs:DescribeLogGroups'
              - Effect: Allow
                Resource: !Sub
                  - 'arn:aws:logs:${region}:${account}:log-group:/aws/kendra/*'
                  - region: !Ref 'AWS::Region'
                    account: !Ref 'AWS::AccountId'
                Action: 'logs:CreateLogGroup'
              - Effect: Allow
                Resource: !Sub
                  - 'arn:aws:logs:${region}:${account}:log-group:/aws/kendra/*:log-stream:*'
                  - region: !Ref 'AWS::Region'
                    account: !Ref 'AWS::AccountId'
                Action: 
                  - 'logs:DescribeLogStreams'
                  - 'logs:CreateLogStream'
                  - 'logs:PutLogEvents'
          PolicyName: !Join
            - ''
            - - !Ref 'AWS::StackName'
              - '-S3DocsKendraIndexPolicy'
      RoleName: !Join
        - ''
        - - !Ref 'AWS::StackName'
          - '-S3DocsKendraIndexRole'

  ##Create the Role needed to attach the Webcrawler Data Source
  KendraS3DocsRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Sid: ''
            Effect: Allow
            Principal:
              Service: kendra.amazonaws.com
            Action: 'sts:AssumeRole'
      Policies:
        - PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Resource: 
                  - !GetAtt S3Bucket.Arn
                  - !Join
                    - ''
                    - - !GetAtt S3Bucket.Arn
                      - '/*'
                Action:
                  - 's3:GetObject'
                  - 's3:ListBucket'
          PolicyName: !Join
            - ''
            - - !Ref 'AWS::StackName'
              - '-S3DocsDSPolicy'
      RoleName: !Join
        - ''
        - - !Ref 'AWS::StackName'
          - '-S3DocsRole'

Outputs:
  S3BucketName:
    Value: !Ref S3Bucket
  KendraIndexID:
    Value: !Ref S3DocsKendraIndex
  AWSRegion:
    Value: !Ref 'AWS::Region'
  Cloud9URL:
    Description: Cloud9 Environment
    Value:
      Fn::Join:
      - ''
      - - !Sub https://${AWS::Region}.console.aws.amazon.com/cloud9/ide/
        - !Ref 'C9Instance'
```


**Tổng quan kết quả**:

Việc triển khai template **CloudFormation** này sẽ tạo ra các tài nguyên sau:

- Một VPC với quyền truy cập internet (nếu không có VPC mặc định)
- Một môi trường phát triển **Cloud9**
- Một **bucket S3** để lưu trữ tài liệu
- Một **chỉ mục Kendra** để tìm kiếm tài liệu trong bucket S3
- Một nguồn dữ liệu **Kendra** để kết nối bucket S3 với chỉ mục Kendra

The **CloudFormation** template creation itself should be very quick. However, it takes additional time for provisioning the resources:

Việc tạo mẫu **CloudFormation** sẽ diễn ra rất nhanh. Tuy nhiên, cần thêm thời gian để cung cấp tài nguyên:

Ước tính 30 phút để tạo phiên bản **Cloud9** và chỉ mục **Kendra**.
Mất thêm khoảng 15 phút (ước tính) để thu thập và lập chỉ mục các tài liệu được chỉ định trong nguồn dữ liệu.

Do đó, sau khi khởi chạy ngăn xếp, **bạn có thể cần chờ khoảng 45 phút trước khi có thể sử dụng chỉ mục Kendra để tìm kiếm tài liệu.**
{{% /expand%}}

---
2. Lưu trữ tệp template YAML trong một thư mục trên máy tính cục bộ của bạn.

3. Điều hướng đến CloudFormation trong AWS Management Console:  [AWS Management Console](https://console.aws.amazon.com/cloudformation/home#/stacks/new?region=us-east-1) .
![2_1](/images/2/2_1.png?featherlight=false "CloudFormation")
{{% notice tip %}}
Chọn region phù hợp với bạn. (Workshop này sử dụng us-east-1)
{{% /notice %}}
4. Trong **CloudFormation** console,chọn tạo stack và **Upload a template file**.
![2_2](/images/2/2_2.png?featherlight=false "CreateStack")


5. Tải template mà bạn đã download lên và chọn **Next**.
   ![2_3](/images/2/2_3.png?featherlight=false "CreateStack")
6. Cho Cloudformation stack của bạn một cái tên `bedrock-workshop-environment`.
   ![2_4](/images/2/2_4.png?featherlight=false "CreateStack")

7. Đối với Configure stack options,giữ các giá trị mặc định và chọn **Next**.
   ![2_5](/images/2/2_5.png?featherlight=false "CreateStack")

8. Xem lại các tham số và  thông báo IAM resource. chọn I acknowledge that **AWS CloudFormation** might create IAM resources.
9.  Để triển khai ta chọn **Submit**.
   ![2_6](/images/2/2_6.png?featherlight=false "CreateStack")

10. Sau khi template đã được trển khai ta vào [CloudFormation Resources](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/resources?) , bạn nên thấy được **CloudFormation stack** đã tạo trước đó. 

   ![2_9](/images/2/2_9.png?featherlight=false "CloudFormation Stack")
