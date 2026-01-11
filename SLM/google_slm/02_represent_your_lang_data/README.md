# Google SLM: 02 Represent Your Language Data

### Teaching a machine the soul of language

- To solve the challenges to make machines understand languages
    - Preprocessing
    - Tokenization
    - Embeddings
- Preprocessing
    - Cleaning the messy data with HTML tags, typos, slang and emojis
- Tokenization
    - Breaking the data down into standardized, usable pieces and translate them to numerical token IDs
- Embeddings
    - Embedding is a vector, a list of numbers that is a coordinate for a token in a high-dimensional map of meaning
        - Words with similar meaning are placed close together
        - Allow the model to work with meaning, not just sequences of strings

### Exploring Raw Data

- Preprocessing is a series of thoughtful and contextual decisions depending on the problem solving
- Start by asking: what information is important to accomplish the task and what is noise?

### Preprocessing Data

- HTML tags
    - Text data sometimes includes leftover HTML tags like <br> or <strong>
    - The regex pattern: `r'<.*?>'`
        - r - raw string
        - < and > - literal characters
        - . - wildcard
        - * - quantifier
        - ? - lazy modifier
    - Text data requires to replace HTML entities like &amp;
- Unicode characters
    - `unicodedata` is a Python module to lookup information about any Unicode character
    - Unicode categories
        - L - letter
        - N - number
        - P - punctuation
        - S - symbol
        - Z - separator
        - C - other/ control
    - Process emojis
        - Replace them with descriptive tags
    - Process hashtags
        - Drop the # symbol
    - Map special characters
        - Replace currency symbol with <money> tag
- The way we clean the data directly influences what our model learns

### Tokenization

- It’s the process of converting words into numerical format so language model can learn from them
    - Words ⇒ smaller pieces ⇒ numbers
- Sentence-level tokenization
    - Model can never see the same token twice, making it impossible to learn meaningful pattern
    - This approach is ineffective because tokens are too large and not repetitive enough for model to learn patterns
- Word-level tokenization
    - Breaking sentences into words and it involves building a vocabulary from individual words
    - Model can learn statistical relationships between tokens
    - But this approach has a issue which is out-of-vocabulary tokens
- Out-of-vocabulary tokens
    - If a word wasn’t in original training data, it is also missing from vocabulary, known as out-of-vocabulary (OOV) problem
    - How model can process
        - Ignore the token
        - Map it to special `<UNK>` token
    - OOV problem makes word-level tokens unreliable for real-world text
- Character-level tokenization
    - This solves OOV problem if data has been cleaned and only includes a finite set of characters
    - But there are 2 trade-off:
        - Loss of meaning
        - Inefficient sequences
    - Even though it solves OOV problem, they are not very meaningful and computationally inefficient
- The middle ground
    - The ideal approach lies in the middle of word-level and character-level tokens
    - Subword tokenization
        - A powerful compromise and the standard for modern language models
- Summary
    - Sentence-level tokens
        - Too large, hindering pattern learning
    - Word-level tokens
        - More frequent, meaningful units but faces OOV issue
    - Character-level tokens
        - Solve OOV issue but individual tokens no longer have meaning
    - Subword tokenization
        - Combining the benefits of word-level and character-level approaches

### Tokenize Texts into Characters and Words

- Token frequencies in text data
    - Preprocessing text
        - Remove punctuation such as commas and periods
        - Convert all text to lowercase
    - Counting tokens
        - How many times each token appears in the dataset
- Zipf’s law
    - Some tokens are very common like - the, a, and
    - But majority are rare, this creates long tail distribution
    - To plot long tail distribution, use log-log plot
        - log of rank vs. log of frequency
    - Zipf’s law
        - A token’s frequency is inversely proportional to its rank
            
            $$
            f \alpha \dfrac{1}{r}
            $$
            
            - f = frequency, r = rank
            - Logarithm of the relationship: log(f) = -log(f) + constant
        - The law guarantees any text corpus will be dominated by very frequent tokens
        - If a model encounters a token only a handful of times in a dataset, it will be challenging to learn its meaning
- Tokenization
    - Character-level tokenization
        - many tokens, small vocabulary
    - Word-level tokenization
        - fewer tokens, larger vocabulary
    - Effect of tokenization on sequence length
        - Tokenization method impacts **number of tokens** and **vocabulary size**
        - Longer sequence increase memory and compute demands
    - Effect of tokenization on vocabulary size
        - Larger vocabulary allows for more information to be distributed across tokens
            - With word-level vocabulary, tokens are whole words and information is clearly distributed
            - With char-level vocabulary, single token’s parameters to hold large amount of contextual information, making it difficult to learn representations that capture precise meaning
        - Larger vocabulary requires more parameters for a model
            - It increases the model size and computational cost
        - Larger vocabulary contains more tokens that appear very infrequently in the training data
            - Model cannot learn sufficiently for the rare tokens because of the lack of sufficient examples
            - Model cannot handle less common words well because of this data sparsity
        - Models trained on character-level tokens will require fewer parameters but learn worse meaning representations
        - Models trained on word-level tokens require more parameters but learn much better meaning representations

### Tokenize Texts into Subword Tokens

- Subword tokenization
    - Frequent words (like - the or is) are kept as single, complete tokens
    - Rare or complex words are broken down into smaller, meaningful sub-units
- Byte pair encoding (BPE) algorithm
    - One of the most popular algorithms for subword tokenizations
