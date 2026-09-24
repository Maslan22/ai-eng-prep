# ai-eng-prep — Roadmap (Henry)

Hands-on AI-engineering build: a production-shaped **agent + RAG + eval** system in Python. It backs up the LLM work on [henry-iddirisu.dev](https://henry-iddirisu.dev) and gives me a codebase I can walk through line by line in interviews.

**Starting point:** adapted from [tp-coder/ai-eng-prep](https://github.com/tp-coder/ai-eng-prep). The phases below exist in the code. A phase is only ✅ once I have run it myself, broken it, and can explain why it works.

**Why this fits my profile:** I already run FastAPI + Postgres in production (DUBTEL AI) and integrate AI into products (voice agents, AI tutor). This repo adds the parts I want to own end to end: retrieval, tool-calling, evals, and MCP.

---

## Phase 0 — Run it locally ⬜

- `uv sync --extra dev`, copy `.env.example` → `.env`, add `OPENAI_API_KEY`
- `docker compose up -d` (Postgres + pgvector), then `uv run python -m app.ingest`
- `uv run python -m app.main "What is this project trying to build?"`
- Done when: a doc question returns a grounded answer with sources, and an off-topic question gets a refusal

## Phase 1 — Tool-calling agent ⬜

Retrieval flipped from always-on → **model-decided**.

- `retrieval.py` — `retrieve_context()` + helpers (one source of truth)
- `tools.py` — `SEARCH_DOCS_TOOL` (flat Responses-API schema) + `search_docs` / `execute_tool`
- `llm.py` — `complete_with_tools()` agent loop with a **MAX_TOOL_ITERATIONS=5** cap and `tool_calls=N` logging
- `main.py` — `--agent` flag
- Test: doc question → `tool_calls=1` (grounded + sources); general question → `tool_calls=0`

## Phase 2 — Second tool + selection ⬜

A distinct `calculator` tool so the agent must pick the **right** tool, not just tool-or-nothing.

## Phase 3 — Trajectory / agent eval ⬜

- `evals/agent_dataset.json` — math / doc / general / compound cases, each with `expected_tools`
- `evals/run_agent.py` — set-equality on tools called + precision / recall / accuracy, regression gate
- Record my own numbers here once it passes

## Phase 4 — MCP server ⬜

- `mcp_server.py` — `FastMCP` wrapping the same `search_docs` / `calculate` executors
- Verify in the MCP Inspector (`uv run mcp dev mcp_server.py`), then add to Claude Code (`claude mcp add`) and use it from a live session

## Phase 5 — Make it mine ⬜

- Swap the `data/docs` corpus for something from my own domain: e.g. notes on e-learning platform features, or API/billing runbooks written in the style I use at work (no employer-confidential material)
- Rewrite `evals/rag_dataset.json` against that corpus
- Write-up: article + project case study on henry-iddirisu.dev with my own eval numbers
