# Module: 2

## Information Retrieval & Search Foundations

### Introduction

- Prompts ↔ Retriever ↔ Documents
    - Prompts: unstructured conversational
    - Retriever: needs to rapidly find relevant documents despite this mess!
    - Documents: wide range of formats designed for humans to read

### Retriever Architecture Overview

- Two search approaches
    - Keyword search
        - Finds docs with exact words found in prompt
    - Semantic search
        - Finds docs with similar meaning to prompt
- Hybrid search techniques
    - Keyword search
        - Ensures sensitivity to exact words
    - Semantic search
        - Finds docs with similar meaning even without matching words
    - Metadata filtering
        - Excludes docs based on rigid criteria
- High-performing retrievers balance all three techniques based on project needs

### Metadata Filtering

- Metadata filtering in RAG
    - It doesn’t perform retrieval, it narrows down results from other techniques based on user attributes, not query content
        - Subscription filtering
        - Geographic filtering
- Pros & Cons
    - Pros
        - Simple to understand & debug
        - Fast, optimized, mature, and reliable
        - Enforces strict retrieval rules, matching exact filter criteria
    - Cons
        - Not true search
        - Rigid, ignores content, and provides no way for ranking
        - Useless alone

### Keyword search: TF-IDF

- It uses sparse vectors
- It uses frequency based scoring (TF): more keyword occurrences  → more points
- Normalized TF scoring
    - But longer docs may contain keywords many times because they are longer
    - To fix that: normalize by doc-length (term frequency - TF)
        - number of keyword occurrences / total words in doc
- Basic TF scoring treats all words equally, to fix the importance of common filler words
    - Weight terms using: inverse document frequency (IDF)
        - TF(word, doc) x log(docs containing word / total docs)
- Documents with rare keywords score higher than docs with common words

### Keyword search: BM25

- BM25 scoring
    - Best matching 25 (BM25) was named as the 25th variant in a series of scoring functions proposed by its creators
    - This gives the score for a single keyword
    - Sum scores across all keywords for total relevance score for a document
- Tunable parameters
    - k - term frequency saturation
        - How much term frequency influences the score
        - Range: 1.2 - 2.0
        - Effect: Higher values increase the impact of term frequency, lower values reduce it
    - b - length normalization
        - The degree of normalization for doc-length
        - Range: 0 (no-normalization) and 1 (full-normalization)
        - Effect: Balances favoring shorter vs. longer docs
- TF-IDF vs. BM25
    - Standard keyword search algorithm in production retrievers
    - Better performance + same cost + more flexibility than TF-IDF
- Keyword search overview
    - Match docs by keyword frequency
        - Sparse vectors → Scored → Ranked
    - TF-IDF
        - Keyword rarity
        - Term frequency
        - Document length
    - BM25
        - Doc length normalization
        - Term frequency saturation
- Keyword search strength
    - Simplicity
    - Guaranteed keyword matching

### Semantic Search

- Semantic search vs. Keyword search
    - Prompt & docs, each get a vector
    - Vectors compared to generate scores
    - The main difference is how vectors are assigned
        - Keyword search: count words
        - Semantic search: use embedding model
- Embedding models
    - Embedding models map tokens to a location in space, the location is represented by a vector
    - Embedding models have multiple dimensions: 100 - 1000+
    - Model types
        - Word embedding model
        - Sentence embedding model
        - Document embedding model
- Measuring vector distance
    - Euclidean distance
    - Cosine similarity
        - Opposite direction: -1
        - Perpendicular: 0
        - Same direction: 1
    - Dot product
        - Opposite direction: negative
        - Perpendicular: 0
        - Same direction: positive
- Semantic search
    - Docs + Prompts ⇒ Embedding model ⇒ Vector space ⇒ Rank by distance & return closest documents

### Hybrid search

- Key strategies
    - Metadata filtering
    - Keyword search
    - Semantic search
- Reciprocal Rank Fusion (RRF)
    - Rewards docs for being highly ranked on each list
    - Control weight of keyword vs. semantic ranking
    - Score points equal to reciprocal of ranking
    - Total points from all ranked list used to perform final ranking
    - Parameter: k
        - RRF only cares about ranks, not scores
        - To make single high rank doesn’t dominate overall ranking
            - k = 0, 1 vs 10 = 10x difference
            - k = 50, 1 vs 10 = 1.2x difference
    - Parameter: Beta
        - To weight semantic vs. keyword
            - B = 0.8, semantic 80% - keyword 20%

### Evaluating Retrieval

- Ingredients to retriever quality metrics
    - The prompt
    - Ranked results
    - Ground truth (to evaluate retriever, need to know correct answers)
- Metrics
    - Precision
        - Relevant retrieved / Total retrieved
        - Penalizes for returning irrelevant docs
    - Recall
        - Relevant retrieved / Total relevant
        - Penalized for leaving out relevant docs
- Top k
    - How many docs the retriever trurns
    - Metrics are discussed in terms of top-k documents
        - Top-5 or Top-1 is stricter
        - Top-5 to Top-15 often used
- Mean average precision
    - MAP@K evaluates avg precision for relevant docs in first K documents
    - Rewards ranking relevant docs highly
- Mean reciprocal rank
    - The later the first relevant doc appears, the worse the reciprocal rank
    - MRR averages over many prompt
- How to use retriever metrics
    - Recall or recall@K
        - Captures fundamental goal of finding relevant docs
    - Precision & MAP
        - Asses irrelevant docs and ranking effectiveness
    - Mean Reciprocal Rank
        - How well model performs at the very top of ranking
- Metrics help:
    - Evaluate retriever performance
    - Check if adjustments improve results
    - All metrics depend on having ground truth relevant docs

### Embedding Model Deepdive

- Positive & negative examples in training
    - Compile massive training dataset of positive & negative pairs
- Contrastive training process
    - Update internal parameters based on scoring the positive & negative pairs
    - Repeat the process: Embed → Score with pairs → Update parameters
    - Iteratively repeat the process, improving the model
- Key takeaways
    - Semantic vectors are abstract and somewhat random
    - Before training: locations in space have no meaning
    - After training: locations have meaning because clusters of similar text have formed
    - Only compare vectors from same embedding model

### Conclusion

- Keyword search
    - Ranks by keyword frequency exact matches
- Semantic search
    - Ranks by meaning, flexible
- Metadata filtering
    - Excludes by criteria
- Hybrid search
    - Combines all three techniques
- Evaluation metrics
    - Measure improvement from adjusting tunable parameters in hybrid search
        - Precision & Recall
        - MAP
        - Mean Reciprocal Rank