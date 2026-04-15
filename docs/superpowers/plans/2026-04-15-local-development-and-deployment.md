# Local Development And Deployment Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a dedicated local development and deployment guide, then reduce `README.md` to a concise entry point that links to the canonical document.

**Architecture:** Keep one authoritative operational guide in `docs/local-development-and-deployment.md` and make `README.md` a summary layer. Reuse the existing commands, ports, and environment variables from the repository so the documentation matches the current runtime behavior instead of inventing a parallel workflow.

**Tech Stack:** Markdown docs, existing `README.md`, `.env.example`, Docker Compose, Python backend, Next.js frontend

---

## Chunk 1: Canonical Guide

### Task 1: Create the dedicated local development and deployment guide

**Files:**
- Create: `docs/local-development-and-deployment.md`
- Reference: `README.md`
- Reference: `.env.example`
- Reference: `docker-compose.yml`
- Reference: `docker-compose.dev.yml`
- Reference: `docker-compose.ghcr.yml`

- [ ] **Step 1: Draft the guide outline**

Include sections for:
- Scope and recommended paths
- Quick start
- Local development
- Docker deployment
- Common issues and troubleshooting
- Maintenance guidance

- [ ] **Step 2: Fill in the quick-start commands**

Document the shortest local path and the shortest Docker path using the existing repository commands:
- `pip install -e ".[server]"`
- `python -m deeptutor.api.run_server`
- `cd web && npm install`
- `cd web && npm run dev -- --port 3782`
- `docker compose -f docker-compose.ghcr.yml up -d`

- [ ] **Step 3: Add platform-specific local development notes**

Document the shared workflow once, then add focused notes for:
- Windows PowerShell environment variable syntax
- Windows UTF-8 or `gbk` console encoding issues
- macOS/Linux shell examples

- [ ] **Step 4: Add Docker deployment guidance**

Document:
- Official GHCR image path
- Build-from-source path
- Dev override path
- Port overrides
- Persistence directories
- Remote deployment behavior for `NEXT_PUBLIC_API_BASE_EXTERNAL`

- [ ] **Step 5: Add troubleshooting entries**

Write compact symptom-driven entries for:
- Python version mismatch
- Unicode or `gbk` backend failures on Windows
- Missing `NEXT_PUBLIC_API_BASE`
- Port conflicts on `8001` and `3782`
- Remote deployment using `localhost` incorrectly
- Frontend loads but API requests fail

- [ ] **Step 6: Review the guide for repository accuracy**

Run:
```bash
rg -n "local-development-and-deployment|NEXT_PUBLIC_API_BASE|NEXT_PUBLIC_API_BASE_EXTERNAL|8001|3782" docs/local-development-and-deployment.md
```

Expected:
- The new guide references the canonical env vars and the default ports.

## Chunk 2: README Summary

### Task 2: Refactor README into an entry point

**Files:**
- Modify: `README.md`
- Reference: `docs/local-development-and-deployment.md`

- [ ] **Step 1: Add a pointer to the dedicated guide**

Add a short note near the Get Started section linking to:
- `docs/local-development-and-deployment.md`

- [ ] **Step 2: Keep only the fast-path startup summary**

Retain:
- Setup Tour
- Minimal manual local start summary
- Minimal Docker start summary
- Default URLs and ports

Remove or compress:
- Repeated environment details that now live in the dedicated guide
- Repeated Docker operational details
- Large blocks that belong in the canonical guide

- [ ] **Step 3: Make README links and anchors consistent**

Ensure the README references the dedicated guide with a stable relative link and does not point readers to removed inline details.

- [ ] **Step 4: Review README for clarity and duplication**

Run:
```bash
rg -n "Option B|Option C|local-development-and-deployment|NEXT_PUBLIC_API_BASE_EXTERNAL|docker compose" README.md
```

Expected:
- README still exposes the main paths, but the detailed operations material is reduced and linked out.

## Chunk 3: Verification

### Task 3: Verify the documentation matches the current repository

**Files:**
- Verify: `docs/local-development-and-deployment.md`
- Verify: `README.md`
- Verify: `.env.example`
- Verify: `docker-compose.yml`
- Verify: `docker-compose.dev.yml`
- Verify: `docker-compose.ghcr.yml`

- [ ] **Step 1: Confirm the new files exist and are linked**

Run:
```bash
Get-Item docs/local-development-and-deployment.md
rg -n "local-development-and-deployment.md" README.md
```

Expected:
- The guide exists.
- README links to it.

- [ ] **Step 2: Confirm the documented commands still exist in the repository**

Run:
```bash
rg -n "python -m deeptutor.api.run_server|npm run dev -- -p 3782|docker compose -f docker-compose.ghcr.yml up -d|pip install -e \"\\.\\[server\\]\"" README.md docs/local-development-and-deployment.md
```

Expected:
- The commands referenced in the docs are present and use the current project conventions.

- [ ] **Step 3: Check git diff for scope**

Run:
```bash
git diff -- README.md docs/local-development-and-deployment.md docs/superpowers/specs/2026-04-15-local-development-and-deployment-design.md docs/superpowers/plans/2026-04-15-local-development-and-deployment.md
```

Expected:
- Changes are limited to the new guide, README summary, and the spec/plan artifacts.

- [ ] **Step 4: Commit**

Run:
```bash
git add README.md docs/local-development-and-deployment.md docs/superpowers/specs/2026-04-15-local-development-and-deployment-design.md docs/superpowers/plans/2026-04-15-local-development-and-deployment.md
git commit -m "docs: add local development and deployment guide"
```

Expected:
- A single docs-focused commit is created when the user wants commits.
