---
title : "Prompt Engineering in Chatbot"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---

#### Prompt engineering overview

Prompt engineering is an emerging discipline focused on developing optimized prompts to efficiently apply language models to various tasks. Prompt engineering helps researchers understand the abilities and limits of large language models (LLMs). By using various prompt engineering techniques, you can often get better answers from the foundation models without spending effort and cost on retraining or fine-tuning them.

Prompt Engineering leverages the principle of “priming”: providing the model with a context of few (3-5) examples of what the user expects the output to look like, so that the model mimics the previously “primed” behavior. By interacting with the LLM through a series of questions, statements, or instructions, users can effectively guide the LLM's understanding and adjust its behavior according to the specific context of the conversation.

The quality of model outputs depends on the quality of the prompt that we provide. Some key aspects of prompt engineering include:

- **Word choice**: Select words and phrases in the prompt that will invoke the desired tone, style, and content from the AI. Using more positive, constructive language tends to yield better results.

- **Providing context**: Give the AI relevant background information and examples to help guide it towards the kind of response that you want.

- **Structuring logically**: Organize the prompt in a clear, logical way to help the AI understand what you are asking for. Breaking it down into simpler steps or bullet points can help.

- **Limiting scope**: Keep prompts focused on narrow, well-defined requests rather than broad, vague ones. This reduces confusion and improves AI responses.

- **Managing length**: Find the right balance of conciseness and sufficient detail. Overly long prompts can be hard to follow, but prompts that are too short might lack the needed context.

- **Rewording**: Try different phrasings if the initial prompt does not produce the desired outcome. Small tweaks can make a big difference in adjusting the AI's response.

- **Providing feedback**: Let the AI know when its responses are on or off track. This guides the AI towards better answers.

In this workshop, the prompts are retrieved from `s3://{S3BucketName}/prompt-engineering/`. The prompt templates are tailored for the Claude, Titan, and Jurassic models. In the upcoming part, you have the opportunity to experiment with two different prompt templates, one that is inadequately composed and another that is well constructed by modifing the prompt context.
#### Prompt engineering with Claude Model

Let's proceed by updating the prompt template for the Claude model and observe how the responses differ. First, start with a poorly constructed prompt. Use the following steps to update the prompt template:

1. Open the AWS Cloud9 environment, and open the `/bedrock-serverless-workshop/prompt-engineering/claude-prompt-template.txt` file.

2. Copy the following poorly constructed prompt, and then update the `/prompt-engineering/claude-prompt-template.txt` file.
```text
Human: I got a question here: {question}.
{context}.

Assistant:
```
3. Save the file and in the AWS Cloud9 terminal, run the following command to copy the test prompt file to the S3 bucket folder.

```bash
cd ~/environment/bedrock-serverless-workshop/
aws s3 cp prompt-engineering/claude-prompt-template.txt s3://${S3BucketName}/prompt-engineering/
```
   ![3_5](/images/3/3_5.png "Upload prompt template to S3")

**Note**: If the upload fails because of {S3BucketName}, run the export command you used in an earlier step to set the environment variable.

1. Return to the Amplify app browser tab  (switch to Cladue Model) and ask the following questions.

Question 1:
```text
What is the federal funds rate as of September 2024?

```
This question isn't intended to have an answer because it pertains to the future. However, due to the poorly formulated prompt, the question generates a response that erroneously assumes that the rate will remain the same from 2023.
   ![3_6](/images/3/3_6.png "Sample question")


Question 2:
```text
What is the federal funds rate as of September 2023?

```
   ![3_7](/images/3/3_7.png "Sample question")

This time, you receive the correct answer, but note that it might not be as refined or polished as we would ideally expect.

Now, try it with an effective prompt. Return to the AWS Cloud9 window and replace the prompt in the claude-prompt-template.txt file with the following well-constructed prompt.

```text
Human: We are conducting a generative AI workshop. Please carefully construct your response for the question from the given context only - {context}.

It's very important that your answer is within the context. If you cannot derive a satisfying response, please respond "Sorry, I don't know the answer."

Here is the question you should process: {question}

Assistant:
```
1. Save the file and return to the AWS Cloud9 terminal. To upload the updated file to the S3 bucket folder, run the S3 copy command.

2. Return to the Amplify app browser tab and test Question 1 and 2.

With an effective prompt, your responses are notably more refined and contextually appropriate.
   ![3_8](/images/3/3_8.png "Sample question")
   ![3_9](/images/3/3_9.png "Sample question")

You can continue to refine your prompt template and test your questions. Effective prompt engineering plays a crucial role in guiding model behavior and attaining the desired responses.

{{% notice note %}}
When you work with Jurassic 2 Model, use `bedrock-serverless-workshop/prompt-engineering/jurassic2-prompt-template.txt` and switch the option to choose it.
{{% /notice %}}