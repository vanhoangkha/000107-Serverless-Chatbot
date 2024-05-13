---
title : "Deploy Amazon Bedrock Serverless Application"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

In this task, you perform steps to build and deploy the backend services. The deployment creates a serverless application where the UI will call REST-based API calls to invoke **Amazon Bedrock** APIs. After the code is deployed, it launches **Amazon API Gateway** and an **AWS Lambda** function.

#### Build and deploy the application

Open **AWS Cloud9** enviroment, then clone the source code by following command:

```bash
cd ~/environment
git clone https://github.com/aws-samples/bedrock-serverless-workshop.git
```

To build and deploy backend and frontend of serverless app, run the following command.

```bash
cd ~/environment/bedrock-serverless-workshop
chmod +x startup.sh
./startup.sh
```

We will run `startup.sh` bash script in that repo. Let disscuss it:

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

1. Prepare enviroment for **Cloud9** instance.
- Check if **CloudFormation** enviroment variable in previous step has been set. If not then stop.
- Update OS. Install jq a tools to parse JSON data from AWS Services like **CloudFormation**.
- Set source to nvm (Node version manager) to install and update nodejs to desirable version. ( NVM come packed with Cloud9 enviroment). Then check it version.

2. Building and Deploying Backend with **AWS SAM**
- Retrieve current **AWS region** through AWS current instance metadata (`169.254.169.254` is a [Link Local Address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-dynamic-data-retrieval.html) for the Instance Metadata Serivice  in JSON format)
- `sam build` command will looking for `template.yaml`which is SAM template file to build the serverless backend.
** AWS SAM** (Serverless Application Model) consists of two parts, AWS SAM CLI and AWS SAM templates. **AWS SAM CLI** is use for develop, debug and deploy serverless applications on AWS SAM templates. **AWS SAM templates** provide a short-hand syntax, optimized for defining Infrastructure as Code (IaC) for serverless applications. It is an extension of **AWS CloudFormation**, so it come with the same syntax as **CloudFormation** and we can deploy AWS SAM template directly to AWS CloudFormation.

Back to our `template.yaml`:

---
{{%expand "Explain backend architecture with template.yaml (Click)"%}}
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


This template is using `2010-09-09` template format version and use AWS Transform `AWS::Serverless-2016-10-31` for serverless application definition.
**Parameter**:
- **KendraIndexId**: Allows specifying the ID of the **Kendra** index used for search (String type).
- **BedrockWSS3Bucket**: Allows defining the name of the **S3 bucket** used by the workshop (String type).
- **ApiGatewayStageName**: Sets the stage name for the **API Gateway** (default: "prod").
  
**Globals**:
- **Function**: Configures defaults for **Lambda** functions like timeout (60 seconds), memory size (5000 MB), and enabling tracing.
- **Api**: Configures API Gateway settings like enabling tracing and **CORS** (Cross-Origin Resource Sharing) with permissive settings allowing all origins, methods, and header.
  
**Resources**:
- Create **API Gateway REST API** with the name  `bedrock-workshop` in resource name `BedrockLambdaApi` sact as entry point for application.
- Creates a child resource under the root resource of the `BedrockLambdaApi`. The child resource has a path part named `kendra-search-summarize-with-bedrock`. This path segment will likely be used to map incoming API requests to a specific backend functionality that interacts with Amazon Kendra and Bedrock (via API). 
- Define configuration for OPTIONS Http method to `BedrockLambdaApi` with none authorization as mock intergation. A pre-defined response will be returned without invoking any backend logic (in this case  a 200 status JSON response and any origin `*` for COR will be returned). If no other integration matches the request (e.g., a real GET or POST request), this MOCK integration will be used.
  

>**The `OPTIONS` method** is a preflight request used by some client applications (like web browsers) before making actual requests to an API.
It allows the client to gather information about the supported HTTP methods (GET, POST, etc.) and CORS (Cross-Origin Resource Sharing) configuration of the API endpoint.

> **Intergation vs Method Response**: An integration defines how API Gateway interacts with your backend service to handle a client request (define "how" to handle the request to backend) while a method response defines the structure and content of the response that API Gateway sends back to the client application after processing a request (define "what" to return to client).

- **ApiCognitoAuthorizer**: defines an authorizer that uses a **Cognito User Pool** to validate incoming API requests. Clients will need to include a valid authorization token in the `**Authorization header** for successful requests (`IdentitySource: 'method.request.header.Authorization'`)`. The ARN of this Pool will be define later.
- **ApiGatewayPostMethod**: defines a POST method that requires Cognito User Pool authorization. When a POST request is received, API Gateway validates the authorization token using the ApiCognitoAuthorizer above (   `Properties: AuthorizationType: COGNITO_USER_POOLS + AuthorizerId: !Ref ApiCognitoAuthorizer`). If valid, it will act as proxy and forwards the entire request to the BedrockLLMFunction Lambda function (define later) then return the response to client. More [document on this](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-apigateway-method.html).

