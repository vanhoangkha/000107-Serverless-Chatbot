---
title : "Triển khai Ứng dụng Serverless Amazon Bedrock"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

Trong tác vụ này, bạn sẽ thực hiện các bước để xây dựng và triển khai các dịch vụ backend. Việc triển khai tạo ra một ứng dụng serverless, nơi UI sẽ gọi các cuộc gọi API dựa trên REST để kích hoạt các API của **Amazon Bedrock**. Sau khi mã được triển khai, nó sẽ khởi chạy **Amazon API Gateway** và một hàm **AWS Lambda**.

#### Xây dựng và triển khai ứng dụng

Mở môi trường **AWS Cloud9**, sau đó clone mã nguồn bằng lệnh sau:

```bash
cd ~/environment
git clone https://github.com/aws-samples/bedrock-serverless-workshop.git
```

Để xây dựng và triển khai backend và frontend của ứng dụng serverless, hãy chạy lệnh sau.

```bash
cd ~/environment/bedrock-serverless-workshop
chmod +x startup.sh
./startup.sh
```

Chúng ta sẽ chạy script bash `startup.sh` trong kho lưu trữ đó. Chúng ta sẽ thảo luận về nó:

``` bash
#=================*   1  *================== 
#!/bin/bash
echo "Preparing Cloud9 for project deploymnet"
# Check if 'CFNStackName' is set in the environment variables
if [ -z "$CFNStackName" ]; then
    echo "Error: 'CFNStackName' environment variable is not set. Please set it and run the script."
    exit 1
fi
echo "CFN Start up stack name: $CFNStackName"


echo "Update ubantu os"
sudo apt-get update

echo "Install jq for cli query operations"
sudo apt install -y jq

#echo "Resize the Cloud9 to 20 GB space"
#cd ~/environment/bedrock-serverless-workshop/tools
#chmod +x resize.sh
#./resize.sh 20

echo "Update node js to version 16"
source "$HOME/.nvm/nvm.sh"
nvm install 16
nvm use 16
node --version

#=================   2  ================== 
echo "Set region"
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo "AWS Region is $AWS_REGION"

echo "Build the backend code using sam build"
cd ~/environment/bedrock-serverless-workshop
sam build

echo "Export S3 bucket name and Kendra index which are created as part of Startup CFN stack"
export S3BucketName=$(aws cloudformation describe-stacks --stack-name ${CFNStackName} --query "Stacks[0].Outputs[?OutputKey=='S3BucketName'].OutputValue" --output text)
export KendraIndexID=$(aws cloudformation describe-stacks --stack-name ${CFNStackName} --query "Stacks[0].Outputs[?OutputKey=='KendraIndexID'].OutputValue" --output text)

#You can also use these commands to read values
#export S3BucketName=$(aws s3api list-buckets | jq -r --arg bucketName "$CFNStackName" '.Buckets[] | select(.Name | contains($bucketName)) | .Name')
#export KendraIndexID=$(aws kendra list-indices | jq -r --arg indexName "$CFNStackName" '.IndexConfigurationSummaryItems[] | select(.Name | contains($indexName)) | .Id')

export SAMStackName="sam-$CFNStackName"
echo $SAMStackName

echo "Copy toml file and replace the parameters"

cp tools/samconf.toml samconfig.toml
# Replace values in .//samconfig.toml
sed -Ei "s|<KendraIndexId>|${KendraIndexID}|g" ./samconfig.toml
sed -Ei "s|<S3BucketName>|${S3BucketName}|g" ./samconfig.toml
sed -Ei "s|<SAMStackName>|${SAMStackName}|g" ./samconfig.toml
sed -Ei "s|<AWS_REGION>|${AWS_REGION}|g" ./samconfig.toml

echo "Deploy app with sam deploy"
sam deploy

echo "Export few more parameters from the sam stack output"
export BedrockApiUrl=$(aws cloudformation describe-stacks --stack-name ${SAMStackName} --query "Stacks[0].Outputs[?OutputKey=='BedrockApiUrl'].OutputValue" --output text)
export UserPoolId=$(aws cloudformation describe-stacks --stack-name ${SAMStackName} --query "Stacks[0].Outputs[?OutputKey=='CognitoUserPool'].OutputValue" --output text)
export UserPoolClientId=$(aws cloudformation describe-stacks --stack-name ${SAMStackName} --query "Stacks[0].Outputs[?OutputKey=='CongnitoUserPoolClientID'].OutputValue" --output text)
export SecretName=$(aws cloudformation describe-stacks --stack-name ${SAMStackName} --query "Stacks[0].Outputs[?OutputKey=='SecretsName'].OutputValue" --output text)
echo "API Gateway endpoint: $BedrockApiUrl"
echo "Cognito user pool id: $UserPoolId"
echo "Cognito client id: $UserPoolClientId"
echo "Secret name: $SecretName"

# Replace values in ./backend/samconfig.toml
sed -Ei "s|<ApiGatewayUrl>|${BedrockApiUrl}|g" ./frontend/src/main.js
sed -Ei "s|<CognitoUserPoolId>|${UserPoolId}|g" ./frontend/src/main.js
sed -Ei "s|<UserPoolClientId>|${UserPoolClientId}|g" ./frontend/src/main.js

#=================   3  ================== 
#Install Ampliyfy and build frontend
echo "Install Ampliyfy and build frontend"
cd ~/environment/bedrock-serverless-workshop/frontend
npm i -S @vue/cli-service
npm i -g @aws-amplify/cli
npm install
npm run build
cp ~/.aws/credentials ~/.aws/config

#Amplify initialization
echo "Amplify initialization"
mv dist build
amplify init --yes


echo "Add hosting, hit enter key if it prompts for action, use default"
amplify add hosting parameters.json

echo "Publish the amplify project"
amplify publish --yes

echo "Save the user_id and password to login to UI"
aws secretsmanager get-secret-value --secret-id $SecretName | jq -r .SecretString
```