- Special tokens
    - `<BOS>` and `<EOS>`
        - Beginning of sequence & End of sequence
        - To mark the start and end of a distinct piece of text
            - Efficient batching
            - Dynamic generation
    - `<PAD>`
        - Padding token is used to make all input sequences in a batch the same length
        - Transformer models require inputs to have a fixed size
    - `<UNK>`
        - A placeholder for a char or symbol that is not in the tokenizer vocabulary

### Subword Tokenization

- The standard for all modern language models
- One of the most common algorithms for subword tokenizer is byte pair encoding (BPE) algorithm
- BPE algorithm
    - It learn most useful subword tokens
    - It starts with smallest possible units, individual characters
    - Then iterative merges the most frequent adjacent pairs into new, larger tokens
- Step-1: Initialization
    - BPE splits every word into individual chars and adds a special end-of-word symbol `</w>`
- Step-2: Count all adjacent pairs
    - Count every adjacent pair of tokens across the entire corpus
- Step-3: Merge the most frequent pair
    - The most frequent pair of tokens (”es”) is merged into a single token and this new token is added to the vocabulary
- Step-4: Replace tokens in corpus
    - All occurrences of the token pair (”e”, “s”) in the dataset replace by the new token “es”
- Step-5: Repeat
    - Using the updated corpus, repeat step-2 and 3
- If it sees OOV word, it recognizes the familiar components by tokenizing
- If we train BPE tokenizer on a very big dataset, this solves the OOV problem
- Vocabulary size trade-off
    - Too few merges leaves the vocabulary size small
        - But it results in long token sequences
        - It is inefficient and makes model to learn harder
    - Too many merges create very short, efficient sequences for common words
        - But the vocabulary becomes large, increasing model size and memory usage
    - The goal is to find a balance
        - Large enough to represent common words and meaningful parts
        - Small enough to generalize to new words and keep model size manageable
- Tokenizer tax
    - The additional costs due to design decisions of tokenizer, users working with less resourced languages pay more and get worse results compared to users working with English
    - We can overcome it by training our own tokenizer on data that is relevant to the task

### What Are Embeddings?

- Model needs a way to check the similarity of tokens
- The solution is to map each token ID to a list of numbers called vector
    - It act as a lookup table for model, known as embedding
- Token embedding create high-dimensional map of meaning
- Token embedding (token vector)
    - A list of floating-point numbers that represents a token
    - During training, model learns to place tokens with similar meanings close together in the space
- Learning embeddings
    - Embedding table starts with a matrix of random numbers
    - Embedding table is learned just like the other parameters when model is trained on dataset

### Design Your Own Embeddings

- Token embeddings train language models by capturing similarities between tokens that helps the prediction of next token
- Embeddings require multiple dimensions, each representing a different axis of meaning
    - To avoid conflicts and capture the rich relationships between words
- The power of modern language models comes from their ability to navigate high-dimensional meaning spaces

### Desired Properties of Embeddings

- Meaningful neighborhoods
    - Good embedding space is the tokens with similar meanings are close together
        - This creates semantic neighborhoods
    - The concept is captured by cosine similarity
        - It measures the angle between two vectors and gives a score between -1 and 1
            - Score 1 means perfectly similar
            - Score 0 means unrelated
            - Score -1 means Opposites
        
        $$
        cosine(u,v) = \dfrac{u.v}{||u||.||v||}
        $$
        
        - By using cosine similarity, we can mathematically verify if model group similar words together
- Dense representations
    - Sparse representation, where many dimensions are zero for a given word
    - The embeddings learn by language model are dense, this means every number in a word vector is non-zero value
- Distributional hypothesis
    - The meaning of a word is defined by the words that appear around it
    - Words that show up in similar contexts across millions of sentences have similar meanings
    - Language model learn which words are statistically similar
- Visualizing the meaning space
    - The embeddings of modern language models have hundreds of dimensions
    - Visualization techniques
        - Direct plotting
            - Pick two or three dimensions out of hundreds available
        - Dimensionality reduction
            - Use algorithms like PCA or t-SNE

### Experiment with Embeddings

- Token embeddings are high-dimensional vectors
- Gemma tokenizer uses a vocabulary of more than 260,000 tokens
- Table that stores embeddings is a matrix
- A vector is an ordered list of numbers
- The shape is the dimension of a vector or a matrix
- Dot product
    - Dot product is the most common operations in machine learning
        - $u^Tv$
    - [`np.dot`](http://np.dot) or `np.matmul`
- In the context of embeddings, Dot product is important measure of similarity
    - $u^Tv < 0$ ⇒ Angle between them is bigger than 90 degrees → high level of dissimilarity
    - $u^Tv = 0$ ⇒ Angle between them is 90 degrees → unrelated
    - $u^Tv > 0$ ⇒ Angle between them is less than 90 degrees → similar
- Cosine similarity
    - To make the similarities less dependent on the specific values and number of dimensions, normalize the similarity to return a value between -1 and +1
        - +1 for identical direction
        - 0 for orthogonal vectors
        - -1 for opposite vectors
- Gemma 1B model uses 1,152-dimensional embeddings
- t-SNE
    - This dimensionality reduction technique is suitable for projecting embeddings for visualizations
    - It is a visualization method that preserves the pairwise similarities between data points in lower-dimensional space

### Train SLM with BPE Tokenizer

- Tasks
    - Load and inspect the dataset
    - Train BPE encoder on the dataset
    - Encode and decode example words and sentences how it handles OOV cases
    - Convert tokenized corpus into padded numerical index sequences required for model training
    - Train transformer model
    - Visualize learned embeddings using t-SNE algorithm
- Train BPE tokenizer
    - A key design choice when training BPE tokenizer is to decide how many merges to perform