# Module: 3

## Information Retrieval with Vector Databases

### Introduction

- Vector databases
    - Optimized for huge quantities of vector data
    - Almost synonymous with RAG systems

### Approximate Nearest Neighbors Algorithms (ANN)

- Basic vector retrieval - KNN
    - KNN: K Nearest Neighbors
        - Vectorize all docs and prompts
        - Compute distances to all doc vectors
        - Sort by distance
        - Return closest elements
    - Scaling challenges
        - Linear growth
            - 1000 docs ⇒ 1000 distance calculations per search
- Approximate Nearest Neighbors
    - ANN is significantly faster than KNN
    - Rely on additional data structures
    - Not guaranteed to find the absolute closest docs
- Navigable Small World
    - Compute distances between all doc vectors
    - Add one node to the graph for each doc
    - Connect each node to its nearest neighbors
    - Can traverse the graph moving along edges between neighboring docs
- Hierarchical improvement
    - Hierarchical Navigable Small World (HNSW) enhances Navigable Small World by speeding up early parts of the search
    - Relies on a hierarchical proximity graph
- HNSW
    - Significantly faster than KNN
    - Exponentially fewer vectors in each layer makes it approx logarithmic
    - Allows scaling up to billions of vectors
        - KNN ⇒ Linear
        - HNSW ⇒ Logarithmic
- Takeaways
    - ANN is significantly faster than KNN at scale
    - Find close documents, but can’t guarantee best matches
    - Depends on proximity graphs, computationally expensive to build but can be pre-computed

### Vector Databases

- Vector databases
    - Designed for vector search
    - Outperform relational databases
    - Optimized for ANN search
- Typical vector database operations
    - Database setup
    - Load docs
    - Create sparse vectors for keyword search
    - Create dense vectors for semantic search
    - Create HNSW index to power ANN algorithm
    - Run searches
- Complete workflow
    - Configure database ⇒ Load & index data ⇒ perform search

### Chunking

- Why chunk docs?
    - Token limits
    - Improved relevancy
    - LLM only sent relevant context
- Balancing chunk size
    - Too large (chapter level chunks)
        - Too many topics in one vector
        - Fill LLM context window
    - Too small (word level chunks)
        - Loses surrounding context
        - Reduces search relevance
    - Just right (optimal chunks)
        - Balance between capturing too much and too little
- Methods
    - Fixed size chunking
        - 250 characters per chunk
    - Overlapping chunking
        - Chunks overlap by 25 characters
            - Minimizes words cut off from context
            - Words in the middle of a chunk have context on each size, words on the ends are in two chunks
            - Increases relevancy, uses more space
    - Recursive character splitting
        - Splitting text into chunks at a specified character, for example: newlines
        - Variable chunk size, but better accounts for doc structure
        - Splitting on different characters
            - HTML docs
                - Split <p> or <h1> chars
            - Python
                - Chunk by function definitions
    - Implementing chunking strategies
        - Implement yourself vs. Libraries
            - Fixed sized splitting with overlaps is straightforward to implement yourself or with external libraries
        - Metadata preservation
            - Chunks inherit source document metadata plus location information

### Advanced Chunking Techniques

- Semantic chunking
    - Groups sentences together based on similar meanings rather than arbitrary character limits
    - Pros
        - Follows author’s train of thought
        - Smarter chunk boundaries
        - Higher recall and precision
    - Cons
        - Chunking can be computationally expensive
        - Requires repeated vector calculations
- Language based chunking
    - Prompt LLM to create chunks from a doc
    - Include instructions on types of chunks, like keeping concepts together, adding breaks when new topic starts
    - Performs well, increasingly more economically viable
- Context-aware chunking
    - Costly pre-processing: LLM adds context chunk by chunk
    - Benefit: better search, no impact on speed
    - Added context helps both search relevance and as the LLM generates responses
- Choosing a chunking approach
    - Fixed width & recursive character splitting: good defaults
    - Semantic & LLM chunking: can yield higher performance, but more complex
    - Context-aware chunking: improves any chunking technique at some cost

### Query Parsing

- Query rewriting
    - Uses an LLM to rewrite the query before it’s submitted to the retriever
    - Messy prompt → Query rewriter LLM → Optimized prompt → Retriever
    - Iterate on the prompt for your query rewriter
    - Benefits are substantial, easily justify costs
- Named entity recognition
    - Identifies & categorizes specific types of information within queries, enabling more targeted search & filtering strategies
    - Gliner Named Entity Recognition
- Hypothetical Document Embeddings (HyDE)
    - Uses generated hypothetical docs that would be ideal search results to help with the search process
    - Normally a retriever is matching prompts to documents
    - HyDE means the retriever is matching docs to docs, one is the perfect hypothetical one generated from the prompt
    - Can provide performance improvements but adds latency and some cost

### Cross-encoders & ColBERT

- Bi-encoder
    - Separate semantic vectors: docs & prompts are embedded separately
    - ANN search
    - Doc vectors are pre-computed
- Cross-encoder
    - Concatenate doc & prompt
    - Feed to cross encoder
    - Generate relevance score
    - Pros
        - Provide better search results than bi-encoder
        - Great for improving the results of other search techniques
    - Cons
        - Scale terribly with millions or billions of docs
        - Can’t pre-process since they run on prompt-docs pairs
        - Too inefficient to use as default search techniques
- ColBERT
    - Contextualized Late Interaction Over BERT
        - Split the difference between bi & cross encoders: generate doc vectors ahead of time like bi-encoders but also capture deep text interactions like in cross-encoders
        - Each token gets a vector
        - ColBERT scoring: each prompt vector find its most similar doc vector
            - Similarity scores between doc and prompt tokens
            - MaxSim score the the document
        - Pros
            - Scalability of bi-encode, much of rich interactions of a cross-encoder
            - Reasonably fast, can still be used in real-time or close-to-real-time scenarios
        - Cons
            - Requires significant vector storage as each token, rather than each doc, needs a dense vector
- Key takeaways
    - Bi-encoders
        - Reasonably good quality, great speed, minimal storage, default semantic search
    - Cross-encoders
        - Best quality, extremely slow, minimal storage
    - ColBERT
        - Nearly the quality of cross-encoder, decent speed, significant vector storage
        - ColBERT & similar approaches increasingly supported by vector DBs

### Reranking

- Purpose of reranking
    1. Top ranked docs from hybrid search
    2. Reranking engine
        - Cross encoder
        - LLM scoring
    3. Optimal doc order
- Cross-encoder reranker
    - Cross-encoder give better results than bi-encoders but are slower
    - Using cross-encoders only after initial bi-encoder filtering make the quality-time trade-off feasible
    - Adds minor latency, but typically yields significantly better results
- LLM based scoring
    - LLM based scoring is powerful but costly
    - Like cross encoders, it is too slow for large scale retrieval and is best used for reranking after initial filtering

### Conclusion

- Approximate nearest neighbors
- Vector databases
- RAG techniques
    - Chunking
    - Query parsing
    - Reranking