1. Chuẩn bị môi trường cho instance **Cloud9**.
- Kiểm tra nếu biến môi trường  **CloudFormation** trong bước trước đã được gán giá trị chưa. Nếu không hay kiểm tra lại bưới trước.
- Cập nhật hệ điều hành. Cài đặt jq một công cụ để thao tác với dữ liệu dạng JSON trả về từ các dịch vụ AWS như **CloudFormation**.
- Chỉnh nguồn dự án thành nvm (Node version manager) để tải và cập nhật npm theo phiên bản chúng ta mong muốn. ( NVM được tích hợp sẵn trong môi trường Cloud9). Sau đó kiểm tra phiên bản npm.

2. Xây dựng và triển khai backend với **AWS SAM**
- Nhận thông tin **AWS region** thông qua dữ liệu metadata của instance hiện tại trong AWS (`169.254.169.254` là một [Link Local Address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-dynamic-data-retrieval.html) phục vụ việc trả về metadata liên quan đến instance dưới định dạng JSON)
- `sam build` sẽ tìm kiếm tệp tin `template.yaml` gọi là SAM template file để xây dựng phần backend của ứng dụng.
** AWS SAM** (Serverless Application Model) bao gồm 2 phần, AWS SAM CLI và AWS SAM templates. **AWS SAM CLI** được sử dụng để phát triển, sửa lỗi và triển khai các ứng dụng serverless viết bằng AWS SAM templates. **AWS SAM templates** cung cấp cấu trúc tinh gọn tối ưu trong xây dựng Infrastructure as Code (IaC) - cơ sở hạ tầng như code cho các ứng dụng serverless. Nó là một bản mở rộng của **AWS CloudFormation**, vậy nên nó sẽ có cùng cấu trúc như là **CloudFormation** và chúng ta có thể triển khai AWS SAM template trực tiếp đến AWS CloudFormation.

Trở về file `template.yaml` của chúng ta:

---
{{%expand "Giải thích các kiến trúc Backend trong template.yaml (Click)"%}}
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: 
  SAM template for serverless bedrock workshop.

Parameters:
  KendraIndexId:
    Description: ID of the Kendra Index 
    Type: String

  BedrockWSS3Bucket:
    Description: Workshop bucket name
    Type: String

  
  ApiGatewayStageName:
    Default: prod  
    Description : Stage name for the API Gateway
    Type: String 

# More info about Globals: https://github.com/awslabs/serverless-application-model/blob/master/docs/globals.rst
Globals:
  Function:
    Timeout: 60
    MemorySize: 5000
    Tracing: Active
  Api:
    TracingEnabled: true
    Cors:
        AllowMethods: "'GET,POST,OPTIONS'"
        AllowHeaders: "'Content-Type', 'Authorization', 'X-Forwarded-For', 'X-Api-Key', 'X-Amz-Date', 'X-Amz-Security-Token'"
        AllowOrigin: "'*'"

