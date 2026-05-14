---
title : "Set up CloudFormation template"
date: 2024-01-01
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---
#### Launch a CloudFormation template

In order to complete the upcoming steps in this workshop, you'll need to create some resources in your chosen AWS region using a **CloudFormation** template.

1. Download the **CloudFormation** template: {{% button href="https://static.us-east-1.prod.workshops.aws/public/6013a24d-678a-4774-81ee-46b66fb4fcab/static/template/ws-startup-stack.yaml" icon="fas fa-download" icon-position="right" %}}Download{{% /button %}}
 

 {{%expand "(Click) What this template file do? (with explanation)"%}}
1. **Define Parameters**: This step defines configuration options that you can customize when launching the **CloudFormation** stack. These options include environment name, **Cloud9** instance details (name, type, size, owner), and automatic stop time for Cloud9.

2. **Create VPC** (Optional): If a default VPC is not available, this step creates a new VPC with public and private subnets, internet gateway, and route tables to allow internet access.

3. **Create IAM Roles**: This step creates three **IAM roles**:

- `C9Role`: This role gives full access (AdministratorAccess policy) to the **Cloud9** instance.
- `KendraS3DocsRole` and `KendraIndexRole`: These roles grant Kendra service permissions to access S3 buckets, **CloudWatch** logs, and describe log groups.

4. **Create Cloud9 Instance**: This step creates a **Cloud9** development environment in the specified VPC subnet. The instance type, size, and owner can be configured during launch.

5. **Create S3 Bucket**: This step creates a private **S3 bucket** to store documents for **Kendra** indexing. Public access to the bucket is disabled.

6. **Create Kendra Index:** This step creates a Kendra index named after the CloudFormation stack with the "DEVELOPER_EDITION".

7. **Create S3 Data Source**: This step creates a Kendra data source linked to the S3 bucket you created earlier. It specifies the "sample-documents/" prefix to include documents from that folder within the S3 bucket for indexing.

8. **Outputs**: This step defines the outputs that will be available after the CloudFormation stack is deployed. These outputs include the S3 bucket name, Kendra index ID, AWS region where the stack was deployed, and the Cloud9 environment URL.


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


**Overall Result**:

Deploying this **CloudFormation** template will create the following resources:

- A VPC with internet access (if no default VPC exists)
- A **Cloud9** development environment
- An **S3** bucket for storing documents
- A **Kendra** index to search the documents in the S3 bucket
- A **Kendra** data source to connect the S3 bucket to the Kendra index

The **CloudFormation** template creation itself should be very quick. However, it takes additional time for provisioning the resources:

Approximately 30 minutes to create the Cloud9 instance and Kendra index.
An additional 15 minutes (approximately) to crawl and index the documents specified in the data source.

So, after launching the stack, **you might need to wait around 45 minutes before being able to use the Kendra index to search the documents**.
{{% /expand%}}

---
2. Store the YAML template file in a folder on your local machine.

3. Navigate to **CloudFormation** in the [AWS Management Console](https://console.aws.amazon.com/cloudformation/home#/stacks/new?region=us-east-1) .
![2_1](/images/2/2_1.png?featherlight=false "CloudFormation")
{{% notice tip %}}
Choose a location that suits you. (This workshop use us-east-1)
{{% /notice %}}
4. On the **CloudFormation** console, choose create stack and **Upload a template file**.
![2_2](/images/2/2_2.png?featherlight=false "CreateStack")


5. Select the template that you just downloaded, and then choose **Next**.
   ![2_3](/images/2/2_3.png?featherlight=false "CreateStack")
6. Give the stack a name, such as `bedrock-workshop-environment`.
   ![2_4](/images/2/2_4.png?featherlight=false "CreateStack")

7. For Configure stack options, keep the default values and choose **Next**.
   ![2_5](/images/2/2_5.png?featherlight=false "CreateStack")

8. Review the parameters and the IAM resource notice. Select I acknowledge that **AWS CloudFormation** might create IAM resources.
9.  To deploy the template, choose **Submit**.
   ![2_6](/images/2/2_6.png?featherlight=false "CreateStack")

10. After the template is deployed, to review the created resources, navigate to [CloudFormation Resources](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/resources?) , you should see the **CloudFormation** stack that you created.

   ![2_9](/images/2/2_9.png?featherlight=false "CloudFormation Stack")
