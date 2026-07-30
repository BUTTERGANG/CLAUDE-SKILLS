---
name: multi-service-secrets-setup
description: Use when wiring up credentials for multiple external services (Resend, AgentMail, Google Drive, Neon, etc.) inside a Replit project. Naming convention, storage location, and scope discipline per service.
version: 1.0.0
---

# Multi-Service Secrets Setup

**Iron law: EVERY EXTERNAL SERVICE CREDENTIAL LIVES IN REPLIT SECRETS, NAMED BY SERVICE, NEVER IN CODE OR A COMMITTED FILE.**

## Usage

- `/multi-service-secrets-setup`
- Triggered when: integrating a new external service (email, storage, DB, auth provider) into a Replit project, or auditing an existing project's credential handling.

## Checklist

1. **One Secrets entry per credential, named `SERVICE_PURPOSE`** (e.g. `RESEND_API_KEY`, `AGENTMAIL_API_KEY`, `NEON_DATABASE_URL`, `GOOGLE_DRIVE_CLIENT_SECRET`) — avoid generic names like `API_KEY` once more than one service is in play; collisions cause silent misuse.
2. **Never prefix a server-only secret with `VITE_`** (or any client-bundle-exposing prefix) — see `vite-replit-config`. Resend/AgentMail/Drive/Neon credentials are always server-side only.
3. **Confirm `.gitignore` covers any local env file** (`.env`, `.env.local`) before the first commit of a project touching real credentials — see `secrets-hygiene`.
4. **Use least-privilege scopes where the service supports it** — e.g. Google Drive OAuth scoped to the specific folder/permission level needed, not full-account access, when the integration allows it.
5. **Distinguish sandbox/test credentials from production ones explicitly in the name** (`RESEND_API_KEY_TEST` vs `RESEND_API_KEY`) when a service provides both — prevents accidentally sending real emails from a dev branch.
6. **Rotate and update in one place.** When a key is rotated, confirm every Repl/deployment referencing it is updated — Replit Secrets are per-Repl, not automatically shared across forks/deployments of the same project.
7. **Document required secrets in a checked-in `.env.example`** (keys only, no values) so a fresh clone/fork knows what to provision without guessing.

## Failure scenarios this prevents

- Two services both get a Secrets entry named `API_KEY`, and a code change silently starts reading the wrong one.
- A Resend key gets `VITE_`-prefixed during quick setup and ends up readable in the browser bundle.
- A forked/duplicated Repl for testing silently uses production Resend/Drive credentials because Secrets weren't reviewed on fork.
- A new contributor has no way to know which env vars the project needs because none were documented.

## Guardrails

- This governs setup and naming discipline; actual leak prevention (staging, commits, uploads) is `secrets-hygiene`'s job — use both together.
- Don't over-engineer scope/rotation tooling for a single-developer prototype — apply proportional to how many people/environments touch the project.
