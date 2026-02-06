# Agentic AI

## Module 1: Introduction to Agentic Workflows

### Agentic AI

- Agentic AI workflow is a process where an LLM based app executes multiple steps to complete a task

### Degrees of Autonomy

- Less autonomous
    - All steps predetermined
    - All tool use hard coded
    - Autonomy is in text generation
- Semi-autonomous
    - Agent can make some decisions, choose tools
    - All tools predefined
- Highly autonomous
    - Agent makes many decisions autonomously
    - Can create new tools on the fly

### Benefits of Agentic AI

- Much better performance
- Faster than humans because of parallelization
- Modular: can add or update tools, swap out models

### Agentic AI Applications

- Example
    - Invoice processing workflow
    - Responding to customer email
    - Customer service agent
    - Visual computer use
- Easier
    - Clear, step-by-step process
    - Standard procedures to follow
    - Text assets only
- Harder
    - Steps not known ahead of time
    - Plan/solve as you go
    - Multi-modal (sound, vision)

### Task Decomposition

- Building block
    - Models
        - LLMs
            - Text generation, tool use, information extraction
        - Other AI models
            - PDF-to-text, text-to-speech, image analysis
    - Tools
        - API
            - Web search, get real-time data, send email, check calendar
        - Information retrieval
            - Databases, RAG
        - Code execution
            - Basic calculator, data analysis

### Evaluating Agentic AI

- Can evaluate using code (objective evals) or LLM-as-a-Judge (subjective evals)
- Two types of evals:
    - End-to-end and component level
- Examine traces to perform error analysis

### Agentic Design Patterns

- Reflection
- Tool use
- Planning
- Multi-agentic workflows

## Module 2: Reflection Design Pattern

### Reflection to Improve Outputs of a Task

- Reflection to improve code
    - Write code for task X → LLM →code V1 →LLM 2  (reasoning)→ code Final
- Reflection with external feedback
    - Write code for task X → LLM → code V1 → execute code → code output, errors → LLM →code Final

### Why Not just Direct Generation?

- Shots
    - Zero-shot (no examples)
    - One-shot (single examples)
    - Two-shot (two examples)
    - Few-shot (multiple examples)
- Tips for writing reflection prompts
    - Clearly indicate the reflection action
    - Specify criteria to check

### Chart Generation Workflow

- Reflection with a different LLM
    - LLM
        - Code generation
    - LLM 2
        - Reflection

### Evaluating the Impact of Reflection

- Evaluating reflection
    - Objection evals
        - Code-based evals are easier
        - Build a dataset of ground truth examples
    - Subjective evals
        - Use LLM as a judge
        - Rubric-based grading is better

### Using External Feedback

- ROI on prompt engineering
    - Better to start early with reflection and reflection with external feedback
- Examples of tools to help reflection
    - Mentioning competitors
        - Pattern matching for competitor names
    - Fact checking an essay
        - Web search results
    - LLM won’t follow output length guidelines
        - Word count tool

## Module 3: Tool Use

### What are Tools?

- Simple tool execution
    - What time is it? → LLM →get_current_time() → time → LLM → response
- Multiple tools
    - Find free slot on Thu and make appointment → LLM → check_calendar() → times → LLM → make_appointment() → output → LLM → response

### Creating a Tool

- Prompting an LLM to use tools before LLM are trained to use tools
    - You have access to a tool called get_current_time for a specific time zone. To use it, return the following exactly.
    - FUNCTION: get_current_time(”timezone”)
- What time is it in New Zealand? → LLM → if “FUNCTION” in output: → get_current_time(”Pacific/Auckland”) → response

### Tool Syntax

- Tool are created in JSON schema format
- Python library `aisuite` can provide an abstraction for the tools creation
- Code execution tool gives LLM more capabilities

### Code Execution

- It is efficient to let LLM to write code for the tool it needs to use
    - Reflection with external feedback would be helpful to make sure generated code works
- Secure code execution
    - Running outside of a sandbox can be risky
    - Sanboxes can help protect against catastrophic errors
        - Docker
        - E2B

### MCP

- Without MCP, each app creates their own tools
- With MCP, each app uses shared MCP server
- There are many pre-built MCP clients and servers

## Module 4: Practical Tips for Building Agentic AI

### Evaluations

- Driving development process with evals
    - Build a system and look at outputs to discover where it is behaving in an unsatisfactory way
    - Drive improvement by putting in place a small eval with ~20 examples to help you track progress
    - Monitor as you make changes to workflow and see if the metric improves
- Create eval to measure performance
    - Choose 3-5 gold standard discussion points to each topic
    - Use LLM-as-a-judge to count how many topics were mentioned
    - Get score for each prompt in eval set
- Two axes of evaluation
    - X
        - Evaluate with code (objective)
        - LLM-as-a-judge (subjective)
    - Y
        - Per example ground truth
        - No per example ground truth
