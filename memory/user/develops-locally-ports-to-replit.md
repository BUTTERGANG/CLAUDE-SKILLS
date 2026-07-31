---
name: develops-locally-ports-to-replit
description: Work is often built in Claude Code outside Replit first and ported into Replit afterwards, so Replit-specific assumptions have to be designed in up front rather than discovered on arrival.
metadata:
  type: user
---

Development frequently happens in Claude Code on a non-Replit machine, with the result
ported into Replit later. The code is therefore usually written somewhere that Replit's
constraints do not apply, and only meets them after the fact.

**Why:** Stated directly on 2026-07-31. It explains a recurring class of breakage — code
that ran correctly where it was written and failed on arrival in Replit, for environment
reasons rather than logic ones.

**How to apply:** When writing code destined for Replit, apply the environment skills at
authoring time, not at porting time:

- Bind servers to `0.0.0.0` and read the port from `process.env.PORT` from the first
  commit — see [[replit-project-bootstrap]].
- Set Vite's `server.host` and `server.allowedHosts` up front, and keep server-only
  secrets off the `VITE_` prefix — see [[vite-replit-config]].
- Reference credentials via `process.env` with service-scoped names, and keep a checked-in
  `.env.example` so the Replit side knows what to provision — see
  [[multi-service-secrets-setup]] and [[secrets-hygiene]].
- Point frontends at a configurable API base URL, never a hardcoded `localhost:PORT`.

"It works here" is not evidence it will work in Replit; the proxy layer is the difference.
Confirm reachability through Replit itself after porting — see
[[verification-before-completion]].
