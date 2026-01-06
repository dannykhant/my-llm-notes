# Google SLM: 01 Build Your Own SLM

### Predict the Next Word

- Language models use probability to predict the next work repeatedly
- The model choose a word with probability distribution and then add the chosen work to the context, then use the new context to predict the next work until a full sentence
- By combining probabilities with sampling mechanism, language models generate wider range of creative outputs

### Create Your Own Probability Distribution

- Prompt is a sequence of words or sentences from which language models predict next word
- Sampling process is the process randomly choose word form a list using the weights
- Probabilities influence the generation of language and must be non-negatives.
- Sensible sentences can be created by manipulating the probabilities
- Fundamental concept
    - Assigning probabilities
    - The impact of context

### N-grams

- N-grams help identify probabilities, predict text, generate sentences, and more
- The co-occurrence and the probability of one word following the next
    
    $$
    P(B|A) = \dfrac{Count(A B)}{Count(A)}
    $$
    
- N-gram is sequence of n words appear together in text
- N-gram tokenization
    - Unigrams
        - Jide, was, hungry, so
    - Bigrams
        - Jide was, was hungry, hungry so
    - Trigrams
        - Jide was hungry, was hungry so
- N-gram language models probabilities are estimated from the counts of n-grams in a dataset
- Context window is the part of the preceding information that influences the model predictions of next work

### Experiment with N-Gram Models

- Tokenization
    - A process that split paragraphs in a dataset into word-like units called tokens
- To compute the probability, B is the next word and A is the context

$$
P(B|A) = \dfrac{Count(A B)}{Count(A)}
$$

- Text corpus
    - The dataset, a collection of texts is referred to as a text corpus
- Workflow
    - Define dataset, break the sentences into individual tokens
    - Create n-grams from tokenized tokens, and calculate counts of n-grams, Count(A B)
    - Estimate P(B | A) using n-gram counts
    - Use estimated P(B | A) distributions to generate next text based on n-gram language model
- Simplest tokenizer is space tokenizer that breaks sentences into individual words based on spaces
- The data sparsity is important property to consider when building n-gram language models
    - The sparsity increases more as the length of the context increases
- Takeaways
    - Functionality
        - N-gram models can be used to predict the next tokens in a sequence based on preceding token (context)
        - It is implemented by estimating conditional probabilities from n-gram counts in the dataset
    - Data sparsity
        - Sparsity is major challenge for n-gram models, especially with higher-order-n-grams (trigrams or larger)
        - Sparsity happens because many possible token combinations are rare or absent in real-world text data
    - Randomness & text generation
        - The model assigns probability to next tokens, the actual choice is random, resulting in different outputs
        - Higher probabilities increase the chance of a token being picked
    - Considerations for text generation
        - Size of n can affect the quality of the generated text, larger n has longer-range dependencies but can lead to sparsity and repetitive output
        - The model is unable to generate text following n-gram that is not present in the dataset
- Limitations of n-grams
    - Small corpus size
    - Inability to handle words that do not appear in the dataset
    - Predictability and repetitiveness
    - Lack of contextual awareness (only consider last n-1 words)

### Trolley Problem

- Decisions may be shaped by perceptions of worth based on social and cultural factors
- LLMs are not making morale decisions as they are not capable of moral reasoning
- LLMs make statistical calculations about prior statements on moral dilemmas and not undertake moral reasoning
- Decision-making may reflect the moral values present in the training data

### Compare N-Gram Models & Transformer Language Models

- Evaluation criteria
    - Fluency
        - Does it read naturally?
    - Coherence
        - Does it make logical sense and stay on topics?
    - Relevance
        - Does it fit the context or prompt?
    - Bias
        - Does the output promote inequalities?
- In the comparison, the main difference between two models are in terms of fluency, coherence and relevance
- Transformers have larger context windows; N-gram models only have n - 1 context window
- Transformers are based on neural networks that can learn sophisticated and abstract patterns
    - For example, can learn that food and snack have related meanings
- Controlling the model output
    - To make output deterministic, choose the token with the highest probability
        - Known as greedy sampling
    - Balancing creativity and consistency
        - For creative tasks, sampling from probability distribution is ideal
        - If accuracy, consistency, and reliability are important, choose the token with highest probability

### Anatomy of a Language Model

- Inference
    - The process of using ML model to make predictions is called inference
- Inference process in Language Model
    - The process of predicting a probability distribution over the next token for a given context
    - It’s repeatedly performed while generating new sequences token by token
- Flow
    - Input (Prompt) ⇒ Model (Parameters) ⇒ Output (Distribution over next token)