Resources:
# REST API
  BedrockLambdaApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: bedrock-workshop
      Description: Mock Integration REST API demo
      

  ApiGatewayResource:
      Type: AWS::ApiGateway::Resource
      Properties:
        ParentId: !GetAtt BedrockLambdaApi.RootResourceId
        PathPart: 'kendra-search-summarize-with-bedrock'
        RestApiId: !Ref BedrockLambdaApi

  # GET Method with Mock integration
  RootMethodOption:
    Type: AWS::ApiGateway::Method
    Properties:
      RestApiId: !Ref BedrockLambdaApi
      ResourceId: !GetAtt BedrockLambdaApi.RootResourceId
      HttpMethod: OPTIONS
      AuthorizationType: NONE
      Integration:
        Type: MOCK
        RequestTemplates:
          application/json: '{"statusCode": 200}'
        IntegrationResponses:
          -
            ResponseParameters:
              method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"
              method.response.header.Access-Control-Allow-Methods: "'POST,OPTIONS'"
              method.response.header.Access-Control-Allow-Origin: "'*'"
            StatusCode: '200'
        PassthroughBehavior: WHEN_NO_MATCH
      MethodResponses:
        -
          ResponseParameters:
              method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"
              method.response.header.Access-Control-Allow-Methods: "'POST,OPTIONS'"
              method.response.header.Access-Control-Allow-Origin: "'*'"
          StatusCode: '200'
          ResponseModels:
            application/json: Empty
      ResourceId: !Ref ApiGatewayResource
      RestApiId: !Ref BedrockLambdaApi

  ApiCognitoAuthorizer:          
    Type: AWS::ApiGateway::Authorizer
    Properties:
      IdentitySource: 'method.request.header.Authorization'
      Name: ApiCognitoAuthorizer
      ProviderARNs:
        - !GetAtt ChatbotUserPool.Arn
      RestApiId: !Ref BedrockLambdaApi
      Type: COGNITO_USER_POOLS

  ApiGatewayPostMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      AuthorizationType: COGNITO_USER_POOLS
      AuthorizerId: !Ref ApiCognitoAuthorizer
      HttpMethod: POST
      Integration:
        IntegrationHttpMethod: POST
        IntegrationResponses:
         -
          ResponseParameters:
              method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"
              method.response.header.Access-Control-Allow-Methods: "'POST,GET,OPTIONS'"
              method.response.header.Access-Control-Allow-Origin: "'*'"
          StatusCode: '200'
        Type: AWS_PROXY
        Uri: !Sub "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${BedrockLLMFunction.Arn}/invocations"
      MethodResponses:
        -
          ResponseParameters:
            method.response.header.Access-Control-Allow-Headers: true
            method.response.header.Access-Control-Allow-Methods: true
            method.response.header.Access-Control-Allow-Origin: true
          StatusCode: '200'
      ResourceId: !Ref ApiGatewayResource
      RestApiId: !Ref BedrockLambdaApi

  ChatbotUserPool:
      Type: AWS::Cognito::UserPool
      Properties:
        UsernameConfiguration: 
          CaseSensitive: false
        AutoVerifiedAttributes:
          - email
        Schema:
          - Name: email
            AttributeDataType: String
            Mutable: false
            Required: true
          - Name: name
            AttributeDataType: String
            Mutable: true
            Required: true
  ChatbotUserPoolClient:
    Type: AWS::Cognito::UserPoolClient
    Properties:
      UserPoolId: !Ref ChatbotUserPool
      ExplicitAuthFlows:
        - ALLOW_USER_PASSWORD_AUTH
        - ALLOW_REFRESH_TOKEN_AUTH
        - ALLOW_USER_SRP_AUTH
        - ALLOW_CUSTOM_AUTH
      AllowedOAuthFlowsUserPoolClient: true
      CallbackURLs:
        - http://localhost:3000
      AllowedOAuthFlows:
        - code
        - implicit
      AllowedOAuthScopes:
        - phone
        - email
        - openid
        - profile
      SupportedIdentityProviders:
        - COGNITO
  
  CognitoUserCreateFunction:
    Type: AWS::Serverless::Function # More info about Function Resource: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction
    Properties:
      CodeUri: bedrockFunc
      Handler: cognitouser.lambda_handler
      Runtime: python3.10
      MemorySize: 2048
      Architectures:
        - x86_64
      Environment:
        Variables:
          Cognito_UserPool: !Ref ChatbotUserPool
          Cognito_ClientID: !Ref ChatbotUserPoolClient
          SECRET_ID: 
            Fn::Sub: "ui-credentials-${BedrockLambdaApi}"
      Policies: 
       - Version: '2012-10-17'
         Statement: 
          - Effect: Allow
            Action:
              - secretsmanager:Get*
              - secretsmanager:Describe*
              - secretsmanager:CreateSecret
              - secretsmanager:DeleteSecret
              - secretsmanager:UpdateSecret
            Resource:
              - !Sub "arn:aws:secretsmanager:${AWS::Region}:${AWS::AccountId}:secret:*"
          - Effect: Allow
            Action:
              - cognito-idp:Describe*
              - cognito-idp:CreateUserPool
              - cognito-idp:CreateUserPoolClient
              - cognito-idp:DeleteUserPool
              - cognito-idp:UpdateUserPool
              - cognito-idp:Admin*
            Resource:
              - !Sub "arn:aws:cognito-idp:${AWS::Region}:${AWS::AccountId}:userpool/*"
              - !Sub "arn:aws:cognito-idp:${AWS::Region}:${AWS::AccountId}:userpool/*/*"

  DeploymentCustomResource:
    Type: Custom::AppConfiguration
    Properties:
      ServiceToken: !GetAtt CognitoUserCreateFunction.Arn

  ApiGatewayDeployment:
    Type: AWS::ApiGateway::Deployment
    DependsOn:   ApiGatewayPostMethod
    Properties:
      Description: Lambda API Deployment
      RestApiId: !Ref BedrockLambdaApi

  ApiGatewayStage:
    Type: AWS::ApiGateway::Stage
    Properties:
      DeploymentId: !Ref ApiGatewayDeployment
      Description: Lambda API Stage
      RestApiId: !Ref BedrockLambdaApi
      StageName: !Ref ApiGatewayStageName

  BedrockFunctionPermission:
        Type: AWS::Lambda::Permission
        DependsOn:
        - BedrockLambdaApi
        Properties:
          Action: lambda:InvokeFunction
          FunctionName: !Ref BedrockLLMFunction
          Principal: apigateway.amazonaws.com

  BedrockLLMFunction:
    Type: AWS::Serverless::Function # More info about Function Resource: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction
    Properties:
      CodeUri: bedrockFunc
      Handler: bedrockllm.lambda_handler
      Runtime: python3.10
      MemorySize: 2048
      Architectures:
        - x86_64
      Environment:
        Variables:
          KENDRA_INDEX_ID: !Ref KendraIndexId
          S3_BUCKET_NAME: !Ref BedrockWSS3Bucket 
      Policies: 
       - Version: '2012-10-17'
         Statement: 
          - Effect: Allow
            Action:
              - kendra:Query
              - kendra:GetQuerySuggestions
              - kendra:Retrieve
            Resource:
              - !Sub "arn:aws:kendra:${AWS::Region}:${AWS::AccountId}:index/${KendraIndexId}"
          - Effect: Allow
            Action:
              - s3:GetObject
              - s3:ListBucket
              - s3:ListBucketVersions
              - s3:DescribeBucket
            Resource:
              - !Sub "arn:aws:s3:::${BedrockWSS3Bucket}/*"
              - !Sub "arn:aws:s3:::${BedrockWSS3Bucket}"
          - Effect: Allow
            Action:
              - bedrock:ListFoundationModels
              - bedrock:GetFoundationModel
              - bedrock:InvokeModel
              - bedrock:InvokeModelWithResponseStream
              - bedrock:ListTagsForResource
              - bedrock:UntagResource
              - bedrock:TagResource
              - bedrock:AcceptAcknowledgement
              - bedrock:GetModelPermission
              - bedrock:GetModelInvocationLogging
              - bedrock:PutModelInvocationLogging
            Resource: "*"
          
