# Project Atlas — Report Copilot (MVP)

A take-home assignment MVP for a market-research report copilot. It includes a FastAPI service, a synthetic six-report corpus, entitlement-aware retrieval, MySQL persistence, a Chroma option, a deterministic answer composer, evaluation prompts, Docker Compose configuration and a CodeIgniter 3 integration scaffold.

> **Status / honesty note:** The API, MySQL connectivity, ingestion, entitlement checks, prompt-injection refusal and missing-API-key rejection were manually exercised during development. The full 25-question evaluation has not yet been run. Citation deduplication/metadata cleanup remains an identified issue. The PHP files are a CI3 integration scaffold, not a complete distributable CI3 installation. Do not describe untested features as passed.

## Features

- `POST /chat`, `POST /ingest`, `GET /escalations`, `GET /health`
- API-key authentication via `X-API-Key`
- Six synthetic Markdown reports, including a conflicting-source scenario and a prompt-injection trap
- Report entitlement checks before report content is passed to the answer composer
- MySQL tables for reports, clients, purchases, conversations, escalation tickets and security events
- Chroma vector-store mode and local TF-IDF fallback
- Deterministic routing for factual lookup, comparison, calculation, out-of-scope and unsafe requests
- Example calculator and fixed aggregate SQL query
- PHP/CodeIgniter 3 controller, library and view scaffold
- 25-question evaluation dataset and evaluation harness

## Architecture

```text
Reviewer / CI3 chat view
          |
          v
CI3 controller + API library (scaffold)
          |
          v
FastAPI API-key authentication + request validation
          |
          v
Router / safety checks
          |
          +---- entitlement lookup in MySQL
          |             |
          |             v
          |      allowed report IDs only
          |             |
          |             v
          |      Chroma / TF-IDF retrieval
          |
          +---- fixed aggregate SQL query
          +---- calculator
          |
          v
Answer composer + citations / confidence / escalation
          |
          v
MySQL conversation and escalation records
```

## Requirements

- Docker Desktop with Linux containers, or Docker Engine + Compose
- Git recommended
- For running Python tests locally: Python 3.11+ recommended

## Docker quick start

1. Extract this project and open a terminal in the project directory.
2. Copy `.env.example` to `.env`.
3. Set a unique `API_KEY` and strong local MySQL passwords in `.env`. Never commit `.env`.
4. Start services:

   ```bash
   docker compose up --build -d
   docker compose ps
   ```

5. Verify API health at `http://localhost:8003/health`, open Swagger at `http://localhost:8003/docs`, and open the Streamlit UI at `http://localhost:8501`. The UI asks for the API key from your local `.env` file.
6. Run ingestion from Swagger (`POST /ingest`) with the `X-API-Key` header if needed. The API may also ingest on startup.
7. Stop services with `docker compose down`. Use `docker compose down -v` only if you intentionally want to delete local database/vector volumes.

If host port 3306 is already occupied, map the host to a different port (for example `3307:3306`) while keeping the internal Docker `DATABASE_URL` host and port as `mysql:3306`.

## Web UI

The Streamlit web interface is included. Start it with `docker compose up --build -d`, then open `http://localhost:8501`. Enter the `API_KEY` from your local `.env`, select a demo client, and ask a question. The API still performs authorization; the UI is not an access-control boundary.

## Example API request

```bash
curl -X POST http://localhost:8003/chat \
  -H 'Content-Type: application/json' \
  -H 'X-API-Key: YOUR_LOCAL_API_KEY' \
  -d '{"client_id":"C-01","question":"What was the global smart water meter market size in 2024?"}'
```

Example entitlement check for client C-02:

```json
{
  "client_id": "C-02",
  "question": "Tell me the market size and CAGR from report R-101."
}
```

C-02's seeded purchase list contains R-103 only. A response must not include R-101 figures or excerpts. A successful HTTP status alone does not prove the security check passed; inspect the answer and citations.

## Endpoints

| Method | Path | Purpose | Authentication |
|---|---|---|---|
| GET | `/health` | API and database health | No API key in current implementation |
| POST | `/chat` | Answer a client question | `X-API-Key` |
| POST | `/ingest` | Re-index report chunks | `X-API-Key` |
| GET | `/escalations` | List recent escalation tickets | `X-API-Key` |

## Environment variables

Copy `.env.example` to `.env` and review every value. Common settings include `API_KEY`, `DATABASE_URL`, MySQL credentials, `CHROMA_URL`, `VECTOR_MODE`, `LLM_MODE`, `OPENAI_API_KEY`, `OPENAI_MODEL` and the confidence threshold. Use secrets only in local environment configuration or an approved secret manager. The deterministic mode does not mean a hosted LLM is running.

## Test and evaluation

Run local tests in an activated virtual environment:

```bash
pip install -r requirements.txt
pytest -q
python evaluation/run_eval.py
```

Check the output before claiming a pass. The 25 prompts in `evaluation/questions.json` are a test set, not evidence that all tests passed. Record observed results in `evaluation/REPORT.md`. See `ONE_MORE_WEEK.md` for the proposed next-week plan and `docs/DEMO_SCRIPT.md` for a demo outline.

## Security and known limitations

- Entitlement checks should restrict retrieval before document text is made available to the answer composer. Automated regression tests are still needed for every client/report combination.
- Prompt-injection detection uses patterns and is not a complete defense against every possible attack.
- SQL aggregation should use fixed, parameterized read-only query paths; a production deployment should use a dedicated least-privilege database user, query limits and timeouts.
- Current manual testing found duplicate citations and metadata appearing in answer prose. This should be fixed before claiming citation quality is complete.
- The evaluation report documents the manual checks performed and known gaps; no unmeasured accuracy score is claimed.
- Review timeout handling, rate limiting, monitoring, secret rotation, tenant isolation and production database permissions before deployment.
- The PHP/CI3 files must be copied into a genuine CodeIgniter 3 installation. Do not claim CI3 integration has passed until the portal is run and demonstrated.

## Submission checklist

- [ ] Run the full 25-question evaluation and update results honestly.
- [ ] Verify source conflict and derived-value behaviour.
- [ ] Verify low-confidence escalation creates a ticket.
- [ ] Verify CI3 rendering, CSRF, escaping, feedback persistence and API timeout fallback.
- [ ] Replace all local/example secrets and ensure `.env` is not committed.
- [ ] Review the AI disclosure and state only tools/tasks actually used.
- [ ] Make visible, reviewable Git commits.
- [ ] Record a 3–5 minute demo showing normal answer, conflict, injection, entitlement denial, escalation and CI3 page.
- [ ] Share repository and demo links with the recruiter.
