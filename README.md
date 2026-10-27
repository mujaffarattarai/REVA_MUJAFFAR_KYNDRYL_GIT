# Building AI Agents with LangChain and LangGraph: Session Notebooks

**Student Name:** Mujaffar Iqbal Attar

This repository contains all the notebooks covered during the sessions, from a first chat-model call up to a production-style email agent with authentication and human-in-the-loop approval. Every notebook includes:

- an **introduction** with the learning goals, student name and module details
- a **Prerequisites** cell listing the API keys and packages it needs
- a **Workflow** section with the numbered steps the notebook follows
- **Markdown explanations** before each step describing the workflow
- **code comments** explaining what each important line does and why
- a closing **Summary and Key Learnings** section

---

## Repository structure

```
.
├── README.md
├── requirements.txt
├── .env.example              # template for your API keys (copy to .env)
└── notebooks/
    ├── module-1/             # Agent foundations
    │   ├── resources/moon.png
    │   ├── 1.5_personal_chef.py + langgraph.json
    │   └── *.ipynb
    ├── module-2/             # Advanced agents
    │   ├── resources/        # MCP server, Chinook.db, employee handbook PDF
    │   └── *.ipynb
    └── module-3/             # Production patterns (middleware)
        ├── resources/Chinook.db
        ├── 3.5_email_agent.py + langgraph.json
        ├── email_agent_app/  # web UI for the email agent
        └── *.ipynb
```

---

## Notebook index

### Module 1: Agent Foundations

| # | Notebook | What it covers |
|---|----------|----------------|
| 1.1 | [Foundational Models](notebooks/module-1/1.1_foundational_models.ipynb) | `init_chat_model`, switching providers (OpenAI / Anthropic / Gemini), `create_agent`, multi-turn history, token streaming |
| 1.1 | [Prompting](notebooks/module-1/1.1_prompting.ipynb) | System prompts, few-shot examples, structured prompts, Pydantic structured output |
| 1.2 | [Tools](notebooks/module-1/1.2_tools.ipynb) | Defining tools with `@tool`, tool schemas from docstrings, inspecting `tool_calls` |
| 1.2 | [Web Search](notebooks/module-1/1.2_web_search.ipynb) | Knowledge cutoff vs. live Tavily web search as a tool |
| 1.3 | [Memory](notebooks/module-1/1.3_memory.ipynb) | Short-term memory with `InMemorySaver` and `thread_id` |
| 1.4 | [Multimodal Messages](notebooks/module-1/1.4_multimodal_messages.ipynb) | Text, image (base64) and audio content blocks |
| 1.5 | [**Project: Personal Chef**](notebooks/module-1/1.5_personal_chef.ipynb) | Tools + system prompt + memory -> leftover-ingredient recipe assistant |

### Module 2: Advanced Agents

| # | Notebook | What it covers |
|---|----------|----------------|
| 2.1 | [MCP](notebooks/module-2/2.1_mcp.ipynb) | Model Context Protocol: local `stdio` server (tools, resources, prompts) and the community time server |
| 2.1 | [Travel Agent](notebooks/module-2/2.1_travel_agent.ipynb) | Remote MCP server over `streamable_http` (Kiwi.com flight search) |
| 2.2 | [Runtime Context](notebooks/module-2/2.2_runtime_context.ipynb) | Read-only per-call context via `context_schema` and `ToolRuntime` |
| 2.2 | [State](notebooks/module-2/2.2_state.ipynb) | Custom `AgentState`, writing state with `Command`, reading `runtime.state` |
| 2.3 | [Multi-Agent](notebooks/module-2/2.3_multi_agent.ipynb) | Supervisor pattern with subagents wrapped as tools |
| 2.4 | [**Project: Wedding Planners**](notebooks/module-2/2.4_wedding_planners.ipynb) | Coordinator + travel/venue/playlist specialists, shared state, MCP retry interceptor |
| Bonus | [RAG](notebooks/module-2/bonus_rag.ipynb) | PDF -> chunks -> embeddings -> vector store -> retrieval tool |
| Bonus | [SQL Agent](notebooks/module-2/bonus_sql.ipynb) | Agent that discovers a SQLite schema and answers questions with SQL |