- Tips for designing end-to-end evals
    - Quick and dirty is okay to start!
    - As you find places where evals fail to capture human judgement as to what system is better, use that as an opportunity to improve the metric
    - Look for places where performance is worse than humans

### Error Analysis and Prioritizing Next Steps

- Tips for error analysis
    - Develop a habit of looking at traces
    - Carry out error analysis to figure out what component performed poorly, leading to a poor final output
    - Use error analysis output to decide where to focus efforts

### More Error Analysis Examples

- Invoice processing workflow
    - Invoice → PDF to text → LLM → update_database() → Record created
    - Error analysis on date:
        - PDF to text
        - LLM data extraction
- Responding to customer email
    - Email → LLM → order_database_query() → LLM → request_review() → Email Reply
    - Error analysis:
        - LLM-drafted query
        - Orders database query
        - LLM-drafted email

### Component-level Evaluations

- Benefits of component-level evaluations
    - Can provide clearer signal for specific errors
        - Avoid the noise in end-to-end system
    - More efficient for focused team to optimize
        - Work on smaller, more targeted problems faster

### How to Address Problems You Identify

- Improving non-LLM component performance
    - Tune hyperparameters of component
        - Web search: Number of results, date range
        - RAG: Change similarity threshold, chunk size
        - ML models: Detection threshold
    - Replace the component
        - Try a different web search engine, RAG provider, etc…
- Improving LLM component performance
    - Improve your prompts
        - Add more explicit instructions
        - Add few-shot prompting
    - Try a new model
        - Try multiple LLMs and use evals to pick the best
    - Split up the step
        - Decompose the task into smaller steps
    - Finetune a model
        - Fine tune on your internal data to improve performance
- Developing intuition for model intelligence
    - Play with models often
        - Having a personal set of evals might be helpful
        - Read other people’s prompts for ideas of how to best use models
    - Use different models in your agentic workflows
        - Which models work for which types of tasks?
        - `aisuite` makes it easy to quickly swap out models

### Latency, Cost Optimization

- Focus on getting high-quality output and then optimize cost and latency later.
- Latency
    - Benchmark or time the agentic workflow
    - Take advantage of parallelism for some steps
    - If LLM steps too long, try small, less intelligent model or faster LLM provider
- Cost
    - Calculate the cost of each step and then benchmark and decide which steps to focus on
        - Costing
            - LLM steps (pay per token)
            - Any API-calling tools (pay per API call)
            - Compute steps (based on server capacity/cost)
- By simply measuring the cost and latency of each step, it gives you a basis with which to decide which components to focus on optimizing

### Development Process Summary

- Build end-to-end system (**Build**)
- Examine outputs, traces (**Analyze**)
- Improve individual component (**Build**)
- Build evals, compute metrics (**Analyze**)
- Error analysis (**Analyze**)
- Component-level evals (**Analyze**)

## Module 5: Patterns for Highly Autonomous Agents

### Planning Workflows

- Let LLM write out multiple steps of a plan, then task it to execute each step of the plan
- Planning design pattern is used successfully in agentic coding systems
- For many other applications, the use of planning is still experimental
- One of the challenges of planning is it’s a bit hard to control because we don’t know at runtime what plan it will come up with

### Creating & Executing LLM Plans

- Formatting plan as JSON
    - Many developers ask LLM to format the plan in JSON format
    - It allows downstream code to parse the steps of the plan exactly
    - JSON or XML are good options instead of plain text

### Planning with Code Execution

- Planning with code execution lets LLM express plans directly as executable code instead of JSON or text
- It enables more flexible and scalable handling of complex queries
- Code-based planning outperforms JSON- and text-based planning

### Multi-agentic Workflows

- Multi-agent systems use multiple specialized agents to collaborate on a task instead of relying on a single agent
- Decomposition: complex tasks are easier to manage when broken into smaller parts
- Each agent represents distinct role and require different tools
- Simple pattern is linear workflow, where agents act sequentially and pass outputs to next agent
- Another pattern is using a manager agent that plans work and delegates tasks to other agents

### Communication Patterns for Multi-agent Systems

- **Linear communication pattern**:
    - Work flows sequentially from one agent to the next (e.g., researcher → designer → writer).
    - Simple, predictable, and commonly used.
- **Hierarchical communication pattern**:
    - A manager agent coordinates tasks, collects results, and redistributes work.
    - Often simpler to manage when all communication flows through the manager.
- **Deeper hierarchical pattern**:
    - Some agents manage their own sub-agents (e.g., researcher with web researcher and fact checker).
    - More powerful but significantly more complex, so less commonly used.
- **All-to-all communication pattern**:
    - Any agent can communicate with any other agent at any time.
    - Enables emergent collaboration but leads to unpredictable outcomes.

###