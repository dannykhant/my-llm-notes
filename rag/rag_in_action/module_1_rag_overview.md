# Module: 1

## RAG Overview

### Introduction to RAG

- RAG
    - LLM are already powerful
    - RAG further improves them
- Two steps for answer questions
    - Retrieval
        - Collect information
    - Generation
        - Reason & Respond
- Traditional language model usage
    - LLM provides great answers
    - But sometimes LLM provides answers are not so great because of:
        - Very recent information
        - Specialized knowledge required
- What LLMs don’t know
    - Private databases: confidential information
    - Hard to access information: some information isn’t widely available online
    - Real time data: trained on past data
- To let LLM know
    - Put the context information in the prompt
    - RAG system ⇒ Augmented prompt ⇒ LLM
- Retriever
    - Manages KB of trusted information
    - Finds the most relevant information & shares it with LLM
    - Improves generation

### Application of RAG

- Code generation
    - LLM needs your project’s context
    - Use your codebase as a KB
    - Improves code generation and Q&A
- Company chatbots
    - Tailored to your company
    - Uses your internal documents
    - Grounds answers in real context
- Specialized knowledge
    - High-impact domains
    - Uses specialized documents
    - Enables accurate, secure use
- AI assisted search
    - Search engines as retrevers
    - AI summarizes search results
    - RAG with the internet as a knowledge base
- Personalized RAG
    - More software includes personal assistants
    - These tools need context
    - Your data is the knowledge base

### RAG Architecture Overview

- Normal LLM use
    - Prompt ⇒ LLM ⇒ Response
- RAG system
    - Prompt ⇒ (Retriever ↔KB) ⇒ Relevant docs ⇒ Augmented prompt ⇒ LLM ⇒ Response
- Advantages of RAG
    - Injects missing knowledge
    - Reduces hallucinations
    - Keeps models up to date
    - Enables source citation
    - Focuses model on generation

### Introduction to LLMs

- LLMs are just fancy autocomplete
- Neural network
    - A complex math model of language
    - Stores which words frequently appear together, in which order, and contextual meaning
    - LLMs use this model to generate text
- Token
    - A piece of a word
    - Some words get single tokens
    - Compound words multiple tokens
    - Punctuation marks
    - ~10,000 - 100,000 tokens in LLM vocabulary, allowing models to represent any possible word with fewer tokens
- Predicting next word
    - Process current state
    - Calculate probabilities
    - Select next token
- Auto-regressive
    - Self-influencing
    - Next tokens make sense in context of old ones
    - Running the same prompt leads to different completion
- How LLMs learn
    - Large text collections ⇒ Trains model (billions of parameters) ⇒ LLM
    - LLM ⇒ Training ⇒ Predictions ⇒ Accurate? ⇒ Update parameters ⇒ Training
- Why LLMs hallucinate
    - LLMs generate probable word sequences
    - Knowledge gaps cause inaccurate responses
    - LLMs are designed to generate probable text, not truthful text
- How RAG solves the problem
    - Retriever + KB ⇒ Relevant context grounds the LLM responses ⇒ LLM
- Why not add everything?
    - Higher computational cost
        - Longer prompts take more computation to run
        - Model performs computationally complex scan of every token
        - Scan happens before generation each new token
    - Context window limit
        - Eventually you hit the limit of LLM context window
        - Smaller models: only a few thousand tokens
        - Largest models: millions

### Introduction to Information Retrieval

- Retriever
    - Documents in a DB
    - Index for search
    - Retriever searches the index
- How retriever works
    - Check the prompt: what does the prompt mean?
    - What documents in the KB are similar?
    - Rank the documents by their scores
- Retriever tradeoffs
    - Relevance vs. irrelevance - return relevant, withhold irrelevant
    - Return every document? - wastes context window
    - Return the single highest ranked doc - miss valuable info
    - No perfect solution - retriever doesn’t perfectly rank docs
    - Monitor & experiment - change settings to find what works
- Vector DB
    - Specialized for retrieval in a RAG system

### Conclusion

- Key concepts
    - RAG pairs an LLM with a KB
    - Data is private, recent, or highly specific and so missing from the LLM’s training data
    - Retriever finds relevant docs and adds them to an augment prompt
    - LLM ground their responses in the retrieved information