### Module 3: Production Patterns (Middleware)

| # | Notebook | What it covers |
|---|----------|----------------|
| 3.2 | [Managing Messages](notebooks/module-3/3.2_managing_messages.ipynb) | `SummarizationMiddleware`, trimming messages with `@before_agent` |
| 3.3 | [Human-in-the-Loop](notebooks/module-3/3.3_hitl.ipynb) | `HumanInTheLoopMiddleware`: approve / reject / edit tool calls |
| 3.4 | [Dynamic Models](notebooks/module-3/3.4_dynamic_models.ipynb) | Switching models per call with `@wrap_model_call` |
| 3.4 | [Dynamic Prompts](notebooks/module-3/3.4_dynamic_prompts.ipynb) | Building system prompts from runtime context with `@dynamic_prompt` |
| 3.4 | [Dynamic Tools](notebooks/module-3/3.4_dynamic_tools.ipynb) | Role-based tool access (internal vs. external users) |
| 3.5 | [**Project: Email Agent**](notebooks/module-3/3.5_email_agent.ipynb) | Authentication + dynamic tools/prompts + HITL send approval |

---

## Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/<your-username>/<repo-name>.git
   cd <repo-name>
   ```

2. **Create a virtual environment and install the dependencies** (Python 3.11+, developed on 3.13)
   ```bash
   python -m venv .venv
   source .venv/bin/activate        # Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```
   Or with `uv`: `uv venv && uv pip install -r requirements.txt`

3. **Add your API keys**
   ```bash
   cp .env.example .env             # then edit .env
   ```
   Each notebook's *Prerequisites* cell lists exactly which keys it needs. Most need only `OPENAI_API_KEY`, and the web-search notebooks also need `TAVILY_API_KEY`.

4. **Launch Jupyter from inside the module folder** so that relative paths such as `resources/Chinook.db` resolve correctly:
   ```bash
   cd notebooks/module-2
   jupyter lab
   ```

### Running the script versions

- `notebooks/module-1/1.5_personal_chef.py` and `notebooks/module-3/3.5_email_agent.py` can be served in LangGraph Studio:
  ```bash
  cd notebooks/module-3
  langgraph dev
  ```
- The email agent web UI is described in [`notebooks/module-3/email_agent_app/README.md`](notebooks/module-3/email_agent_app/README.md).

---

## Learning path at a glance

```
Model -> Prompt -> Tools -> Memory -> Multimodal      (Module 1)
   -> MCP -> Context & State -> Multi-agent -> RAG/SQL (Module 2)
   -> Middleware: summarise, HITL, dynamic model/prompt/tools (Module 3)
```

## Key learnings across the sessions

1. **An agent is a loop.** The model decides, tools act, results are fed back, and this repeats until the model gives a final answer. `create_agent` gives you this loop.
2. **Tools are the agent's interface to the world.** Clear names, type hints and docstrings directly improve how reliably the model picks a tool.
3. **Context and state are different.** Context is read-only per call (who the user is). State is mutable and persists across turns (what has happened so far).
4. **Checkpointers give memory** and are also what make human-in-the-loop pauses and resumes possible.
5. **MCP standardises integrations,** so tools, prompts and resources can be reused across agents and teams.
6. **Multi-agent systems** split complex tasks into focused specialists coordinated by a supervisor.
7. **Middleware is the production toolkit.** It lets you control cost (summarisation, model routing), safety (HITL, role-based tools) and personalisation (dynamic prompts) without rewriting the agent.

---

> **Note:** The `.env` file is git-ignored, so API keys are never committed. Notebook outputs have been cleared to keep the repository small and free of personal data. Run the notebooks to regenerate the outputs.
