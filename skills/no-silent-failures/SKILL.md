---
name: no-silent-failures
description: Use when writing or reviewing code that catches errors, falls back to a default, retries, or truncates output. Prevents failures from being swallowed and reported as success.
version: 1.0.0
---

# No Silent Failures

**Iron law: A CAUGHT ERROR, A FALLBACK, OR A TRUNCATED RESULT MUST BE VISIBLE, NOT ABSORBED.**

## Usage

- `/no-silent-failures`
- Triggered when writing/reviewing: try/catch blocks, `|| default`, retry loops, `.slice(0, N)` / `head -N` style truncation, "best effort" logic, or any code path that can produce a partial or degraded result without raising.

## Checklist

1. **Every catch block either re-raises, logs at a visible level, or returns an explicit error value** — never a silent `pass`, empty catch, or swallowed exception.
2. **Every fallback value is logged when used.** If code falls back to a default because the primary path failed, that fact must be observable (log line, return flag, counter) — not indistinguishable from the primary path succeeding.
3. **Every truncation/sampling/cap is announced.** If a result set, log tail, or output is capped or sampled, state what was dropped. Silent truncation reads as "covered everything" when it didn't.
4. **Every retry exhaustion is a hard failure**, not a quiet return of the last (failed) attempt's data.
5. **Distinguish "zero results" from "search failed."** An empty result and an error must never produce the same downstream code path.

## Failure scenarios this prevents

- A scraper that catches per-item exceptions and continues, silently dropping 30% of records — reported as "scrape complete."
- A batch job that falls back to cached/stale data on API failure without flagging it, so downstream consumers treat stale data as fresh.
- A search or grep helper that caps at N results and returns them with no indication more exist, leading to false "nothing found" conclusions.
- A retry wrapper that returns the last failed response body after exhausting retries, which looks like a normal (if odd) success response.

## Guardrails

- Do not add error handling for scenarios that can't happen — this skill governs paths that already handle errors, not adding new ones speculatively.
- When reviewing someone else's code for this pattern, grep specifically for `except:`, `catch`, `|| `, `.slice(`, `head -`, `LIMIT`, and inspect what happens on the failure branch.
