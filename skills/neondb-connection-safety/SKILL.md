---
name: neondb-connection-safety
description: Use when connecting an app to Neon Postgres or running schema migrations against it — pooled vs direct connection strings, branch-per-environment discipline, and migration safety.
version: 1.0.0
---

# NeonDB Connection Safety

**Iron law: KNOW WHICH CONNECTION STRING YOU'RE USING AND WHICH BRANCH IT POINTS AT BEFORE RUNNING ANYTHING DESTRUCTIVE.**

## Usage

- `/neondb-connection-safety`
- Triggered when: wiring up a Neon connection for the first time, debugging serverless connection errors (`too many connections`, timeouts), or running any migration/schema change against a Neon database.

## Checklist

1. **Use the pooled connection string for app runtime traffic**, especially serverless/edge functions — Neon's direct connection has a low connection limit that serverless invocation patterns exhaust quickly. Neon selects pooling by **hostname, not port**: add `-pooler` to the endpoint ID (`ep-cool-darkness-123456-pooler.us-east-2.aws.neon.tech`) and keep the standard Postgres port. There is no separate pooler port — `6543` is the Supabase/PgBouncer convention and does not apply here.
2. **Use the direct (unpooled) connection string for migrations and long-lived transactions** — pooled connections in transaction mode don't support session-level features some migration tools rely on (prepared statements, session variables, advisory locks).
3. **Confirm which branch the connection string targets before running a migration.** Neon branches are cheap to create — verify you're pointed at a dev/preview branch, not `main`/production, especially after copy-pasting a connection string from Replit Secrets.
4. **For schema changes, follow additive-first migration discipline**: add nullable/defaulted columns before backfilling, avoid destructive `DROP`/`ALTER ... NOT NULL` in the same deploy as the code that depends on it — ties into `batch-job-safety`'s reversibility check.
5. **Store connection strings only in Replit Secrets**, never committed — see `secrets-hygiene`. Neon connection strings embed credentials directly in the URL.
6. **Watch for cold-start latency on Neon's autosuspend** — a "connection timeout" on first request after idle may be the compute waking up, not a real failure; don't misdiagnose as a code bug.

## Failure scenarios this prevents

- A serverless function pattern (one connection per invocation) exhausts Neon's direct connection limit under moderate traffic because the pooled endpoint wasn't used.
- A migration is run against what turns out to be the production branch because the connection string was copied from the wrong Secrets entry.
- A `NOT NULL` column added in the same migration as new code deploys, breaking any in-flight request still running old code against the new schema.

## Guardrails

- Treat any migration against a branch named `main`/`production` as requiring the `destructive-action-confirmation` checklist, even if the SQL itself looks additive.
- Don't assume Neon behaves identically to a traditional always-on Postgres instance — autosuspend and pooling behavior are structurally different.