- Vector of token IDs
    - Transformer models require a vector (a list) of numbers
    - Prompt is converted to numbers using a mapping where each token is translated to a unique token index (token ID)
    - Output is probability distribution over token IDs

### What does it mean to train a model?

- Training a model
    - It means teaching it to recognize patterns in data
- Training process
    - Predict
        - Model looks at a sequence of words (input) and tries to predict the very next token (target)
    - Compare
        - It then compares its prediction to the actual target token
        - The difference between prediction and target is captured by the loss
            - High loss → bad guess
            - Low loss → closer
    - Adjust
        - Based on the loss, model slightly adjust its parameters to improve the next guess
        - Optimization
            - The process of guessing, checking the loss and adjusting
- Loss
    - Loss measures the mismatch between the prediction and the target
    - Models are trained to lower the loss to its lowest possible value over time
    - Decreasing loss is a sign the model is getting better

### Machine Learning Development Pipeline

1. Data
    - Preprocessing is the essential step of cleaning and preparing data for model training
        - Removing dups, irrelevant content and correcting errors like typos
        - Filter harmful or biased text
        - Once data quality looks high, it is formatted and tokenized
2. Train
    - Training language models from scratch needs a lot of resources
    - Thus, many projects uses existing pre-trained models as starting point
        - These models possess strong language understanding and broad general knowledge
3. Fine-tune
    - Fine-tuning adapts the model to a particular purpose and desired behavior
        - Supervised Fine-tuning (SFT)
            - A pre-trained is trained on a smaller, high-quality dataset specifically created for the target task
        - Reinforcement Learning from Human Feedback (RLHF)
            - Aligning the AI’s behavior with human preferences to make it more helpful and harmless
4. Evaluate
    - Benchmarks
        - Testing the model on standardized questions or tasks (benchmarks) to check capabilities and weaknesses
    - Human evaluation
        - Real people interact with model, rating quality, checking for factual errors, identifying potential harms, and assessing its helpfulness
            - A/B testing different model versions
            - Red teaming, where individuals try to make the model fail
5. Deploy
    - Once the model meets evaluation criteria, it can be deployed
    - Continuous monitoring of the model’s performance with user feedback and system logs is crucial for issues and improvement
    - The ongoing monitoring and evaluation feeds back into development cycle

### Dataset Preparation for Training SLM

- Pre-processing steps
    - Load the dataset and tokenize it
    - Construct a list of all tokens in the dataset
    - Construct a list of unique tokens in the dataset
    - Create a mapping of tokens to token IDs and a mapping of token IDs to tokens
    - Define functions that can translate between tokens and their corresponding IDs
    - Define a Python class that encapsulates all methods necessary for preparing the data for a transformer model
- Loading & tokenizing the dataset
    - We have to tokenize sequences before we can use them to train a transformer model same like training n-gram language models
    - Space tokenizer will be used to split sequences on spaces
    - `re.split` is better at handling texts that contain multiple spaces
- Build the vocabulary
    - Transformer models use a fixed set of tokens that they can process and generate
        - The set of tokens is known as the vocabulary
    - The vocabulary is the list of unique tokens that appear in the training data
- Convert the tokens into token IDs (indices)
    - To train a transformer on a text dataset, we have to turn the text data into a list of token IDs
    - Each token maps uniquely to a different number and the IDs are consecutive
    - Translation between tokens and IDs
        - token_to_index
        - index_to_token
- Encode and decode functions
    - It is much easier to convert between tokens and token indices by implementing encode and decode function
    - `encode` function takes string of text and return list of indices
    - `decode` function takes list of indices and return list of text
- Package the methods in a Python class
    - The class can save us to go through the pre-processing steps again and again to train a transformer
    - The class streamlines the process of converting text into numerical data that can be fed into a language model and converting the output of a language model to human-readable texts

### Train Your Own Small Language Model

- The model we will be training is referred to as a small language model because it has fewer parameters (~3.5 million) instead of 1 billion of Gemma-1B
- Keras
    - Open source deep learning framework to define neural network architectures and train models using those
- Steps
    - Load the dataset, tokenize it, and convert it to token IDs
    - Pad the dataset to make all sequences have the same length
    - Shuffle examples in the dataset and group them into batches
    - Transform the data into model inputs and model targets
    - Train the transformer model
- Tokenization
    - Tokenize the dataset
    - Prepare the vocabulary
    - Provide methods for translating tokens into token IDs and vice versa
