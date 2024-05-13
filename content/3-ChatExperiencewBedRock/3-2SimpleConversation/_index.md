---
title : "Simple Conversation with Chatbot UI"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---
 #### Prompt parameters
 Prompt parameters refer to the instructions, guidelines, or specific inputs provided to an FM to influence the generated output. These parameters are essential for fine-tuning the model's responses and ensuring that the generated text aligns with the desired context or goals.

**Prompt**: a textual input given to the model to initiate a response. A prompt can be in the form of a question, statement, or incomplete sentence. The quality and specificity of the prompt can greatly affect the output. In the next task, you will experience various prompt engineering methods.

**Model temperature**: a parameter that controls the randomness of the generated text. Higher values (such as 0.9 and 1) make the output more diverse and creative. Lower values (such as 0 and 0.1) make the output more focused and deterministic.

**Max tokens**: a parameter that limits the length of the generated response. You can set a maximum number of tokens to control the length of the output.
 
 #### Simple Conversation with Chatbot UI

 Open browser and open the previous **Amplify** UI link. If you not singned in, use the credentials that you retreived ealier to sign in. The UI is displayed as follows:


 When you enter a question, you might expect an answer that is accompanied by relevant document references. However, if no supporting document or data source is available, you might receive a response that states: "Sorry, I don't have the answer."

Try several of the following sample questions, and review the generated responses by **Amazon Bedroc**k FMs.

```text
What is the role of FOMC?
```
   ![3_1](/images/3/3_1.png "Sample question")

```text
What is the federal funds rate as of September 2023?
```
   ![3_2](/images/3/3_2.png "Sample question")

```text
What are demographic trends in the dental space?
```
   ![3_3](/images/3/3_3.png "Sample question")

```text
What are the solar energy trends in 2023?
```
   ![3_4](/images/3/3_4.png "Sample question")

You can experiment with various prompt parameters (such as model temperature and max tokens) to compare and fine-tune your responses.
