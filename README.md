# NavIC CBTMS Backend

This repository contains the server side of NavIC, a college bus tracking prototype. FastAPI provides the REST and WebSocket APIs; SQLAlchemy persists fleet and telemetry records; a newline-delimited JSON TCP receiver accepts device messages. A simulator is included for development because no IVTD hardware is connected, and simulator telemetry stays labelled as simulated.
---
## Requirements

Python 3.11+ and pip. SQLite is the local default; no external database service is required for a basic run.

## Install and run

Run these commands from the repository root:

```bash
python -m venv .venv
. .venv/bin/activate                 # Windows: .venv\Scripts\activate
python -m pip install -r backend/requirements.txt
cp .env.example .env                 # optional local configuration
python backend/run.py
```

The HTTP API listens on port `8000` by default, with health check at <http://localhost:8000/health> and API docs at <http://localhost:8000/docs>. The telemetry TCP receiver defaults to `127.0.0.1:5000`. Keep `.env` and all credentials private.

To provision an additional administrator, run this locally as an existing operator. With `--user-id`, the command prompts for a password and stores only a salted scrypt hash in the database. Do not pass credentials on the command line or commit them.

```bash
python -m navic_tcp.manage_users bootstrap-admin \
  --email admin2@example.edu --user-id admin2@example.edu --force
```

## Run simulated telemetry

In another terminal, from the repository root:

```bash
python -m navic_tcp.client_main --devices 6 --continuous
```

These generated frames are development data, not evidence about the real IVTD protocol or hardware.

## Tests and lint

```bash
pytest backend/tests tests
ruff check backend navic_tcp tests scripts
```

Use a disposable SQLite database when checking migrations. Schema changes must be additive and reversible; do not reset existing fleet or telemetry data.

## Main folders

- `backend/` — FastAPI app, SQLAlchemy models, migrations, and backend tests.
- `navic_tcp/` — TCP receiver, message validation, simulator, and shared backend services.
- `tests/` — protocol, startup, and integration coverage.
- `config/` — demo device registry.
- `docs/` — project decisions and technical notes.

The [complete application repository](https://github.com/CorbenZen/NavIC-complete) includes the React frontend and full-stack run instructions.
