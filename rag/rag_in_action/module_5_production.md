# Module: 5

## RAG Systems in Production

### Introduction

- Evaluation & logging
- System optimization
- Multi-modal RAG

### What makes production challenging

- Scaling performance
    - More traffic increases latency and load
    - More requests mean higher memory and compute costs
    - Scaling while keeping performance high is hard
- Unpredictability of prompts
    - Even with rigorous testing, it’s impossible to predict every type of request your RAG system will receive
    - Users are creative and unpredictable
- Messy real world data
    - Data is frequently fragmented, messy or missing metadata
    - Much of it isn’t text-based, it’s in images, PDFs or slide decks
    - Accessing this data requires extraction tools for your knowledge base
- Security & privacy
    - Private by design
    - Many RAG systems are deployed to safely handle proprietary or sensitive data
    - Ensuring privacy while allowing authorized access is essential

### Implementing RAG evaluation strategies

- Key metrics
    - Software performance metrics
        - Track latency, throughput, memory and compute usage
    - Quality metrics
        - Measure user satisfaction and system output quality
- How to track
    - Aggregate statistics
        - Track trends and identify regressions over time
    - Detailed logs
        - Trace individual prompts through your pipeline
    - Experimentation
        - A/B test changes and run secure experiments
- Code-based evaluators
    - Cheapest, simplest, most straightforward
    - Recording prompts per second
    - Unit tests for valid JSON output
    - Nearly free to run
- Human feedback
    - Most costly but captures what code misses
    - Thumbs up/down ratings
    - Detailed text feedback
    - Pre-compiled test datasets
    - Manual quality assessments
- LLM-as-a-Judge
    - Splits the difference between cost & flexibility
    - Can judge if retrieved docs are relevant to the prompt
    - More flexible than code-based and cheaper than human feedback
    - Needs clear rubrics and works best with labels like relevant or irrelevant
- Software performance metrics:
    - Latency
    - Throughput
    - Memory usage
    - Tokens/second
- Quality metrics
    - Human annotation
        - Thumbs up/down
    - LLM-as-a-Judge
        - Response quality
- Retriever
    - Human-annotated dataset
    - Recall & precision
- LLM (RAGAS)
    - Response relevancy
    - Citation quality
    - Noise filtering

### Logging, monitoring and observability

- LLM observability platforms
    - Characteristics
        - Capture system-wide and component level metrics
        - Help log system traffic
        - Enable experimentation with new system settings
    - Example
        - Phoenix by Arize
- Traces
    - Information shown
        - Initial text prompt
        - Query sent to retriever
        - Chunks returned by retriever
        - Processing by reranker
        - Final prompt to language model
        - Generated response
        - Latency
- Try prompts, run experiments, build reports
    - Interactively try out prompts on your system
    - A/B test changes to see how the affect system performance
    - Generate regular reports of key system metrics
- Other monitoring tools
    - Arize and other LLM-observability platforms aren’t the tool for all your monitoring and evaluation needs
    - Use other classic monitoring and observability tools to fill these gaps
- Iteratively improving you RAG system
    - Good observability pipeline leads to flywheel of system improvement
    - Identify bugs in production traffic, try out changes
    - Tune your system to how it’s actually used
    - Experiment with changes → Observe traffic → Evaluate performance

### Customized Evaluation

- Custom datasets
    - Collection of previously received prompts and information on its journey through your system
    - Great deal of flexibility on what data to store
    - What you store determines what evaluation you can run
    - Prompts and responses are good defaults for system-wide evals
    - For detailed evaluation, collect more data from each component
    - Datasets can easily get massive!
- Visualizing data
    - Visualizing data is important for seeing high level trends
    - Clustering tools allow you to identify trends in how your system is used and evaluate each cluster individually

### Quantization

- Before quantization
    - Large LLM and vector embedding sizes
    - Higher memory and compute cost
- After quantization
    - Smaller, faster, and cheaper to run
    - Minimal loss in quality or retrieval relevance
- LLM quantization
    - Typical LLM has 16-bits parameters
    - Models have 1-billion to 1-trillion parameters, meaning huge amount of memory
    - Quantized models compress parameters to 8-bit or 4-bit equivalents, shrinking the memory footprint
