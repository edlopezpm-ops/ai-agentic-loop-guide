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
- **[`ENGINEERING_CONSTITUTION_GUIDE.md`](ENGINEERING_CONSTITUTION_GUIDE.md)**
  — a guide to the pattern behind `CLAUDE.md`/`CODEX.md`/`AGENTS.md`
  governance files: why to maintain one, a section structure that actually
  changes assistant behavior, and a versioning discipline learned directly
  from consolidating the real files in this repo (see below).
- **[`collected-md/`](collected-md/)** — a sanitized, deduplicated,
  version-consolidated sample of real `.md` files (project READMEs and
  AI-collaboration governance docs) gathered from local projects on one
  machine. `governance/` holds exactly one canonical, current revision each
  of `CLAUDE.md`, `CODEX.md`, `agents.md` — older superseded revisions and
  exact duplicates were removed rather than shipped alongside the current
  one. Included as concrete, real-world illustration of what these docs
  look like in practice — not as a tutorial in themselves.
- **[`MANIFEST.md`](MANIFEST.md)** — where every collected file originally
  came from, which were exact duplicates, and which were older revisions
  superseded by a newer one.

## Why this exists

Most "AI coding" content either shows a single flashy prompt or a vague
diagram of agents talking to each other. Neither is reusable. This repo is
the opposite: a concrete, stack-specific loop with exit criteria, guardrails,
and a starter prompt you can paste into a new project today.

## How to use it

1. Read `MASTER_AI_LOOP_GUIDE.md` top to bottom once.
2. Read `ENGINEERING_CONSTITUTION_GUIDE.md` if you don't already keep a
   `CLAUDE.md`/`AGENTS.md` in your own projects — the loop assumes one
   exists.
3. Copy the starter prompt in the loop guide's §7 into a new project with
   two AI coding sessions (one architect role, one implementer role — or
   the same tool used twice, deliberately, for each role).
4. Follow the loop: Define → Plan → Implement → Verify → Reflect.
5. Stop when the Definition of Done checklist is fully checked, not when it
   "feels" done.

## Security note

Everything under `collected-md/` was scanned for secret-like patterns
(API keys, private keys, tokens, passwords) before being committed, and
manually reviewed for other identifying data. Three things were found and
redacted: a local-dev database password, a personal email address embedded
in a proprietary-use notice, and a hardware BIOS serial number — see
`MANIFEST.md` for details. If you fork this pattern for your own project
docs, run the same check (automated scan + manual read) before you publish.

`MANIFEST.csv` (a machine-readable version of `MANIFEST.md`) is generated
locally but intentionally not published — it's redundant with `MANIFEST.md`
and kept private via `.gitignore`.

## License

MIT — see [`LICENSE`](LICENSE). Reuse the guide, the structure, or the
prompts freely.

---

Built with the **[AI Engineering Knowledge Racking (AEKR)](https://aekr.io)** workflow.

![Build with AEKR](assets/aekr-banner.png)