- **ChatbotUserPool**: acts as a directory for storing user information like usernames, emails, passwords, and other custom attribute. In this workshop, we will store those attribute with a schema that include email and name of user. Email is unmutable and is verify automatically during sign up process (AutoVerifiedAttributes include email)
- **ChatbotUserPoolClient**: represents your application and defines how it can interact with the `ChatbotUserPool`. In our case, it allow many AuthFlows (Ex: `ALLOW_USER_PASSWORD_AUTH` and `ALLOW_REFRESH_TOKEN`) and make use of AWS Cognito as Identity Providers (SupportedIdentityProviders include COGNITO)
- **CognitoUserCreateFunction**: Define **Lambda** function to handle interaction with AWS Cognito secret manager (event will trigger when create or delete event on CloudFormation stack) then create the user to associate with that **CloudFormation** Stack. Then we create custom event for this to work and response back to our stack. This can be modify futher for login with API gateway in Lambda function. As for now, understand it as if.
- **ApiGatewayDeployment**: Deploy `BedrockLambdaApi` that we metion before. This cannot happen before `ApiGatewayPostMethod` step. 
- **ApiGatewayStage**: Change the stage of `BedrockLambdaApi` to `ApiGatewayStageName` stage name "**prod**" in this case.
- **BedrockFunctionPermission**: Create IAM for the **API Gateway** to invoke **Lambda** function (`lambda:InvokeFunction`) according to [AWS API Gateway principal](apigateway.amazonaws.com) . We will create that function(`BedrockLLMFunction`) in next step.
- **BedrockLLMFunction**: This is our **main function** to interact with Amazon Bedrock API as all POST request with chat data will forward to this endpoint. The function locate in `bedrockFunc` directory in current path (` CodeUri: bedrockFunc`), it entry point is `lambda_handler` (`Handler: bedrockllm.lambda_handler`), it take `KENDRA_INDEX_ID` and `S3_BUCKET_NAME` as enviroment variable. Moreover, it policy allows:
  - Interact with a **Kendra** index (query, suggestions, retrieval) based on the `KendraIndexId` environment variable.
  - Access an **S3 bucket** (list objects, get objects, describe bucket) based on the BedrockWSS3Bucket environment variable.
  - Perform various **Bedrock** Model actions (list, get, invoke, permissions, logging, tagging) potentially interacting with a Bedrock service.



- Finally, we will breakdown the **output** we retrieved from this SAM template:
  - **CognitoUserPool**: **CognitoUserPool** location
  - **CongnitoUserPoolClientID**: Cognito User Pool Client ID
  - **BedrockApiUrl**: **API Gateway** endpoint URL for the Bedrock Lambda Function
  - **SecretsName**: Secrets name to retrieve ui credentials
{{% /expand%}}

---
{{%expand "What do BedrockLLM Function do ? (Click)"%}}

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
This Python script take **S3 bucket** location and **Amazon Kendra** index from our **CloudFormation** stack as enviroment variable. Depend on user input on main Lambda handler received event, it set the apporiate variable template for different LLM models (for simplicity this workshop will use simple login if statement). To generate an answer, it will retreive the instruction context of the prompt (prompt template - can be found at `prompt-engineering` directory at the project root path). Here is a example of a prompt template, the variable will be subsitute in:
    
 ```text
Human: We are conducting a Generative AI workshop, please carefully construct your response for the question from the given context only - {context}.

It's very important that your answer is within the context, if you cannot derive satisfying response, please respond with "Sorry, I don't know the answer"

Here is the question you should process: {question}

Assistant:
 ```
Next, we create a bedrock client and get the `bedrock_llm` base on LLM template above then feed it to create a LangChain object (`RetrievalQA`) which combines the LLM with an Amazon Kendra retriever (for finding relevant documents) and the defined prompt template. We convert the question to vector using Langchain to retrieve client object. That object is called and extracted to get the answer and source document relevant. The result can be returned to front-end with 200 HTTP request in JSON format.

{{% /expand%}}

---

   ![2_12](/images/2/2_12.png "Create SAM stack")

{{% notice note %}}
Running sam build will create a folder named .aws-sam/build in your project directory.
{{% /notice %}}
   ![2_13](/images/2/2_13.png "Create SAM stack")
   ![2_14](/images/2/2_14.png "SAM stack Result")


- We export the **S3 bucket name** and `Kendra Index Id` from our **CloudFormation** Stack that we created.
- Give a name for the **SAM** stack that we are going to deploy.
- Copy optional `samconfig.toml` configuration file from `tools` directory and subsitute the parameters.
- Deploy the **SAM** stack
- Extract the ouput of **SAM** stack to enviroment variable and display them for later use.
3. Building and Deploying Frontend with AWS Amplify
- Navigate to front end directory.
- Installing all Nodejs and Vuejs dependencies
- Copy AWS credential to current working directory. (`cp ~/.aws/credentials ~/.aws/config`)
- Rename the built frontend code to `build`. (The Amplify CLI expects the built code to be in a directory named `build`).
- Init AWS Amplify in current directory (--yes automatically select the default option for all prompt).
- Add Amplify hosting option
{{% notice note %}}
During **amplify add host**, you are prompted with a selection twice, keep the default selections and **hit enter**. The defaults are, **Hosting with Amplify Console (Managed hosting with custom domains, Continuous deployment)** for the first option, and **Manual deployment** for the second option.
{{% /notice %}}
   ![2_15](/images/2/2_15.png "Amplify notice")

- After completion, you will see the following image:
   ![2_16](/images/2/2_16.png "Amplify access")
  

Copy and store the value for **amplifyapp url**, **user_id**, and **password** in a text editor. You use these credentials to sign in to the UI.

Launch the amplifyapp url from the web browser, login using above credentials. After a successful login you see the the below home screen. Note, it is not yet ready with source documents, and the chat is not yet functional. In the next task you complete indexing your source documents and test with sample questions.

   ![2_17](/images/2/2_17.png "Login UI")
   ![2_18](/images/2/2_18.png "Chatbot UI")
