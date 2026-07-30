---
name: vite-replit-config
description: Use when setting up or debugging a Vite dev server inside Replit — host/allowedHosts, HMR over Replit's proxy domain, and env var exposure differences from a normal local Vite setup.
version: 1.0.0
---

# Vite + Replit Config

**Iron law: VITE'S DEFAULT DEV-SERVER CONFIG ASSUMES A LOCAL BROWSER ON THE SAME MACHINE — REPLIT VIOLATES THAT ASSUMPTION.**

## Usage

- `/vite-replit-config`
- Triggered when: bootstrapping a Vite app inside Replit, seeing "Blocked request. This host is not allowed" in the webview, HMR not updating / repeatedly reconnecting, or env vars not reaching client code as expected.

## Checklist

1. **Set `server.host: true` (or `'0.0.0.0'`)** in `vite.config.*` — without it Vite dev server only listens on loopback, unreachable through Replit's proxy.
2. **Set `server.allowedHosts`** to include the Replit-provided domain (or `true`/`'all'` for dev convenience) — recent Vite versions reject requests with an unrecognized `Host` header by default, which is exactly what Replit's proxy sends.
3. **HMR over Replit's HTTPS proxy needs explicit config** — set `server.hmr.clientPort` (typically `443`) and confirm the HMR websocket isn't silently falling back to polling/failing; a broken HMR often still "runs," it just never reflects new edits, easy to mistake for a caching issue.
4. **Only `VITE_`-prefixed env vars reach client-side code.** A secret or config value set in Replit Secrets without that prefix will be `undefined` in browser code — this is a Vite convention, not a Replit-specific one, but it's the most common "why is my env var undefined in the frontend" cause.
5. **Never put server-only secrets behind a `VITE_` prefix** — anything with that prefix is bundled into client-shipped JS and publicly visible. Cross-check against `secrets-hygiene`.
6. **Confirm the dev server port matches what `replit-project-bootstrap`'s workflow config expects** — a Vite-specific port mismatch is a common subset of that broader issue.

## Failure scenarios this prevents

- Webview shows "Blocked request" even though the server process is running and logs show no errors.
- Edits to a component don't show up without a full manual refresh, misdiagnosed as a caching bug instead of a broken HMR websocket.
- A backend API key accidentally prefixed `VITE_API_KEY` ships inside the public JS bundle.

## Guardrails

- These settings are dev-only conveniences — don't carry `allowedHosts: true` or overly permissive HMR config into a production build config.
- Pairs with `replit-project-bootstrap` (general Replit reachability) and `secrets-hygiene` (credential exposure).
