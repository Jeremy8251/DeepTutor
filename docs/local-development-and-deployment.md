# Local Development And Deployment

This guide is the canonical reference for running DeepTutor locally and deploying it
with Docker. It is written for both first-time contributors and maintainers.

Use this guide when you need:

- The fastest way to get DeepTutor running locally
- A reliable manual development workflow
- A Linux Docker deployment path
- The correct environment variables, ports, and troubleshooting steps

## Scope And Recommended Paths

Recommended paths by scenario:

| Scenario | Recommended path |
|:--|:--|
| First local setup | Run `python scripts/start_tour.py` |
| Manual local development | Python backend + Next.js frontend |
| Remote or server deployment | `docker compose -f docker-compose.ghcr.yml up -d` |
| Hot-reload Docker development | `docker compose -f docker-compose.yml -f docker-compose.dev.yml up` |

Default ports:

- Backend API: `8001`
- Frontend UI: `3782`

Default URLs:

- Frontend: `http://localhost:3782`
- Backend docs: `http://localhost:8001/docs`

## Quick Start

### Option A: Guided Setup

This is the shortest path for a first run.

```bash
git clone https://github.com/HKUDS/DeepTutor.git
cd DeepTutor
python scripts/start_tour.py
```

The setup tour installs dependencies, helps configure providers, and launches the app.

### Option B: Manual Local Start

Recommended versions:

- Python: `3.11`
- Node.js: current LTS or newer
- npm: version bundled with your Node.js install

1. Create and activate a Python environment.

Windows PowerShell:
```powershell
conda create -n deeptutor python=3.11
conda activate deeptutor
```

macOS/Linux:
```bash
python3.11 -m venv .venv
source .venv/bin/activate
```

2. Install backend and frontend dependencies.

```bash
pip install -e ".[server]"
cd web
npm install
cd ..
```

3. Create the root environment file.

Windows PowerShell:
```powershell
Copy-Item .env.example .env
```

macOS/Linux:
```bash
cp .env.example .env
```

4. Fill in the required values in `.env`.

```dotenv
LLM_BINDING=openai
LLM_MODEL=gpt-4o-mini
LLM_API_KEY=sk-xxx
LLM_HOST=https://api.openai.com/v1

EMBEDDING_BINDING=openai
EMBEDDING_MODEL=text-embedding-3-large
EMBEDDING_API_KEY=sk-xxx
EMBEDDING_HOST=https://api.openai.com/v1
EMBEDDING_DIMENSION=3072
```

5. Start the backend.

Windows PowerShell:
```powershell
python -X utf8 -m deeptutor.api.run_server
```

macOS/Linux:
```bash
python -m deeptutor.api.run_server
```

6. Start the frontend in a second terminal.

Windows PowerShell:
```powershell
Set-Location web
$env:NEXT_PUBLIC_API_BASE='http://localhost:8001'
npm run dev -- --port 3782
```

macOS/Linux:
```bash
cd web
NEXT_PUBLIC_API_BASE=http://localhost:8001 npm run dev -- --port 3782
```

Open `http://localhost:3782`.

### Option C: Docker Quick Start

1. Create `.env` from `.env.example` and fill in the required provider values.
2. Start the official image:

```bash
docker compose -f docker-compose.ghcr.yml up -d
```

3. Open `http://localhost:3782`.

## Local Development

### Environment Requirements

Minimum practical requirements:

- Python `3.11` recommended
- Node.js installed
- npm installed
- One configured LLM provider
- One configured embedding provider for knowledge base features

Optional but commonly useful:

- A search provider configuration
- Docker Desktop or Docker Engine for containerized development

### Required Environment Variables

Required in `.env`:

| Variable | Required | Purpose |
|:--|:--:|:--|
| `LLM_BINDING` | Yes | LLM provider name |
| `LLM_MODEL` | Yes | LLM model name |
| `LLM_API_KEY` | Yes | LLM API key |
| `LLM_HOST` | Yes | LLM base URL |
| `EMBEDDING_BINDING` | Yes | Embedding provider name |
| `EMBEDDING_MODEL` | Yes | Embedding model name |
| `EMBEDDING_API_KEY` | Yes | Embedding API key |
| `EMBEDDING_HOST` | Yes | Embedding base URL |
| `EMBEDDING_DIMENSION` | Yes | Embedding vector dimension |

