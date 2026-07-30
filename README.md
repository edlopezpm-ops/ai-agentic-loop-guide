# AI Agentic Loop Guide

A small reference project on **AI Looping, AI Orchestration, and Agentic
development** for mini software projects — written so someone else can read
it and actually reuse the workflow, not just look at a diagram.

## What's in here

- **[`MASTER_AI_LOOP_GUIDE.md`](MASTER_AI_LOOP_GUIDE.md)** — the actual
  playbook. Defines AI Looping / Orchestration / Agentic precisely, lays out
  a repeatable plan→implement→verify→reflect loop using two AI coding
  assistants in complementary roles (architect vs. implementer), and maps
  that loop onto a concrete stack: **PostgreSQL, Python, Java, TypeScript,
  HTML5**. Includes a copy-pasteable starter prompt.
- **[`collected-md/`](collected-md/)** — a sanitized sample of real
  `.md` files (project READMEs and AI-collaboration governance docs)
  gathered from local projects on one machine, **deduplicated by content**
  and with any secret-like values (passwords, keys, tokens) redacted before
  publishing. Included as concrete, real-world illustration of what these
  docs look like in practice — not as a tutorial in themselves.
- **[`MANIFEST.md`](MANIFEST.md)** — where every collected file originally
  came from, and which ones were duplicates of each other.

## Why this exists

Most "AI coding" content either shows a single flashy prompt or a vague
diagram of agents talking to each other. Neither is reusable. This repo is
the opposite: a concrete, stack-specific loop with exit criteria, guardrails,
and a starter prompt you can paste into a new project today.

## How to use it

1. Read `MASTER_AI_LOOP_GUIDE.md` top to bottom once.
2. Copy the starter prompt in §7 into a new project with two AI coding
   sessions (one architect role, one implementer role — or the same tool
   used twice, deliberately, for each role).
3. Follow the loop: Define → Plan → Implement → Verify → Reflect.
4. Stop when the Definition of Done checklist (§4) is fully checked, not
   when it "feels" done.

## Security note

Everything under `collected-md/` was scanned for secret-like patterns
(API keys, private keys, tokens, passwords) before being committed. One real
local-dev database password was found and redacted — see `MANIFEST.md` for
details. If you fork this pattern for your own project docs, run the same
check before you publish.
