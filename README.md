# LinguaWalk

**A stateful language-learning backend for a Custom GPT.**

LinguaWalk connects a conversational language tutor to a FastAPI service that manages learning sessions, vocabulary and grammar observations, spaced review, and long-term progress. The GPT handles conversation and reports learning events; the backend owns the durable state and deterministic learning rules.

> **Scope:** This repository implements the backend and a Custom GPT Actions interface. It does not train or host a language model, and it is not an autonomous multi-agent framework.

## Features

- **GPT Actions integration:** OpenAPI schema for a Custom GPT to call authenticated backend endpoints.
- **Session lifecycle:** Start and end learning sessions and record their context.
- **Learning memory:** Persist learner profiles, vocabulary, grammar, and session history in SQLite.
- **Adaptive review:** Select due learning targets based on current mastery, mistakes, and scheduled review intervals.
- **Progress tracking:** Expose profile and progress summaries through REST endpoints.
- **Consolidation:** Generate weekly and monthly summaries while retaining historical learning evidence.
- **Deployment:** Docker image and example Kubernetes/K3s manifests.
- **Tests:** Automated tests for API behavior, sessions, learning rules, and consolidation.

## Architecture

```text
Custom GPT (conversation and observations)
              |
         HTTPS / OpenAPI Actions
              |
         FastAPI backend
              |
   Learning rules and session logic
              |
           SQLite
```

The GPT proposes observations; the backend calculates IDs, timestamps, mastery, review schedules, counters, and summaries. See [ARCHITECTURE.md](ARCHITECTURE.md) for details.

## Tech stack

Python · FastAPI · SQLite · OpenAPI · Docker · Kubernetes/K3s · pytest

## Quick start

Requirements: Python 3 and a virtual environment.

```bash
cd app
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export LINGUAWALK_API_KEY="$(python3 -c 'import secrets; print(secrets.token_urlsafe(32))')"
python db.py
uvicorn app:app --reload --host 127.0.0.1 --port 8000
```

In a second terminal:

```bash
curl http://127.0.0.1:8000/health
```

Interactive API documentation: `http://127.0.0.1:8000/docs`.

The API uses a bearer token for protected endpoints. Use the generated `LINGUAWALK_API_KEY` value in the `Authorization: Bearer ...` header; do not commit real credentials.

## Tests

From the `app/` directory, after installing dependencies:

```bash
python -m pytest
```

## Repository guide

| Path | Purpose |
| --- | --- |
| [`app/`](app/) | FastAPI source, SQLite schema, tests, and container configuration |
| [`gpt-action.openapi.json`](gpt-action.openapi.json) | Custom GPT Actions API specification |
| [`GPT_ACTION.md`](GPT_ACTION.md) | GPT integration notes |
| [`ARCHITECTURE.md`](ARCHITECTURE.md) | System boundaries and persistence design |
| [`LEARNING_RULES.md`](LEARNING_RULES.md) | Review and learning-state rules |
| [`k8s/`](k8s/) | Example Kubernetes deployment resources |

See [`app/README.md`](app/README.md) for endpoint details and [`k8s/README.md`](k8s/README.md) for deployment notes.

## Security and deployment notes

This repository is intended as a portfolio-friendly reference implementation. Set your own API key, use HTTPS when exposing the API, and review Kubernetes manifests and environment variables before deployment. Never commit `.env` files, live databases, tokens, or real infrastructure secrets. Documentation-only addresses in the `192.0.2.0/24` range are examples, not reachable deployment targets.

## License

MIT — see [LICENSE](LICENSE).
