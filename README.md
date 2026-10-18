# LangChain Academy: Introduction to LangChain (Python)

My hands-on practice repo for the official LangChain Academy course
[Introduction to LangChain - Python](https://github.com/langchain-ai/lca-lc-foundations).
Every notebook is typed and run by me, then compared with the course code.

**Goal:** build the skills for AI Engineer / Agentic AI Engineer roles, and turn the
three course projects into standalone portfolio repos.

## What I'm learning

```mermaid
flowchart LR
    A[Module 1<br/>Foundations] --> B[Module 2<br/>Tools, MCP, State, Multi-agent]
    B --> C[Module 3<br/>Production agents]
    C --> D[Portfolio projects]
    A -.-> P1[Personal Chef]
    B -.-> P2[Wedding Planner]
    C -.-> P3[Email Assistant]
```

## Progress

### Module 1: Foundations
- [x] 1.1 Foundational models (initialising and invoking a model)
- [x] 1.2 Prompting (basic, few-shot, structured prompts, structured output)
- [x] 1.3 Tools (tool definitions)
- [x] 1.4 Web search tool (Tavily)
- [x] 1.5 Memory (checkpointer and `thread_id`)
- [x] 1.6 Multimodal messages
- [x] 1.7 Project: Personal Chef agent (`personal_chef.py`)

### Module 2: Tools, MCP, state and multi-agent
- [x] 2.1 MCP: client, servers, travel agent
- [x] 2.2 Runtime context and state
- [x] 2.3 Multi-agent
- [x] Bonus: RAG over a PDF, SQL agent
- [ ] 2.4 Project: Wedding Planner

### Module 3: Production agents
- [x] 3.2 Managing messages (summarization and trimming)
- [x] 3.3 Human-in-the-loop (approve, reject, edit)
- [x] 3.4 Dynamic models
- [ ] 3.5 Project: Email Assistant

## Repo layout

```
langchain-academy/
├── notebooks/
│   ├── module-1/    # foundations + Personal Chef
│   ├── module-2/    # MCP, state, runtime context, multi-agent, RAG, SQL
│   └── module-3/    # message management, HITL, dynamic models
├── pyproject.toml   # dependencies (managed with uv)
├── .env             # API keys (not committed)
└── README.md
```

## Key concepts covered

| Concept | Where |
|---|---|
| `init_chat_model`, `create_agent` | Module 1 |
| `@tool`, web search, structured output | Module 1 |
| Checkpointer (`InMemorySaver`) and `thread_id` | 1.5 |
| MCP servers and `MultiServerMCPClient` | 2.1 |
| State (`AgentState`) vs runtime context (`ToolRuntime`) | 2.2 |
| Multi-agent, RAG, SQL | 2.3, bonus |
| `SummarizationMiddleware`, `@before_agent`, `RemoveMessage` | 3.2 |
| `HumanInTheLoopMiddleware`, `Command(resume=...)` | 3.3 |
| Dynamic model selection | 3.4 |

## Setup

```bash
git clone https://github.com/sivahere4cloud/langchain-academy.git
cd langchain-academy
uv sync
```

Create a `.env` file in the repo root:

```
OPENAI_API_KEY=...
TAVILY_API_KEY=...
# optional, only for the notebooks that use them
ANTHROPIC_API_KEY=...
GOOGLE_API_KEY=...
```

Open any notebook in VS Code or Jupyter and select the `.venv` kernel.
Restart the kernel after installing new packages.

## Windows note

The MCP notebooks need `pywin32` (already in `pyproject.toml`) and the asyncio
fix cell at the top of the notebook.

## Workflow

One branch per module, one pull request per module into `main`,
merged with a merge commit so every commit stays visible.

## Next

Standalone portfolio repos, each with a README flowchart:
`personal-chef-agent`, `wedding-planner-multi-agent`, `email-assistant-hitl-agent`.

```mermaid
flowchart TD
    START(["Start: pick a model, build an agent, run it"])

    subgraph M1["MODULE 1: Foundations"]
        direction TB
        A1["1.1 init_chat_model model_name<br/>returns: chat model object"]
        A2["create_agent model, tools, system_prompt,<br/>response_format, checkpointer<br/>returns: agent, a runnable graph"]
        A3["agent.invoke messages with HumanMessage<br/>returns: dict, answer = response messages -1 .content"]
        A4["agent.stream, stream_mode messages<br/>returns: token and metadata pairs"]
        A5["1.2 Prompting: system_prompt, few-shot examples,<br/>response_format = Pydantic class<br/>returns: response structured_response"]
        A6["1.3 @tool on a function with docstring and type hints<br/>returns: tool the model can call"]
        A7["1.4 web_search tool using TavilyClient.search query<br/>returns: dict of results"]
        A8["1.5 Memory: checkpointer = InMemorySaver<br/>config thread_id<br/>returns: same history on the next invoke"]
        A9["1.6 HumanMessage content = text part + image part<br/>returns: answer about the image"]
        A10["1.7 Project: Personal Chef<br/>prompt + web_search + memory + multimodal<br/>returns: recipe answer"]
        A1 --> A2
        A2 --> A3
        A2 --> A4
        A2 --> A5
        A2 --> A6
        A6 --> A7
        A2 --> A8
        A3 --> A9
        A5 --> A10
        A7 --> A10
        A8 --> A10
        A9 --> A10
    end

    subgraph M2["MODULE 2: MCP, state, context, multi-agent"]
        direction TB
        B1["2.1 MultiServerMCPClient config<br/>await client.get_tools<br/>returns: list of tools from MCP servers"]
        B2["2.2 Runtime context: dataclass + context_schema<br/>agent.invoke context = Ctx<br/>tool reads runtime.context via ToolRuntime<br/>returns: read-only values the model cannot change"]
        B3["2.2 State: class CustomState extends AgentState<br/>tool returns Command update = ...<br/>tool reads runtime.state<br/>returns: values saved per thread"]
        B4["2.3 Multi-agent: each subagent wrapped in a @tool<br/>subagent.invoke messages<br/>returns: str back to the main agent"]
        B5["2.4 Project: Wedding Planner<br/>coordinator + travel, venue, playlist subagents<br/>WeddingState, update_state returns Command<br/>RetryMCPInterceptor retries failed MCP calls"]
        B6["Bonus RAG: PyPDFLoader.load, RecursiveCharacterTextSplitter,<br/>OpenAIEmbeddings, InMemoryVectorStore<br/>search_handbook tool returns: matching text str"]
        B7["Bonus SQL: SQLDatabase.from_uri, db.run query<br/>sql_query tool returns: result str"]
        B1 --> B5
        B2 --> B3
        B3 --> B5
        B4 --> B5
        B7 --> B5
    end

    subgraph M3["MODULE 3: Production agents"]
        direction TB
        C1["3.2 SummarizationMiddleware model, trigger, keep<br/>returns: old messages replaced by one summary"]
        C2["3.2 before_agent trim_messages state, runtime<br/>returns: dict with RemoveMessage list, or None<br/>RemoveMessage id = delete that message from state"]
        C3["3.3 HumanInTheLoopMiddleware interrupt_on<br/>returns: run pauses, response has __interrupt__"]
        C4["3.3 agent.invoke Command resume = decisions, same thread_id<br/>approve, reject with message, or edit with edited_action<br/>returns: run continues"]
        C5["3.4 wrap_model_call state_based_model<br/>request.override model = ...<br/>returns: handler request"]
        C6["3.4 dynamic_prompt<br/>returns: str system prompt built from context or state"]
        C7["3.4 wrap_model_call dynamic_tool_call<br/>request.override tools = ...<br/>returns: handler request"]
        C8["3.5 Project: Email Assistant<br/>EmailContext + AuthenticatedState<br/>authenticate returns Command<br/>check_inbox and send_email return str<br/>HITL on send_email"]
        C3 --> C4
        C1 --> C8
        C2 --> C8
        C4 --> C8
        C5 --> C8
        C6 --> C8
        C7 --> C8
    end

    START --> M1
    M1 --> M2
    M2 --> M3
    B6 --> M3

    M3 --> R1

    subgraph RUN["EVERY RUN OF A PRODUCTION AGENT"]
        direction TB
        R1["agent.invoke messages, context, config thread_id<br/>returns: response dict"]
        R2["Middleware runs first<br/>before_agent, dynamic_prompt, wrap_model_call<br/>can trim messages, change prompt, model or tools"]
        R3["Model call<br/>returns: AIMessage, maybe with a tool call"]
        R4{"Tool call?"}
        R5{"Tool needs approval?"}
        R6["Pause with __interrupt__<br/>human sends Command resume"]
        R7["Tool runs<br/>returns: str, or Command that updates state<br/>result saved as ToolMessage"]
        R8["Final AIMessage<br/>response messages -1 .content"]
        R1 --> R2
        R2 --> R3
        R3 --> R4
        R4 -- "yes" --> R5
        R4 -- "no" --> R8
        R5 -- "yes" --> R6
        R5 -- "no" --> R7
        R6 -- "approve or edit" --> R7
        R6 -- "reject" --> R3
        R7 --> R2
    end
```