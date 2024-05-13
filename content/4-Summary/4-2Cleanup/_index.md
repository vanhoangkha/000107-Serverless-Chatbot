---
title : "Clean up resources"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

#### Remove the AWS SAM application and startup CloudFormation stack
Navigate to the AWS **Cloud9** terminal, and then run the following commands:

1. To delete **SAM** stack, run the following command.
```bash
cd ~/environment/bedrock-serverless-workshop
sam delete 
```
2. To delete startup stack, run the following command.

```bash
aws cloudformation delete-stack --stack-name $CFNStackName
```
   ![4_1](/images/4/4_1.png "Clean up Resources")
Remember to check **Kendra** index too.
   ![4_2](/images/4/4_2.png "Clean up Resources")
