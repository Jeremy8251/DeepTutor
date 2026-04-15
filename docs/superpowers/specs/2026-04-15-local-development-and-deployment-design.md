# Local Development And Deployment Design

Date: 2026-04-15
Project: DeepTutor
Status: Approved for planning

## Goal

Create a maintainable documentation structure for local development and deployment
that serves both new contributors and project maintainers.

The deliverable should:

- Help a new contributor get the project running quickly.
- Give maintainers a canonical reference for local development and deployment.
- Reduce README bloat by moving procedural detail into a dedicated document.
- Cover Windows, macOS, and Linux for local development, with emphasis on Windows
  and Linux.
- Cover Linux Docker deployment as the primary deployment path.
- Include a small, focused troubleshooting section for the most common failures.

## Audience

This documentation targets two groups:

- New contributors who need the fastest path to a running local environment.
- Current maintainers who need a reliable reference for development, deployment,
  environment variables, ports, persistence, and troubleshooting.

## Deliverables

Two documentation surfaces will be maintained together:

1. `README.md`
2. `docs/local-development-and-deployment.md`

### README Responsibilities

`README.md` remains the entry point and should contain only:

- A short pointer to the full guide.
- A minimal local quick-start path.
- A minimal Docker quick-start path.
- Default URLs and ports.
- A link to the dedicated guide.

The README should not duplicate full setup, deployment, or troubleshooting detail.

### Dedicated Guide Responsibilities

`docs/local-development-and-deployment.md` becomes the canonical operational guide
and should contain the full procedures for:

- Environment requirements.
- Local development setup and startup.
- Docker deployment.
- Environment variables.
- Common troubleshooting.
- Data, logs, and ports.

## Recommended Structure

The dedicated guide should follow this sequence:

1. Scope and recommendations
2. Quick start
3. Local development
4. Docker deployment
5. Common issues and troubleshooting
6. Maintenance guidance

### 1. Scope And Recommendations

This section explains:

- Who the guide is for.
- Recommended path for local development.
- Recommended path for deployment.
- Default backend and frontend ports.

### 2. Quick Start

This section provides the shortest successful path for a first-time setup:

- Recommended Python version: 3.11
- Required Node.js and npm availability
- `.env` setup
- Backend start command
- Frontend start command
- Docker quick-start alternative
- Access URLs

### 3. Local Development

This section is the main contributor workflow.

It should include:

- Shared prerequisites across platforms.
- Platform-specific notes for Windows, macOS, and Linux.
- Backend dependency installation.
- Frontend dependency installation.
- Environment file setup.
- Manual startup commands.
- Common day-to-day commands for development.

The section should avoid repeating the full same sequence three times. Shared steps
should be written once, with platform-specific differences called out inline or in
short subsections.

#### Required Windows Notes

The guide must explicitly cover these Windows-specific issues because they were
observed during local verification:

- Python 3.11 is preferred even if newer versions may partially work.
- Windows default `gbk` console encoding can break Unicode output in the backend.
- PowerShell environment variable syntax differs from bash syntax.
- The frontend must receive `NEXT_PUBLIC_API_BASE` when started manually.

Recommended examples should include PowerShell-safe commands.

### 4. Docker Deployment

This section focuses on Linux Docker deployment.

It should include:

- Pulling the official GHCR image.
- Building from source with `docker compose`.
- Required `.env` configuration.
- Default ports and how to override them.
- Volume-backed persistence.
- Remote deployment behavior for frontend-to-backend API routing.

This section must explain the role of:

- `NEXT_PUBLIC_API_BASE_EXTERNAL`
- `NEXT_PUBLIC_API_BASE`

Especially for remote servers, the guide should explain that the frontend runs in the
user's browser, so `localhost` refers to the browser host rather than the Docker host.

### 5. Common Issues And Troubleshooting

This section should be intentionally limited to the most common operational failures.

Target list:

- Python version mismatch.
- Windows Unicode or `gbk` encoding failures.
- Missing `NEXT_PUBLIC_API_BASE`.
- Port conflicts on `8001` and `3782`.
- Remote deployment accidentally pointing the browser to local `localhost`.
- Frontend starts but backend requests fail.
- Service starts but page does not load as expected.

Each issue should use a compact format:

- Symptom
- Likely cause
- Fix

### 6. Maintenance Guidance

This section should define the documentation ownership model:

- `README.md` stays concise.
- Detailed setup and deployment procedures live in the dedicated guide.
- When ports, commands, env vars, or deployment behavior change, update the guide
  first and then verify the README summary still matches.

## Writing Style

The documentation should follow these writing rules:

- Lead with the recommended path before listing alternatives.
- Give short context before command blocks.
- Use directly executable commands.
- Prefer one canonical command per situation rather than too many options.
- Keep troubleshooting concrete and symptom-driven.
- Avoid duplicating the same instructions in multiple places.

## Constraints

- Do not expand the README into a full operations manual.
- Do not create separate platform guides unless complexity forces it later.
- Do not include low-frequency troubleshooting that is not grounded in current setup
  behavior.
- Do not rely on hidden assumptions about environment variables; state required ones
  explicitly.

## Implementation Notes

The documentation work should reflect the current project state:

- Backend is started manually with `python -m deeptutor.api.run_server`.
- Frontend is started manually from `web/` with `npm run dev -- --port 3782`.
- Docker deployment is available through `docker-compose.yml`,
  `docker-compose.dev.yml`, and `docker-compose.ghcr.yml`.
- The project currently recommends Python 3.11 in the existing README.

## Open Operational Notes

The design assumes:

- The repository will keep the dedicated guide at
  `docs/local-development-and-deployment.md`.
- README changes will be limited to a concise summary and a link to the guide.

If maintainers later want a docs index page, this guide can be linked from there
without changing its role as the canonical setup and deployment reference.
