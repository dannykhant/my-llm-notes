# Finetuning Large Language Models

### Introduction

- How to use LLM on my own data for my own task
- Finetuning is a good way to adjust the LLM tone
- Training LLM takes massive amount of data and massive GPU resources
- In finetuning, takes an existing LLM and train it with your own data

### Why Finetune

- Why?
    - Finetuning is taking general purpose models such as GPT-5 and specializing them into something like ChatGPT or Copilot
    - Analogy
        - PCP ⇒ Cardiologist / Dermatologist
- What does finetuning do for the model?
    - Lets you put more data into the model than what fits into the prompt
    - Gets the model to learn the data, rather than just get access to it
    - Steers the model to more consistent outputs
    - Reduces hallucinations
    - Customizes the model to a specific use case
    - Process is similar to the model’s earlier training
- Prompt engineering vs. Finetuning
    - Prompting
        - Pros
            - No data required to get started
            - Smaller upfront cost
            - No technical knowledge needed
            - Connect data through retrieval (RAG)
        - Cons
            - Much less data fits
            - Forgets data
            - Hallucinations
            - RAG misses or, gets incorrect data
    - Finetuning
        - Pros
            - Nearly unlimited data fits
            - Learn new information
            - Correct incorrect information
            - Less cost afterwards if smaller model
            - Uses RAG too
        - Cons
            - More high-quality data
            - Upfront compute cost
            - Needs some technical knowledge, esp. data
- Benefits of finetuning your own LLM
    - Performance
        - Stop hallucinations
        - Increase consistency
        - Reduce unwanted info
    - Privacy
        - On-prem or VPC
        - Prevent leakage
        - No breaches
    - Cost
        - Lower cost per request
        - Increased transparency
        - Greater control
    - Reliability
        - Control uptime
        - Lower latency
        - moderation
- Tools
    - PyTorch
    - Hugging Face
    - Lamini
        
        ```python
        from lamini import Lamini
        ```
        

### Where finetuning fits in

- Pretraining
    - Model at the start:
        - Zero knowledge about the world
        - Can’t form English words
    - Next token prediction
    - Giant corpus of text data
    - Often scraped from the internet: unlabeled
    - Self-supervised learning
    - After training:
        - Learns language
        - Learns knowledge
- What is - data scraped from the internet?
    - Often not publicized how to pre-train
    - Open-source pre-training data: The Pile
    - Expensive & time-consuming to train
- Finetuning after pre-training to overcome the limitation of base models
    - Pre-training ⇒ Base model
    - Pre-training ⇒ Base model ⇒ Finetuning ⇒ Finetuned model
    - Finetuning usually refers to training further
        - Can also be self-supervised unlabeled data
        - Can be labeled data you curated
        - Much less data needed
        - Tool in your toolbox
    - Finetuning for generative tasks is not well-defined:
        - Updates entire model, not just part of it
        - Some training objective: next token prediction
        - More advanced ways reduce how much to update
- What is finetuning doing for you?
    - Behavior change
        - Learning to respond more consistently
        - Learning to focus, e.g. moderation
        - Teasing out capability, e.g. better at conversation
    - Gain knowledge
        - Increasing knowledge of new specific concepts
        - Correcting old incorrect information
    - Both
- Tasks to finetune
    - Just text-in, text-out:
        - Extraction: text in, less text out
            - Reading
            - Keywords, topics, routing, agents (planning, reasoning, self-critic, tool use), etc…
        - Expansion: text in, more text out
            - Writing
            - Chat, write emails, write code
    - Task clarity is key indicator of success
    - Clarity means knowing what’s bad vs. good vs. better
- First time finetuning
    1. Identify tasks by prompt engineering a LLM
    2. Find task that you see an LLM doing ~OK at
    3. Pick one task
    4. Get ~1000 inputs and outputs for the task
    5. Fine a small LLM on this data

### Instruction Finetuning