Outputs:
    CognitoUserPool:
      Description: Cognito User Pool
      Value:
        Fn::Sub: "${ChatbotUserPool}"
    
    CongnitoUserPoolClientID:
      Description: Cognito User Pool Client ID
      Value:
        Fn::Sub: "${ChatbotUserPoolClient}"
        
    BedrockApiUrl:
      Description: API Gateway endpoint URL for the Bedrock Lambda Function
      Value:
        Fn::Sub: "https://${BedrockLambdaApi}.execute-api.${AWS::Region}.${AWS::URLSuffix}/prod"

    SecretsName:
      Description: Secrets name to retrieve ui credentials
      Value:
        Fn::Sub: "ui-credentials-${BedrockLambdaApi}"
```


Bản mẫu này sử dụng phiên bản định dạng mẫu `2010-09-09` và sử dụng `AWS::Serverless-2016-10-31` của AWS Transform để định nghĩa ứng dụng serverless.
**Tham số**:
- **KendraIndexId**: Cho phép chỉ định ID của chỉ mục **Kendra** được sử dụng để tìm kiếm (kiểu String)..
- **BedrockWSS3Bucket**: ACho phép định nghĩa tên của **S3 Bucket** được sử dụng bởi workshop (kiểu String)..
- **ApiGatewayStageName**: Đặt tên giai đoạn cho **API Gateway** (mặc định: "prod").
  
**Biến toàn cục (Globals):**:
- **Function**: Cấu hình mặc định cho các hàm **Lambda** như timeout (60 giây), kích thước bộ nhớ (5000 MB) và bật theo dõi..
- **Api**: Cấu hình cài đặt **API Gateway** như bật theo dõi và **CORS** (Chia sẻ Tài nguyên Giữa các Nguồn) với các cài đặt cho phép tất cả các nguồn, phương thức và tiêu đề.
  
**Resources**:
- Tạo **API Gateway REST API** với tên `bedrock-workshop` trong tên tài nguyên `BedrockLambdaApi` hoạt động như điểm vào cho ứng dụng.
- ạo một tài nguyên con dưới tài nguyên gốc của `BedrockLambdaApi`. Tài nguyên con có một phần path được đặt tên là `kendra-search-summarize-with-bedrock`. Phân đoạn path này có thể sẽ được sử dụng để ánh xạ các yêu cầu API đến từ một chức năng back-end cụ thể tương tác với **Amazon Kendra** và **Bedrock** (thông qua API).
- Định cấu hình cho phương thức HTTP `OPTIONS` với `BedrockLambdaApi` với quyền hạn none như mô phỏng tích hợp. Một phản hồi được xác định trước sẽ được trả về mà không cần gọi bất kỳ logic back-end nào (trong trường hợp này, phản hồi JSON trạng thái 200 và bất kỳ nguồn gốc nào `*` cho COR sẽ được trả về). Nếu không có tích hợp nào khác khớp với yêu cầu (ví dụ: yêu cầu GET hoặc POST thực tế), tích hợp MOCK này sẽ được sử dụng.

>**Phương thức `OPTIONS` **  là một yêu cầu preflight được sử dụng bởi một số ứng dụng khách (như trình duyệt web) trước khi thực hiện các yêu cầu thực tế cho một API. Nó cho phép khách hàng thu thập thông tin về các phương thức HTTP được hỗ trợ (GET, POST, v.v.) và cấu hình CORS (Chia sẻ Tài nguyên Giữa các Nguồn) của điểm cuối API.

> **Intergation vs Method Response**: **Intergation** xác định cách API Gateway tương tác với dịch vụ back-end của bạn để xử lý yêu cầu của khách hàng (xác định "cách" xử lý yêu cầu cho back-end) trong khi **Method Response** xác định cấu trúc và nội dung của phản hồi mà API Gateway gửi lại cho ứng dụng khách hàng sau khi xử lý một yêu cầu (xác định "cái gì" trả về cho khách hàng).

- **ApiCognitoAuthorizer**: Định nghĩa một bộ ủy quyền sử dụng **Cognito User Pool** để xác thực các yêu cầu API đến. Khách hàng cần bao gồm một mã ủy quyền hợp lệ trong tiêu đề **Authorization** để yêu cầu thành công (`IdentitySource: 'method.request.header.Authorization'`). ARN của Pool này sẽ được xác định sau.
- **ApiGatewayPostMethod**: Định nghĩa một phương thức POST yêu cầu ủy quyền **Cognito User Pool**. Khi nhận được yêu cầu POST, API Gateway xác thực mã ủy quyền bằng cách sử dụng ApiCognitoAuthorizer ở trên (`Properties: AuthorizationType: COGNITO_USER_POOLS + AuthorizerId: !Ref ApiCognitoAuthorizer`). Nếu hợp lệ, nó sẽ hoạt động như proxy và chuyển tiếp toàn bộ yêu cầu đến hàm Lambda BedrockLLMFunction (xác định sau) sau đó trả về phản hồi cho khách hàng. Xem thêm [tài liệu về điều này](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-apigateway-method.html).

- **ChatbotUserPool**: Hoạt động như một thư mục để lưu trữ thông tin người dùng như tên người dùng, email, mật khẩu và các thuộc tính tùy chỉnh khác. Trong workshop này, chúng ta sẽ lưu trữ các thuộc tính đó với lược đồ bao gồm email và tên người dùng. Email không thể thay đổi và được xác minh tự động trong quá trình đăng ký (AutoVerifiedAttributes bao gồm email)
- **ChatbotUserPoolClient**: Đại diện cho ứng dụng của bạn và xác định cách nó có thể tương tác với **ChatbotUserPool**. Trong trường hợp của chúng tôi, nó cho phép nhiều AuthFlows (Ví dụ: `ALLOW_USER_PASSWORD_AUTH` và `ALLOW_REFRESH_TOKEN`) và sử dụng **AWS Cognito** làm Nhà cung cấp Nhận dạng (SupportedIdentityProviders bao gồm COGNITO)
- **CognitoUserCreateFunction**: Định nghĩa hàm **Lambda** để xử lý tương tác với trình quản lý bí mật AWS Cognito (sự kiện sẽ kích hoạt khi tạo hoặc xóa sự kiện trên ngăn xếp CloudFormation) sau đó tạo người dùng để liên kết với stack **CloudFormation** đó. Sau đó, chúng tôi tạo sự kiện tùy chỉnh để điều này hoạt động và phản hồi lại ngăn xếp của chúng ta. Hiện tại, có thể sửa đổi thêm để đăng nhập với API Gateway trong hàm Lambda.
- **ApiGatewayDeployment**: Triển khai `BedrockLambdaApi` mà chúng tôi đã đề cập trước đó. Điều này không thể xảy ra trước bước `ApiGatewayPostMethod`.
- **ApiGatewayStage**: hay đổi giai đoạn của `BedrockLambdaApi` thành tên giai đoạn ApiGatewayStageName "**prod**" trong trường hợp này.
- **BedrockFunctionPermission**: Tạo IAM cho API Gateway để gọi hàm **Lambda** (lambda:InvokeFunction) theo [API Gateway Principal](apigateway.amazonaws.com). Chúng ta sẽ tạo hàm đó (`BedrockLLMFunction`) ở bước tiếp theo.
- **BedrockLLMFunction**: Đây là hàm chính của chúng ta để tương tác với **Amazon Bedrock API** vì tất cả các yêu cầu POST với dữ liệu chat sẽ được chuyển tiếp đến điểm cuối này. Hàm nằm trong thư mục `bedrockFunc` trong đường dẫn hiện tại (`CodeUri: bedrockFunc`), điểm gọi hàm của nó là `lambda_handler` (`Handler: bedrockllm.lambda_handler`), nó lấy `KENDRA_INDEX_ID` và `S3_BUCKET_NAME` làm biến môi trường. Hơn nữa, chính sách của nó cho phép:
  - Tương tác với chỉ mục **Kendra** (query, gợi ý, truy xuất) dựa trên biến môi trường `KendraIndexId`
  - Truy cập vào **S3 bucket** (liệt kê objects, thao tác objects, miêu tả bucket) dựa trên biến môi trường `BedrockWSS3Bucket`.
  - Thực hiện nhiều hành động trong cacs **Bedrock** Model (liệt kê, lấy, gọi, quyền, ghi nhật ký, gắn thẻ) có khả năng tương tác với dịch vụ Bedrock.



- Cuối cùng, chúng tôi sẽ phân tích **kết quả đầu ra** (output) thu được từ mẫu **SAM** này:
  - **CognitoUserPool**: đường dẫn **CognitoUserPool**
  - **CongnitoUserPoolClientID**: ID của Cognito User Pool Client
  - **BedrockApiUrl**: **API Gateway** endpoint URL cho Bedrock Lambda Function
  - **SecretsName**: Tên bí mật để truy xuất thông tin đăng nhập UI
{{% /expand%}}

---
{{%expand "Hàm chính Bedrock LLM để làm gì ? (Click)"%}}

```python
import coloredlogs
import logging
import os
import json
import boto3
import traceback