Common optional values:

| Variable | Required | Purpose |
|:--|:--:|:--|
| `SEARCH_PROVIDER` | No | Search provider such as `brave`, `tavily`, `jina`, `searxng`, `duckduckgo`, `perplexity` |
| `SEARCH_API_KEY` | No | Unified search provider API key |
| `SEARCH_BASE_URL` | No | Custom search base URL |
| `BACKEND_PORT` | No | Backend port, default `8001` |
| `FRONTEND_PORT` | No | Frontend port, default `3782` |
| `NEXT_PUBLIC_API_BASE_EXTERNAL` | No | Public backend URL for remote browser access |
| `NEXT_PUBLIC_API_BASE` | No | Direct frontend API base override |
| `DISABLE_SSL_VERIFY` | No | Keep `false` in production |

Provider-specific search keys such as `BRAVE_API_KEY`, `TAVILY_API_KEY`,
`JINA_API_KEY`, and `PERPLEXITY_API_KEY` are also supported.

### Manual Development Workflow

Use two terminals:

- Terminal 1: backend
- Terminal 2: frontend

Backend:

Windows PowerShell:
```powershell
python -X utf8 -m deeptutor.api.run_server
```

macOS/Linux:
```bash
python -m deeptutor.api.run_server
```

Frontend:

Windows PowerShell:
```powershell
Set-Location web
$env:NEXT_PUBLIC_API_BASE='http://localhost:8001'
npm run dev -- --port 3782
```

macOS/Linux:
```bash
cd web
NEXT_PUBLIC_API_BASE=http://localhost:8001 npm run dev -- --port 3782
```

Verify:

- Frontend: `http://localhost:3782`
- Backend docs: `http://localhost:8001/docs`

### Frontend API Base Notes

The frontend requires `NEXT_PUBLIC_API_BASE`.

You have two practical ways to provide it for local development:

1. Export it inline before `npm run dev`
2. Put it into `web/.env.local`

Example `web/.env.local`:

```dotenv
NEXT_PUBLIC_API_BASE=http://localhost:8001
```

The setup launcher may generate this file automatically. If you start the frontend
manually and it reports that `NEXT_PUBLIC_API_BASE` is missing, fix this first.

### Common Day-To-Day Commands

Backend server:

```bash
python -m deeptutor.api.run_server
```

Frontend dev server:

```bash
cd web && npm run dev -- --port 3782
```

Python package install:

```bash
pip install -e ".[server]"
```

Docker dev mode:

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml up
```

### Platform Notes

#### Windows

- Prefer Python `3.11` even if other versions appear to work.
- If backend logs fail on Unicode output, run the backend with `python -X utf8`.
- In PowerShell, environment variables use `$env:NAME='value'`, not `NAME=value`.
- If you background processes yourself, keep logs under `.runtime/` so local debugging
  is easier to inspect.

#### macOS And Linux

- Standard shell export syntax works for frontend startup.
- `python3.11` may be the safest interpreter name when multiple Python versions are installed.

## Docker Deployment

### Option A: Official GHCR Image

This is the recommended deployment path for Linux servers.

1. Prepare `.env`.
2. Start the prebuilt image:

```bash
docker compose -f docker-compose.ghcr.yml up -d
```

3. Check logs if needed:

```bash
docker compose -f docker-compose.ghcr.yml logs -f
```

### Option B: Build From Source

Use this when you need a local image built from the current checkout.

```bash
docker compose up -d
```

### Option C: Docker Dev Override

Use this for hot-reload with source mounts:

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml up
```

This mounts:

- `deeptutor/`
- `deeptutor_cli/`
- `scripts/`
- key frontend source directories under `web/`

### Remote Deployment And API Routing

This is the most important deployment detail.

The frontend runs in the user's browser. That means `localhost` is interpreted on the
machine running the browser, not the server running Docker.

