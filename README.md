# TripPilot — Multi-Agent Travel Assistant

A multi-agent travel assistant built with **Google Agent Development Kit (ADK)** and **Gemini**, using **RAG** over visa and travel-policy documents, deployed on **Google Cloud Run**.

## Architecture

```
User ──► FastAPI (Cloud Run) ──► Orchestrator Agent (Gemini, ReAct)
                                   ├── Flight Agent      → flight search tool (mock API / Amadeus test API)
                                   ├── Visa Agent        → RAG retriever over visa docs (ChromaDB)
                                   ├── Itinerary Agent   → builds day-by-day plan
                                   └── Critic Agent      → self-reflection: checks plan vs constraints + retrieved rules
                                 Session state (ADK session service) · Tracing (latency, tokens, cost)
```

## Features (each maps to a resume bullet — build all four)

1. **Hierarchical delegation** — Orchestrator routes sub-tasks to specialist sub-agents (ADK `sub_agents`), each using tools (ReAct loop).
2. **RAG pipeline** — Ingest visa/travel-policy PDFs → chunk → embed (Vertex AI `text-embedding` model) → store in ChromaDB → retrieve top-k with source citations.
3. **Self-reflection** — Critic agent reviews the draft answer against the user's budget/dates and the retrieved visa rules; sends it back for revision if it fails.
4. **Production bits** — FastAPI + Docker on Cloud Run, session state across turns, per-request logging of latency, input/output tokens, tokens/sec and estimated cost, plus `eval/` with ~20 test queries and expected answers.

## Tech stack
Python · Google ADK · Gemini (Vertex AI) · Vertex AI Embeddings · ChromaDB · FastAPI · Docker · Google Cloud Run


## Run locally
```bash
pip install -r requirements.txt
export GOOGLE_CLOUD_PROJECT=your-project
python ingest.py data/visa_docs/
uvicorn app.main:app --reload
```