- Padding the dataset
    - The input to transformer models is a matrix where each row corresponds to the data for one example in the dataset
    - If each paragraph is an example, each row in a matrix has the IDs of every token in the paragraph
    - But the paragraphs don’t have the same length and it causes a problem when combining the multiple paragraphs into a matrix since every row in a matrix must have same number of entries
    - The solution
        - Solution-1
            - Use a special `<PAD>` token to make all sequences have same length
            - It is done by adding `<PAD>` token at the beginning or the end of the paragraph
        - Solution-2
            - Truncate paragraphs, which is removing the tokens at the beginning or end of a paragraph to make them to have the length of the shortest paragraph
            - This may remove a lot of information from the dataset
    - To get the shortest/longest list in a list of lists
        - `min(list_of_lists, key=len)`
        - `max(list_of_lists, key=len)`
    - To pad and truncating the paragraphs
        - `keras.preprocessing.sequence.pad_sequences()`
- Prepare input and target
    - Training procedure of transformers
        - They repeatedly make guesses of next token
        - If wrong, the training procedure updates the model parameters
        - The model is getting better by this way
    - For the training procedure, we have to prepare
        - Input
        - Target
    - Input
        - A sequence of tokens that is passed into transformer model
        - A part of paragraph, a full paragraph, multiple paragraphs, etc…
    - Target
        - The target sequence is what the model predict from the input
        - The target is same as input sequence but shifted left by one token
        - The target will contain the next token that follow the input sequence
    - Example: (in real-world below are token IDs instead of raw tokens)
        - Input: Table, Mountain, is
        - Target: Mountain, is, beautiful
    - The input sequence and target sequence length is one token shorter because of the left shifting
- Shuffle the dataset and specify the batch size
    - The purpose of shuffling & batches
        - The final step before training is to split the data into groups of a handful of paragraphs called batches
        - To include a diverse set of paragraphs in one batch
            - By shuffling, all paragraphs appear in random order before splitting them up into batches
        - Batch size
            - The number of paragraphs that should be included in one batch
            - Increasing batch size ⇒ speed up the training and lead to better model
            - But larger batch size require more memory
- Flow
    - Dataset ⇒ Padding ⇒ Shuffling ⇒ Batching
- Train a small language model
    - The size of transformer model and the amount of training data has strong impact on model performance
    - Parameters of a transformer model
        - Parameters are a set of numbers that guide the model to perform; they are very large collection of numbers that determine the model behavior
        - Parameters are updated after processing each batch of paragraphs
        - Parameters are initialized with random numbers at the start of training
        - Models are trained by processing the data multiple times; each is known as an iteration or epoch
        - During each training iteration, the parameters are updated to have better predictions of next token
    - Initialize the model
        - max_length
            - The max length of a paragraph in the dataset
        - vocabulary_size
            - The size of vocabulary, the number of unique tokens in the dataset
        - learning_rate
            - How quickly the parameters should be updated
            - Higher value ⇒ Speed up training but may result a worse model
            - Lower value ⇒ Slow down training but likely improves the model
    - Initialize a callback function
        - To make sure the predictions get better over time during training
            - Define a callback function to regularly print the text generation
    - Run the training
        - If we train the model for at least 200 epochs, we will likely get the best results
        - Check the loss changes as training progresses
            - The loss should go down as training continues
- Evaluate the small language model
    - Key questions to evaluate the model quality
        - How good is the model at predicting next tokens for a given prompt?
        - Is the generated text coherent?
        - Is the likely next token when the context is changed slightly?
    - Unseen tokens
        - The unseen tokens are the tokens that did not appear in training dataset
        - They are get replaced by the special string `<UNK>`
- Summary
    - Tokenized the dataset
    - Padded the paragraphs
    - Prepared the input and target data
    - Shuffled and batched the data
    - Trained the SLM
    - Prompted the trained model

### Evaluating Your Model

- Defining the goal for evaluation
    - Evaluation is the determining if the model is good at specific task (goal)
    - The success criteria from one model can be a total failure for another
- Criteria for evaluating a small language model
    - Fluency
        - Does it learn vocabulary and sentence structure?
        - Does it generate grammatically correct sentence?
    - Thematic and stylistic coherence
        - Does it learn recurring themes in the dataset?
        - Is it able to generate coherent texts about these theme?
    - Context sensitivity
        - Does it change its predictions depending on small changes in the context?
    - Bias
        - Does it generate text that are harmful, one-dimensional stereotypes?
- Metrics
    - Automated metrics with test sets
        - The fastest way to get repeatable feedback is to build a small, high-quality test set
    - Abstract metrics: Is the model learning?
        - Loss is the most common metric to ensure the model is fundamentally learning
    - Human evaluation
        - Methods
            - Rating scales
            - Side-by-side comparison
            - Qualitative feedback
        - It is time-consuming method but best type of evaluation because it measures the model performance for the the people who actually use it