from langchain.embeddings import BedrockEmbeddings
from langchain.vectorstores import OpenSearchVectorSearch
from langchain.chains import RetrievalQA
from langchain.prompts import PromptTemplate
from langchain.retrievers import AmazonKendraRetriever
from langchain.llms.bedrock import Bedrock

coloredlogs.install(fmt='%(asctime)s %(levelname)s %(message)s', datefmt='%H:%M:%S', level='INFO')
logging.basicConfig(level=logging.INFO) 
logger = logging.getLogger(__name__)

S3_BUCKET_NAME = os.environ["S3_BUCKET_NAME"]
KENDRA_INDEX_ID = os.getenv("KENDRA_INDEX_ID", None)
REGION = os.getenv('AWS_REGION', 'us-west-2')


def get_bedrock_client():
    bedrock_client = boto3.client("bedrock-runtime", region_name=REGION)
    return bedrock_client

def create_bedrock_llm(bedrock_client, model_version_id, model_args):
    bedrock_llm = Bedrock(
        model_id=model_version_id, 
        client=bedrock_client,
        model_kwargs=model_args,
        verbose=True, 
        )
    return bedrock_llm

def create_langchain_vector_embedding_using_bedrock(bedrock_client, bedrock_embedding_model_id):
    bedrock_embeddings_client = BedrockEmbeddings(
        client=bedrock_client,
        model_id=bedrock_embedding_model_id)
    return bedrock_embeddings_client
    