- What is instruction finetuning?
    - AKA instruction-tuned or instruction-following LLMs
    - Teaches model to behave more like a chatbot
    - Better user interface for model interaction
        - Turned GPT-3 into ChatGPT
        - Increase AI adoption, from thousands of researchers to millions of people
- Instruction following datasets
    - Some existing data is ready as-is, online:
        - FAQs
        - Customer support conversations
        - Slack messages
- LLM data generation
    - Non-Q&A data can also be converted to Q&A
        - Using a prompt template
        - Using another LLM
        - ChatGPT (Alpaca)
        - Open-source models
- Instruction finetuning generalization
    - Can access model’s pre-existing knowledge
    - Generalize following instructions to other data, not in finetuning dataset
        - Finetuning data
            - What’s the capital of France?
            - Paris
        - Code not in finetuning data, only base data, model can now answer
            - Write a function that computes fibonacci sequence in Python?
            - def fibonacci(n): …
- Overview of finetuning
    - Iterative process
        - Data prep ⇒ Training ⇒ Evaluation ⇒ Data prep

### Data Preparation

- What kind of data?
    - Better
        - Higher quality
        - Diversity
        - Real
        - More
    - Worse
        - Lower quality
        - Homogeneity
        - Generated
        - Less
- Steps to prepare data
    - Collect instruction-response pairs
    - Concatenate pairs (add prompt template, if applicable)
    - Tokenize: Pad, Truncate
    - Split into train/test
- Tokenizing data
    - Converting words/subwords into IDs
    - There are multiple popular tokenizers
        - Use the tokenizer associated with the model you use

### Training Process

- Training: same as other neural networks
    - Process
        - Add training data
        - Calculate loss
        - Backprop through model
        - Update weights
    - Hyperparameters
        - Learning rate
        - Learning rate schedule
        - Optimizer
- Moderation
    - Encouraging model to be not to off-track of the content purpose

### Evaluation & Iteration

- Evaluating generative models is difficult
    - Human expert evaluation is most reliable
    - Good test data is crucial
        - High-quality
        - Accurate
        - Generalized
        - Not see in training data
    - Elo comparisons also popular
- LLM benchmarks: suite of evaluation methods
    - ARC
        - A set of grade-school questions
        - Useful for finding base model but not quite useful for evaluating non-grade-school related finetuned models
    - HellaSwag
        - A test of common sense
    - MMLU
        - A multitask metric covering elementary math, US history, computer science, law and more
    - TruthfulQA
        - It matures a model’s propensity to reproduce falsehoods commonly found online
- Error analysis
    - Understand base model behavior before finetuning
    - Categorize errors
        - Iterate on data to fix these problems in data space
            - Misspelling
            - Too long
            - Repetitive

### Consideration on Getting Started Now

- Practical approach to finetuning
    - Figure out your task
    - Collect data related to the task’s inputs/outputs
    - Generate data if you don’t have enough data
    - Finetune a small model: 400M-1B
    - Vary the amount of data you give the model
    - Evaluate your LLM to know what’s going well vs. not
    - Collect more data to improve
    - Increase task complexity
    - Increase model size for performance
- Tasks to finetune vs. model size
    - Complexity: more tokens out is harder
        - Extract: “reading” is easier
            - Keywords, topics, routing, agents
        - Expand: “writing” is harder
            - Chat, write emails, write code
    - Combination of tasks is harder than 1 task
    - Harder or more general = larger model
        - Might want an agent to be flexible and do several things at once or in one step
- PEFT
    - Parameter-Efficient Finetuning
        - Additive
        - Adapters
        - Soft prompts
        - Selective
        - Reparametrization-based
- LoRA
    - Low-Rank Adaptation
        - Fewer trainable parameters: for GPT-3, 10000x less
        - Less GPU memory: for GPT3, 3x less
        - Slightly below accuracy to finetuning
        - Same inference latency
    - Train new weights in some layers, freeze main weights
        - New weights: rank decomposition matrices of original weights’ change
        - At inference, merge with main weights
    - Use LoRA for adapting to new, different tasks