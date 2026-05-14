---
title : "Access Cloud9 and set up enviroment"
date: 2024-01-01
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

#### Access Cloud9

To set up your environment, you open the **AWS Cloud9** environment, as detailed in the following instructions.

1. From the **AWS Management Console**, navigate to **AWS Cloud9**.
   ![2_7](/images/2/2_7.png?featherlight=false "Clou9Search")

2. In the **Environments** section, for `bedrock-workshop-environment`, under **Cloud9** IDE, choose Open.
   ![2_8](/images/2/2_8.png?featherlight=false "Open Cloud 9")

3.Confirm that the **AWS Cloud9** environment loaded properly.

  - You can close the Welcome tab.

  - You can drag tabs around to any position that you like. For example:
   ![2_10](/images/2/2_10.png?featherlight=false "Cloud 9 Main Interface")


#### Set up Enviroment

After obtaining the stack name, substitute <your-startup-stack-name> with the copied name, run the following command. Additionally, make sure to configure the environment variable **S3BucketName**, this bucket is utilized for storing sample documents and testing the workshop.

```bash
export CFNStackName=<you-startup-stack-name>
export S3BucketName=$(aws cloudformation describe-stacks --stack-name ${CFNStackName} --query "Stacks[0].Outputs[?OutputKey=='S3BucketName'].OutputValue" --output text)
echo "S3 bucket name: $S3BucketName"
```
For example:
   ![2_11](/images/2/2_11.png?featherlight=false "Enviroment variable")
