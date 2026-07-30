---
name: replit-project-bootstrap
description: Use when setting up a new app inside the Replit environment or diagnosing "it works locally but not in Replit." Covers port/host binding, workflow config, and confirming the app is actually reachable.
version: 1.0.0
---

# Replit Project Bootstrap

**Iron law: DO NOT CLAIM THE APP RUNS UNTIL IT IS REACHABLE THROUGH REPLIT'S PROXY, NOT JUST "STARTED WITHOUT ERROR."**

## Usage

- `/replit-project-bootstrap`
- Triggered when: starting a new project inside Replit, an app fails to load in the Replit webview despite the process starting, or moving a project that ran fine elsewhere into Replit.

## Checklist

1. **Bind to `0.0.0.0`, not `localhost`/`127.0.0.1`.** Replit's proxy cannot reach a server bound only to loopback — this is the single most common "server started but webview is blank" cause.
2. **Read the port from `process.env.PORT`**, don't hardcode a port. Replit assigns/exposes a specific port; hardcoding one that doesn't match means the proxy can't route to it.
3. **Configure the run command in `.replit` / the workflow config**, not just a local script — confirm the actual command Replit executes matches what you tested (`run =` field or Workflows UI), including for multi-process setups (frontend + backend).
4. **Secrets go in Replit's Secrets pane, not a committed `.env`.** Set them there, reference via `process.env`, and confirm `.env`/`.env.local` are gitignored (see `secrets-hygiene`).
5. **After startup, verify reachability directly** — open the webview or curl the exposed URL, don't infer "it's up" from clean stdout alone (see `verification-before-completion`).
6. **For multi-service apps** (separate frontend/backend), confirm both are configured to run concurrently in the workflow, and that the frontend's API base URL points at the correct Replit-exposed address, not `localhost:PORT`.

## Failure scenarios this prevents

- A server binds to `localhost` and starts cleanly in logs, but the Replit webview shows nothing — mistaken for a build error when it's a binding issue.
- A hardcoded port (e.g. `3000`) doesn't match what Replit's proxy expects, so requests silently never reach the process.
- A frontend calls `http://localhost:8080/api` for its backend, working only when both happen to run in the same container context locally, breaking once deployed.

## Guardrails

- Don't assume Replit behaves like a standard local dev environment — the proxy layer is the source of most "works everywhere except here" issues.
- Pairs with `vite-replit-config` for Vite-specific dev-server settings, and `secrets-hygiene` for credential handling.
