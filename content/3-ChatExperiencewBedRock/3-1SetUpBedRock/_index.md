---
title : "Enable Foundation Models in Amazon Bedrock"
date: 2024-01-01
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
Use **Amazon Bedrock** to build and scale generative AI applications with FMs. **Amazon Bedrock** is a fully managed service that makes FMs, from leading AI startups and Amazon, available through an API. You can choose from a wide range of FMs to find the model that is best suited for your use case. With the **Amazon Bedrock** serverless experience, you can get started quickly, privately customize FMs with your own data, and quickly integrate and deploy them into your applications by using AWS tools without having to manage any infrastructure.


Sign in to the **AWS Management Console**, and then type `Bedrock` in the console search box. Review to ensure that your AWS account has access to **Amazon Bedrock**, and then select the AWS Region where this workshop is running; for example, US East (N. Virginia).
   ![2_30](/images/2/2_30.png "Request Bedrock model access")

#### Activate model access

Your AWS account comes with no model access by default, and you need to access **Claude** and **Jurassic** models to complete this workshop.

1. At the upper-left of the **Amazon Bedrock** console, choose the menu icon. In the navigation pane, choose **Model access**, and then select **Manage Model Access**.
   ![2_31](/images/2/2_31.png "Request Bedrock model access")

Select **Anthropic Claude, AI21 Labs Jurassic-2 Ultra models**.

{{% notice note %}}
You may need to verify use case detail with Amazon Claude model.
{{% /notice %}}
   ![2_32](/images/2/2_32.png "Request Bedrock model access")

Choose **Request model access**.
   ![2_33](/images/2/2_33.png "Request Bedrock model access")

#### Chat with model

At the upper-left of the **Amazon Bedrock** console, choose the menu icon. Under **Playgrounds**, choose **Chat**.

You can also take this time, on the left menu, to navigate around the different options.
   ![2_34](/images/2/2_34.png "Select Bedrock model access")

On the Selection provider dropdown menu, select the **Anthropic** and **Claude 2 v2** model.
   ![2_35](/images/2/2_35.png "Select Bedrock model access")


Next to **Human**, type a question in the text box, and then choose **Run**. For example: **"What is the distance to the moon from Earth?"**
   ![2_36](/images/2/2_36.png "Select Bedrock model access")

The model returns a response similar to this:
   ![2_37](/images/2/2_37.png "Select Bedrock model access")