def lambda_handler(event, context):
    logging.info(f"Event is: {event}")
    # Parse event body
    event_body = json.loads(event["body"])
    question = event_body["query"]
    logging.info(f"Query is: {question}")
    
    
    try:

        #bedrock_embedding_model_id = args.bedrock_embedding_model_id

        bedrock_model_id = event_body["model_id"]
        temperature = event_body["temperature"]
        maxTokens = event_body["max_tokens"]
        logging.info(f"selected model id: {bedrock_model_id}")
        
        model_args = {}
        
        PROMPT_TEMPLATE = 'prompt-engineering/claude-prompt-template.txt'
        if bedrock_model_id == 'anthropic.claude-v2':
            model_args = {
                "max_tokens_to_sample": int(maxTokens),
                "temperature": float(temperature),
                "top_k": 250,
                "top_p": 0.1
            }
            PROMPT_TEMPLATE = 'prompt-engineering/claude-prompt-template.txt'
        elif bedrock_model_id == 'amazon.titan-text-express-v1':
            model_args = {
                "maxTokenCount": int(maxTokens),
                "temperature": float(temperature),
                "topP":1
            }
            PROMPT_TEMPLATE = 'prompt-engineering/titan-prompt-template.txt'
        elif bedrock_model_id == 'ai21.j2-mid-v1' or bedrock_model_id == 'ai21.j2-ultra-v1':
            model_args = {
                "maxTokens": int(maxTokens),
                "temperature": float(temperature),
                "topP":1
            }
            PROMPT_TEMPLATE = 'prompt-engineering/jurassic2-prompt-template.txt'
        else:
            model_args = {
                "max_tokens_to_sample": int(maxTokens),
                "temperature": float(temperature)
            }
            PROMPT_TEMPLATE = os.environ["PROMPT_TEMPLATE_CLAUDE"]

        # Read the prompt template from S3 bucket
        s3 = boto3.resource('s3')
        obj = s3.Object(S3_BUCKET_NAME, PROMPT_TEMPLATE) 
        prompt_template = obj.get()['Body'].read().decode('utf-8')
        logging.info(f"prompt: {prompt_template}")

        PROMPT = PromptTemplate(
            template=prompt_template, input_variables=["context", "question"]
        )

        #Create bedrock llm object
        bedrock_client = get_bedrock_client()
        bedrock_llm = create_bedrock_llm(bedrock_client, bedrock_model_id, model_args)
        #bedrock_embeddings_client = create_langchain_vector_embedding_using_bedrock(bedrock_client, bedrock_embedding_model_id)
        
        logging.info(f"Starting the chain with KNN similarity using Amazon Kendra, Bedrock FM {bedrock_model_id}")
        qa = RetrievalQA.from_chain_type(llm=bedrock_llm, 
                                    chain_type="stuff", 
                                    retriever=AmazonKendraRetriever(index_id=KENDRA_INDEX_ID),
                                    return_source_documents=True,
                                    chain_type_kwargs={"prompt": PROMPT, "verbose": True},
                                    verbose=True)
        
        response = qa(question, return_only_outputs=False)
        
        source_documents = response.get('source_documents')
        source_docs = []
        for d in source_documents:
            source_docs.append(d.metadata['source'])
        
        output = {"answer": response.get('result'), "source_documents": source_docs}

        return {
            "statusCode": 200,
            "headers": {
                "Content-Type": "application/json",
                "Access-Control-Allow-Origin": "*",
                "Access-Control-Allow-Headers": "*",
            },
            "body": json.dumps(output)
        }

        logging.info('new bedrock version tested successfully')
    except Exception as e:
        print('Error: ' + str(e))
        traceback.print_exc()

        # Handle exceptions and provide an error response
        error_message = str(e)
        error_response = {
            "statusCode": 500,
            "headers": {
                "Content-Type": "application/json",
                "Access-Control-Allow-Origin": "*",
                "Access-Control-Allow-Headers": "*",
            },
            "body": json.dumps({"error": error_message})
        }
        return error_
