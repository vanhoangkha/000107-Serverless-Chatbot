---
title : "The core concept"
date: 2024-01-01
weight : 1 
chapter : false
pre : " <b> 1.1 </b> "
---

#### How do generative AI work?

**Generative AI models** (large language models, in particular) try to **predict the next word** (or token, which is a chunk of text) given the current sequence of words. The models do this by generating a probability score for each word in their vocabulary and picking the word with the highest probability score for the next generation. This process is repeated until an entire sentence or blurb is generated.

![How do generative AI work?](/images/1/Img1_1.png?featherlight=false "How do generative AI work?")

#### How is generative AI different from traditional AI?

Traditionally, AI models performed natural language tasks (such as text generation, sentiment detection, question answering, and text classification) and image-based tasks (such as object detection and semantic segmentation). **These traditional models needed to be trained with labeled datasets for a specific task each time**. **Generative AI models, on the other hand, are trained with a large amount of unlabeled data one time, and they can be reused for different tasks.** Generative models, therefore, have much more linguistic knowledge and exhibit few-shot learning capabilities unseen in traditional models. With just a few demonstrations, these models can generate coherent text in a new style or on a new topic.

![How is generative AI different from traditional AI?](/images/1/Img1_2.png?featherlight=false "How is generative AI different from traditional AI?")

#### How do I use generative AI models?
We **interact with the FMs for generative AI through prompts**. Prompts are used primarily to communicate with text-to-text FMs. In the prompt, we **provide instructions to the FM to generate new content.** Writing good prompts helps get the best responses from the FMs. Examples of a prompt include a question, instruction, or elaborate description. Prompt engineering can involve phrasing a query, specifying a certain style of output, providing relevant context, and assigning a role (such as "act like a human assistant") to the AI. You explore more about prompt engineering in this workshop.

#### Retrieval-Augmented Generation (RAG)
Consider a Q&A based on a specific organization's knowledge base. Although FMs are trained on very large datasets, they might not be trained on the specific data or knowledge base of a particular organization. FMs understand our instructions and language so well, though, that we can provide the context and knowledge base to augment their answers. In essence, **we can provide our knowledge base in the prompt and instruct an FM to answer a question based on that provided content**.

Can we send an entire knowledge base to an FM one time and have it answer questions? Because FMs have built-in limits on how much text they can consume at one time, **we need a way to store the content somewhere else, retrieve the most relevant content, and send the relevant content to the FM to augment its generation. Hence the term Retrieval-Augmented Generation (RAG).**
![Retrieval-Augmented Generation (RAG)](/images/1/Img1_3.png?featherlight=true "Retrieval-Augmented Generation (RAG)")
