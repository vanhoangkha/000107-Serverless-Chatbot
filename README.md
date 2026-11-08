# Create a Serverless Chatbot with Amazon Bedrock, Amazon Kendra and Your Own Data

> Building a serverless chatbot.

A hands-on workshop from the [AWS First Cloud Journey](https://github.com/vanhoangkha/aws-first-cloud-journey) series, available in English and Vietnamese.

**Read the workshop online:** https://vanhoangkha.github.io/000107-Serverless-Chatbot/

## Overview

In this workshop, you will learn how to build a generative AI-powered chatbot by using Amazon Bedrock and Amazon Kendra. You also learn how to build this chatbot by using Amazon Lambda serverless services.

## Workshop contents

1. Introduction
   - The core concept
   - Amazon Bedrock, Amazon Kendra and Amazon Cloud9
   - AWS Lambda and AWS Amplify
   - How the architecture work?
2. Enviroment Setup
   - Set up CloudFormation template
   - Access Cloud9 and set up enviroment
   - Deploy Amazon Bedrock Serverless Application
   - Index the Sample Data by Using Amazon Kendra
3. Chatbot Experience with Amazon Bedrock
   - Enable Foundation Models in Amazon Bedrock
   - Simple Conversation with Chatbot UI
   - Prompt Engineering in Chatbot
4. Summary and Cleanup
   - Summary
   - Clean up resources

## Prerequisites

- An AWS account with permissions to create the resources used in the labs
- Basic familiarity with the AWS Management Console
- [Hugo](https://gohugo.io/installation/) (extended edition) if you want to run the site locally

## Run locally

```bash
git clone --recurse-submodules https://github.com/vanhoangkha/000107-Serverless-Chatbot.git
cd 000107-Serverless-Chatbot
hugo server
```

Then open http://localhost:1313 in your browser.

## Repository structure

| Path | Purpose |
| --- | --- |
| `content/` | Workshop pages (`*.md` English, `*.vi.md` Vietnamese) |
| `static/` | Images and other static assets |
| `layouts/` | Hugo layout overrides |
| `themes/` | Hugo theme |
| `config.toml` | Site configuration |

## Cost and cleanup

The labs create AWS resources that may incur charges. Complete the **Clean up** chapter when you finish to remove them.

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md).

To report a security issue, see [SECURITY.md](SECURITY.md).

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE).
