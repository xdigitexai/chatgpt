# Xdigitex ChatGPT Gateway

A prototype bridge for triggering a published ChatGPT Workspace Agent and giving it controlled remote-computer/SSH tools.

## Architecture

Xdigitex client -> Gateway API -> ChatGPT Workspace Agent -> Xdigitex tool API -> SSH target -> result callback.

This project does **not** use an OpenAI Platform API key. The trigger client uses a ChatGPT Workspace Agent access token and API trigger ID.

## Features

- Asynchronous task creation and Workspace Agent triggering
- Stable conversation keys and idempotency
- Workspace Agent run-status polling
- Result callback endpoint
- Named SSH server profiles; credentials stay on the gateway
- SSH command execution, file read/write, environment inspection, HTTP verification
- Command safety policy and audit events
- API-key protection for gateway/tool endpoints
- SQLite task/event persistence
- Docker-ready deployment

## Quick start

```bash
cp .env.example .env
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --host 0.0.0.0 --port 8080
```

Add server profiles to `servers.json`. Prefer private-key authentication. Never commit real credentials.

Create a task:

```bash
curl -X POST http://localhost:8080/v1/tasks \
  -H "X-Gateway-Key: change-me" -H "Content-Type: application/json" \
  -d '{"instruction":"Inspect the app and fix its 500 error","server_id":"demo-vps","conversation_key":"demo-vps"}'
```

## ChatGPT Workspace Agent instructions

Use the contents of `workspace-agent-instructions.md` in the published Workspace Agent. Configure its API channel and connect the tool surface to this gateway.

## Security

The SSH layer deliberately blocks a small set of obviously destructive commands. That is a guardrail, not a sandbox. Run the gateway under a dedicated service identity, use least-privilege SSH users, restrict reachable hosts, rotate secrets, and require approval for production-destructive actions.