```
Mã python này lấy địa chỉ của **S3 bucket**  và chỉ mục  của **Amazon Kendra** từ  **CloudFormation** stack như là các biến môi trường. Dựa vào dữ liệu được chọn từ hàm gọi lambda, nó chọn các biến khác nhau để gọi các mô hình ngôn ngữ lớn khác nhau (Để đơn giản workshop này chỉ sử dụng những câu lệnh if đơn giản). Để sinh ra câu trả lời, nó sẽ nhận ngữ cảnh hướng dẫn trong prompt (prompt template - có thể tìm trong thư mục `prompt-engineering` ở đường dẫn dự án trong Cloud9). Dưới đây là một ví dụ đơn giản về prompt template, các biến bị thiếu sẽ được thay thế vào sau:
    thư
 ```text
Human: We are conducting a Generative AI workshop, please carefully construct your response for the question from the given context only - {context}.

It's very important that your answer is within the context, if you cannot derive satisfying response, please respond with "Sorry, I don't know the answer"

Here is the question you should process: {question}

Assistant:
 ```
Tiếp theo,chúng ta tạo client bedrock và lấy `bedrock_llm` dựa trên LLM định dạng mẫu trên và tạo LangChain object dựa trên nó (`RetrievalQA`) - cái mà có khả năng kết hợp LLM với bộ trả về của Amazon Kendra (để tìm kiếm các tài liệu liên quan) và xác định định dạng prompt. Object đó sẽ được gọi và phân tách để lấy câu trả lời và các tài liệu liên quan. Kết quả trả về sẽ được đưa tới front-end với 200 HTTP request trong định dạng JSON.

{{% /expand%}}

---

   ![2_12](/images/2/2_12.png "Create SAM stack")

{{% notice note %}}
Chạy `sam build` sẽ tạo một thư mục có tên `.aws-sam/build` trong thư mục dự án của bạn.
{{% /notice %}}
   ![2_13](/images/2/2_13.png "Create SAM stack")
   ![2_14](/images/2/2_14.png "SAM stack Result")


- Chúng ta trích xuất tên **S3 Bucket** và **ID chỉ mục Kendra** từ stack `CloudFormation` chúng ta đã tạo.
- Đặt tên cho stack **SAM** mà chúng tôi sẽ triển khai
- Sao chép tệp cấu hình `samconfig.toml` tùy chọn từ thư mục `tools` và thay thế các tham số.
- Triển khai stack **SAM**
- Trích xuất đầu ra của **stack SAM** thành biến môi trường và hiển thị chúng để sử dụng sau.
1. Xây dựng và Triển khai Frontend với **AWS Amplify**
- Điều hướng đến thư mục front-end
- Cài đặt tất cả các phụ thuộc Nodejs và Vuejs
- Sao chép thông tin xác nhận AWS vào thư mục làm việc hiện tại.  (`cp ~/.aws/credentials ~/.aws/config`)
- Đổi tên mã front-end đã build thành `build`. (AWS Amplify CLI mong đợi mã được build sẽ nằm trong thư mục có tên `build`).
- Khởi tạo AWS Amplify trong thư mục hiện tại (`--yes`  tự động chọn tùy chọn mặc định cho tất cả lời nhắc).
- Thêm tùy chọn lưu trữ Amplify
{{% notice note %}}
Trong quá trình **amplify add host**, bạn được nhắc chọn hai lần, hãy giữ nguyên các lựa chọn mặc định và nhấn **enter**. Các tùy chọn mặc định là, **Lưu trữ với Amplify Console (Lưu trữ được quản lý với tên miền tùy chỉnh, Triển khai liên tục)** cho tùy chọn đầu tiên và **Triển khai thủ công cho tùy chọn thứ hai**.
{{% /notice %}}
   ![2_15](/images/2/2_15.png "Amplify notice")

- Sau khi hoàn thành, bạn sẽ thấy hình ảnh sau:
   ![2_16](/images/2/2_16.png "Amplify access")
  

Sao chép và lưu trữ giá trị cho **amplifyapp ur**l, **user_id** và **password** trong trình soạn thảo văn bản. Bạn sử dụng các thông tin xác nhận này để đăng nhập vào giao diện người dùng.

Khởi chạy amplifyapp url từ trình duyệt web, đăng nhập bằng thông tin xác nhận ở trên. Sau khi đăng nhập thành công, bạn sẽ thấy màn hình chính bên dưới. Lưu ý, nó vẫn chưa sẵn sàng với các tài liệu nguồn và chức năng chat vẫn chưa hoạt động. Ở phần tiếp theo, bạn sẽ hoàn thành việc lập chỉ mục các tài liệu nguồn của mình và kiểm tra với các câu hỏi mẫu.

   ![2_17](/images/2/2_17.png "Login UI")
   ![2_18](/images/2/2_18.png "Chatbot UI")