- The quantization process
    - Find min & max values
    - Divide range into 256 sections
    - Assign integers
    - Store min value & section size
- Quantization performance
    - 8-bit integer quantization delivers remarkable performance despite simple approach
    - Embedding models: only few percentage points drop in recall@K benchmark
    - LLM: minor performance drops in standard benchmarks
- 1-bit quantized embedding models
    - Compress model size by 32x
    - Each value is either 0 or 1
    - Performance can drop noticeably
    - Fast 1-bit retrieval + full precision reranking
- Matryoshka quantization
    - Choose your vector size (e.g. first 100 dims)
    - Dimensions sorted by information
    - Flexible retrieval approaches

### Cost vs. Response Quality

- Primary RAG cost drivers
    - Vector database: storage & query cost
    - LLM: inference & generation cost
- Optimizing LLM cost
    - Smaller models
        - Use smaller core models or smaller models in agentic components of overall system
        - Models may be smaller to begin with or have been quantized
        - Finetune small model to one specific task
    - Smaller prompts
        - Retrieve fewer docs (reduce top-k)
        - Use system prompts to encourage shorter responses or set token limits
- Vector database cost reduction
    - Storage types
        - RAM
        - Disk memory
        - Cloud object storage
    - Key principles
        - Store HNSW index in RAM for faster retrieval
        - More rarely accessed vectors to SSD/disk
        - Keep doc contents in object storage
    - Multi-tenancy
        - Divide docs in your database by users they belong to
        - Each tenant has their own HNSW index
        - Dynamically move tenant data to RAM or slow storage
        - On-demand data loading
            - Load data to RAM when only needed
        - Time zone optimization
            - Move data to faster storage during daytime in their region
        - Multi-tenancy makes it more efficient to move data in & out of expensive memory

### Latency vs. Response Quality

- Latency culprit
    - Most latency come from transformers
    - LLM calls are the main bottleneck
    - Retrieval and databases are fast
- LLM latency techniques
    - Smaller or quantized model
        - Faster on same hardware
    - Router LLM
        - Skip unnecessary steps that increase latency
- Caching
    - Direct caching
        - Return cached responses immediately when close matches are found, skipping the slow generation step entirely
    - Personalized caching
        - Feed cache response and user prompt to a small, fast LLM to make adjustments for better relevance
- Retrieval latency
    - Quantized embeddings
        - Use binary/low-bit quantized vectors
    - Database sharding
        - Split large indexes across instances
    - Leverage provider tools
        - Most platform support these features

### Security

- Data tenant separation
    - Separate tenant
        - Each user access only their authorized database
    - Single database
        - All docs in one database with metadata filtering
- Vector database challenges
    - Store vectors in decrypted memory
        - Require for ANN to function
    - Encrypt text chunks if needed
        - Can be decrypted later for prompt building
    - Balance security and complexity
        - Add protection but may increase latency
- Vector reconstruction risk
    - Text reconstruction from dense vectors if possible
    - Mitigation  techniques
        - Adding noises to dense vectors
        - Applying transformation to vectors
        - Reducing dimensionality while preserving distances

### Multi-modal RAG

- Multi-modal
    - Text doc
    - Images
    - Audit & video
- Multi-modal model
    - Language vision model
        - Processes both text and images using a shared token sequence
    - Image tokenization
        - Breaks images into patch-based tokens, typically 100-1,000 tokens
    - Multi-model transformers
        - Understands text-image relationships through a uniform transformer
- Common file formats
    - Enable ingesting many common file formats
    - Easily treated as image files
- PDF RAG
    - Slides and PDFs are information dense
        - Text
        - Captions
        - Charts
        - Images
    - PDF RAG
        - PDF/Slide → Split into patches then vectorized
        - Scoring works like ColBERT
            - Prompt tokens find the most similar patch in each document, and these maximum similarity scores are added up

### Conclusion

- Production challenges vs. prototyping
    - Increased traffic & usage volume
    - Greater exposure to unpredictable errors
    - Higher stakes for failures and downtime
- Managing trade-offs
    - Can’t simply optimize for response quality
    - Keep costs under budget
    - Keep latency inside target range
- Security
    - Protect your knowledge base
- Multimodal RAG
    - Use RAG with PDFs, slides and more