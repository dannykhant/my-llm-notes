# LangChain for LLM App Development

### Overview of LangChain

- Open-source development framework for LLM application
- Python and JavaScript (TypeScript) packages
- Focused on composition and modularity
- Key value adds:
    - Modular components
    - Use cases: Common ways to combine components
- Components
    - Models
    - Prompts
    - Indexes
    - Chains
    - Agents

### Models, Prompts and Parsers

- Why use prompt templates?
    - Prompts can be long and detailed
    - Reuse good prompts when you can
    - LangChain also provides prompts from common operations
- LangChain output parsing works with prompt templates
    - LangChain functions parse the LLM’s output assuming that it will use certain keywords
        - Keywords for Chain of Thought Reasoning (ReAct)
- Model
    - LangChain provides abstraction for LLM
    
    ```python
    from langchain.chat_models import ChatOpenAI
    ```
    
- Prompt template
    - Prompt template can be useful for re-usability and collaboration
    
    ```python
    from langchain.prompts import ChatPromptTemplate
    ```
    
- Output parsers
    - To define the format of LLM output
    
    ```python
    from langchain.output_parsers import ResponseSchema, StructuredOutputParser
    ```
    

### Memory

- LLM are stateless
    - Each transaction is independent
    - Chatbots appear to have memory by providing the full conversation as context
- When conversation is longer, the amount of memory is also longer and that will cost a lot of tokens to the LLM
- LangChain provides several kinds of memory to store and accumulate the conversation
- To create conversation
    
    ```python
    from langchain.chains import ConversationChain
    ```
    
- Memory types
    - ConversationBufferMemory
        - For storing messages and then extracts the messages in a variable
    - ConversationBufferWindowMemory
        - A list of interactions of the conversation over time, it only uses the last K interactions
    - ConversationTokenBufferMemory
        - A buffer of recent interactions in memory, and uses token length rather than number of interactions to determine when to flush interactions
    - ConversationSummaryMemory
        - A summary of the conversation over time
- Additional memory types
    - Vector data memory
        - Stores text in a vector database and retrieves the most relevant blocks of text
    - Entity memories
        - Using LLM, it remembers details about specific entities
    - We can also use multiple memories at one time
        - Conversation memory + Entity memory to recall individuals
    - We can also store the conversation in conventional database - key-value store or SQL

### Chains

- LLM Chain
    - The most basic type of chains
- Sequential Chain
    - Another type of chains to combine multiple chains where the output of one chain is the input of next chain
    - Two types of sequential chains:
        - SimpleSequentialChain
            - Single input/ output
        - SequentialChain
            - Multiple inputs/ outputs
- Router Chain
    - To route the input to the respective destination chain
        - Input ⇒ Router Chain (if input related to subj) ⇒ Destination Chain ⇒ Output
        - Input ⇒ Router Chain (else) ⇒ Default Chain ⇒ Output

### Q&A over Documents

- LLM on documents
    - LLM can only inspect a few thousand words at a time
- Embeddings
    - Embedding vector captures content/ meaning
    - Text with similar content will have similar vectors
- Vector database
    - Create
        - Documents ⇒ Chunks ⇒ Embed ⇒ Embedding vector (Original chunks) in Vector database
    - Index
        - Query ⇒ Embed ⇒ Compare all entries ⇒ Pick the n most similar
        - The returned values can now fit in LLM context
- Stuff method
    - Documents ⇒ Prompt ⇒ LLM call ⇒ Output
    - Stuffing is the simplest method, simple stuff all data into prompt as context to pass to LLM
    - Pros
        - Single call to LLM and LLM has access to all data at once
    - Cons
        - LLMs have limited context length so it will not work with large or many documents
- Map_reduce method
    - Documents ⇒ Chunks ⇒ Multiple LLM calls ⇒ Overall LLM call ⇒ Output
- Refine method
    - Documents ⇒ Chunks ⇒ 1st LLM call ⇒ 2nd LLM call ⇒ … ⇒ Output
- Map_rerank method
    - Documents ⇒ Chunks ⇒ Multiple LLM call with (score returned) ⇒ Select highest score ⇒ Output

### Evaluation

- To create Q&A application
    
    ```python
    from langchain.chains import RetrievalQA
    ```
    
- To create LLM-generated examples for evaluation
    
    ```python
    from langchain.evaluation.qa improt QAGenerateChain
    ```
    
- To do manual evaluation
    
    ```python
    langchain.debug = True
    ```
    
- LLM assisted evaluation
    
    ```python
    from langchain.evaluation.qa import QAEvalChain
    ```
    
- LangChain evaluation platform
    - It’s a UI to to view the evaluation results in a nicer look

### Agents

- LLM as a reasoning engine rather than knowledge store
- Build-in LangChain tools
    
    ```python
    from langchain.agents import load_tools
    ```
    
    - llm-math
    - wikipedia
- To create agent
    
    ```python
    from langchain.agents import initialize_agent
    ```
    
- To create Python agent with PythonREPLTool
    
    ```python
    from langchain.agents.agent_toolkits import create_python_agent
    from langchain.tools.python.tool import PythonREPLTool
    ```
    
- To create your own tool
    
    ```python
    from langchain.agents import tool
    
    @tool
    def tool_func(param):
    	pass
    ```