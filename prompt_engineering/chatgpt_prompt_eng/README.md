# Prompt Engineering

### Introduction

- Two types of LLM
    - Base LLM
        - Predicts next word, based on text training data
    - Instruction Tuned LLM
        - Tries to follow instructions
        - Fine-tune on instructions & good attempts at the instructions
        - RLHF: Reinforcement Learning with Human Feedback

### Guidelines

- The two principles of prompting
    1. Write clear & specific instructions
    2. Give the model time to think
- Principle-1
    - Tactic-1: Use delimiters
        - Examples: ```, """, <>, <tag></tag>, :
    - Tactic-2: Ask for structured output
        - Examples: JSON, HTML
    - Tactic-3: Check whether conditions are satisfied
        - Example:
            - Rewrite the text in the following step by step format if it contains sequence of instructions
                - Step 1, Step 2, Step N
            - If the text does not contain sequence of instructions then simply write
                - No steps provided
    - Tactic-4: Few-shot prompting
        - Give successful examples of completing tasks then ask model to perform the task
        - Example: Your task is to answer in consistent style
            - <child>: Teach me about patience
            - <grandparent> Some answers…
            - <child>: Teach me about resilience
- Principle-2
    - Tactic-1: Specify the steps required to complete a task
        - Example: Your task is to perform following actions:
            - Action 1, Action 2
            - Use the following format
                - Title 1: <Action 1’s output>
                - Title 2: <Action 2’s output>
    - Tactic-2: Instruct the model to work out its own solution before rushing to a conclusion
        - Example: Your task is to determine the solution is correct or not
            - To solve it, do the following:
                - First, work out your own solution to the problem
                - Then compare your solution to the solution provided and evaluate the provided solution is correct or not
                - Don’t decide if the provided solution is correct until you have done the problem yourself
- Model Limitations
    - Hallucination
        - Make statements that sound plausible but not true
    - Reducing hallucinations
        - First find relevant information
        - Then answer the question based on the relevant information

### Iterative Prompt Development

- Iterative Development
    - Idea ⇒ Implementation ⇒ Experimental result ⇒ Error analysis
- Iterative Process
    - Try something
    - Analyze where the result does not give what you want
    - Clarify instructions, give more time to think
    - Refine prompts with a batch of examples

### Summarizing

- Summarize with word/sentence/character limit
- Summarize with a focus
- Try “extract” instead of “summarize”
- Summarize multiple

### Inferring

- Infer sentiment (positive/negative)
- Identify types of emotions
- Identify anger
- Extract specific information (such as product, company name) from text
- Infer topics
- Make news alert for certain topics
    - Create a topics list
    - Prompt (This is zero-shot prompt)
        - Determine each item in following topics list is a topic in the text below, give your answer with 0 or 1
    - Send alert if the specific topic is 1

### Transforming

- Translation
- Universal Translator
- Tone Transformation
- Format Conversion (eg., JSON → HTML)
- Spellcheck/ Grammar check

### Expanding

- Use sentiment to customize the automated email reply
- Tune creativity with `Temperature` parameter
    - It allows to change the variety of model’s responses
        - Higher temperature ⇒ More random

### Chatbot

- OpenAI API call
    - user ⇒ input ⇒ assistant
    - assistant ⇒ output ⇒ user
- Roles
    - system
        - Sets behavior of assistant
    - assistant
        - Chat model
- Manage conversation context and memory

### Summary

- Principles
    - Write clear & specific instructions
    - Give the model time to think
- Iterative prompt development
- Capabilities
    - Summarizing
    - Inferring
    - Transforming
    - Expanding
- Building a chatbot