Local Docker on your own machine:

- Leave `NEXT_PUBLIC_API_BASE_EXTERNAL` blank
- The default `http://localhost:8001` works through Docker port mapping

Remote Docker on a server:

- Set `NEXT_PUBLIC_API_BASE_EXTERNAL` to the server's public backend URL

Example:

```dotenv
NEXT_PUBLIC_API_BASE_EXTERNAL=http://203.0.113.10:8001
```

You can also provide `NEXT_PUBLIC_API_BASE`, but the main remote deployment case should
normally use `NEXT_PUBLIC_API_BASE_EXTERNAL`.

### Custom Ports

Set these in `.env`:

```dotenv
BACKEND_PORT=9001
FRONTEND_PORT=4000
```

Then restart:

```bash
docker compose up -d
```

Or:

```bash
docker compose -f docker-compose.ghcr.yml up -d
```

### Persistence

Docker persists user state and knowledge bases through bind mounts:

| Container path | Host path | Purpose |
|:--|:--|:--|
| `/app/data/user` | `./data/user` | settings, memory, workspace, sessions, logs |
| `/app/data/knowledge_bases` | `./data/knowledge_bases` | uploaded files and vector indices |

These directories survive `docker compose down`.

## Common Issues And Troubleshooting

### 1. Backend fails with Unicode or `gbk` errors on Windows

Symptom:

- Backend crashes on Unicode output
- Errors mention `gbk` or `UnicodeEncodeError`

Likely cause:

- PowerShell or the Python process is using the default Windows console encoding

Fix:

- Start the backend with:

```powershell
python -X utf8 -m deeptutor.api.run_server
```

If needed, also set:

```powershell
$env:PYTHONUTF8='1'
```

### 2. Frontend says `NEXT_PUBLIC_API_BASE` is not configured

Symptom:

- The page fails during startup
- Browser console logs show `NEXT_PUBLIC_API_BASE is not configured`

Likely cause:

- The frontend was started manually without the API base env var

Fix:

- Export the variable before `npm run dev`, or create `web/.env.local` with:

```dotenv
NEXT_PUBLIC_API_BASE=http://localhost:8001
```

### 3. `8001` or `3782` is already in use

Symptom:

- Backend or frontend refuses to start
- The process reports port conflicts

Likely cause:

- Another local process is already bound to the default port

Fix:

- Stop the conflicting process, or change the port
- For Docker, update `BACKEND_PORT` or `FRONTEND_PORT` in `.env`
- For manual frontend start, pass a different `--port`

### 4. Remote deployment loads the UI but API calls fail

Symptom:

- Frontend loads on a remote server
- Requests or WebSocket connections fail in the browser

Likely cause:

- The frontend is trying to call `localhost:8001` on the user's machine

Fix:

- Set `NEXT_PUBLIC_API_BASE_EXTERNAL` to the public backend URL
- Restart the container after updating `.env`

### 5. Backend starts, but the browser still cannot use the app

Symptom:

- `http://localhost:8001/docs` works
- `http://localhost:3782` loads partially or fails to interact with the backend

Likely cause:

- Frontend API base configuration does not match the backend address

Fix:

- Confirm `NEXT_PUBLIC_API_BASE=http://localhost:8001` for manual local runs
- Confirm `NEXT_PUBLIC_API_BASE_EXTERNAL` is correct for remote deployments

### 6. Python environment installs, but runtime behavior is inconsistent

Symptom:

- Imports work, but startup or optional paths behave unexpectedly

Likely cause:

- A newer Python version is in use instead of the recommended `3.11`

Fix:

- Recreate the environment with Python `3.11`
- Reinstall with:

```bash
pip install -e ".[server]"
```

## Maintenance Guidance

Use this file as the source of truth for setup and deployment.

When commands, ports, environment variables, or Docker behavior change:

1. Update this guide first
2. Update the `README.md` summary second
3. Verify both still point to the same startup paths

Avoid duplicating full operational detail in `README.md`. Keep `README.md` as the
entry point and keep this guide as the canonical reference.
