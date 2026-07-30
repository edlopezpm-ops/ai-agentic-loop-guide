# Engineering Constitution — A Guide to Persistent AI Governance Documents

An "engineering constitution" is a persistent, versioned file — `CLAUDE.md`,
`CODEX.md`, `AGENTS.md` — that tells an AI coding assistant how to operate
inside a repository. It's not a one-off prompt. Context windows reset every
session; this file doesn't. It's the standing policy the assistant reloads
every time.

Sample copies of a real one (redacted, versioned, consolidated down to one
canonical revision per assistant) live in
[`collected-md/governance/`](collected-md/governance/) in this repo — this
guide explains the pattern behind them, not just this one instance of it.

## Why maintain one at all

- **Consistency across sessions.** Without it, you re-explain your
  preferences, your stack, and your risk tolerance every single
  conversation.
- **Encodes hard-won preferences.** Corrections you've already made once
  ("don't mock the database in tests," "ask before force-pushing") stay
  fixed instead of repeating.
- **Draws an explicit authority boundary.** What the assistant can decide
  unilaterally vs. what it must stop and ask about.
- **Creates an auditable contract.** Anyone reading the repo — a
  collaborator, a future you — can see how AI-assisted work happens here
  without reconstructing it from chat history.

## Where it lives, per tool

Different assistants look for different filenames automatically:

| File | Read by |
|---|---|
| `CLAUDE.md` | Claude Code |
| `AGENTS.md` | Codex CLI and other agentic tools (emerging cross-tool convention) |

If you run more than one assistant against the same repo, you'll end up
maintaining more than one file. That's fine — see the versioning section
below for how to keep them from drifting into an unmanageable pile of
per-project forks.

## A structure that earns its place

Not every constitution needs every section below, but these are the ones
that actually change assistant behavior rather than just restating good
intentions:

1. **Mission** — one sentence every other rule serves.
2. **Role definition** — partner/reviewer, not autonomous decision-maker.
   State explicitly what the assistant is *not*.
3. **About the user** — real expertise level, so the assistant doesn't
   over-explain things you already know or under-explain things you don't.
4. **Decision framework** — which problems are deterministic (write code,
   don't re-reason every time) vs. which require judgment (architecture,
   tradeoffs) — and when to stop and ask instead of guessing.
5. **Engineering principles** — your actual technical opinions: testing
   philosophy, architecture bias, what "done" means.
6. **Communication style** — how you specifically want to be talked to.
7. **Safety rules** — destructive-action guardrails, secrets handling,
   confirmation requirements before anything hard to reverse.
8. **Status vocabulary** — e.g. `DONE` / `DONE_WITH_CONCERNS` / `BLOCKED` /
   `NEEDS_CONTEXT`, so a report is unambiguous about whether work is
   actually finished.

## Versioning discipline

This is the concrete lesson from building this repo. Auditing every `.md`
file across several real local projects turned up **three different
revisions of the same `CODEX.md`** — two identical older copies forked
across three project folders, and one materially more developed revision
in a fourth. Nobody had gone back to update the older copies once the
newer one was written; they'd just been left in place.

Treat the constitution as **one living document, not a fork per project**:

- Keep exactly one current revision. When it changes, propagate the
  change everywhere it's deployed instead of letting old copies linger.
- Tag the version inside the file itself (a plain `V2` line at the top is
  enough) so a stale copy is identifiable at a glance, without needing a
  diff against every other project to notice it's behind.
- If you find drift during an audit, that's a maintenance signal — treat
  it the same as any other detected inconsistency, not as an acceptable
  side effect of copy-pasting a file between projects.

## What not to put in it

- **Credentials, tokens, connection strings.** Same rule as any other file
  in the repo — a governance file is not exempt.
- **Personal identifying information** — email addresses, phone numbers,
  device serial numbers. This file gets copied into new projects, handed
  to collaborators, and — as happened while building this exact repo —
  potentially collected into a public reference archive. Write it assuming
  it will eventually be read by more people than just you.
- **Instructions trying to restrict which AI tool is allowed to read the
  file.** One of the source files audited for this repo contained a clause
  instructing "any Claude or Anthropic technology" to stop reading it and
  reserving the file for a different vendor's tool. It doesn't function as
  an access control — any tool capable of opening a text file can read it
  regardless of what the file's own text asks it to do — and it reads as
  adversarial rather than as a real security boundary. If you don't want a
  file read by a given tool, don't put it on that tool's context path;
  don't rely on the file to police itself.

## How this relates to the loop

The constitution is the standing ruleset; [`MASTER_AI_LOOP_GUIDE.md`](MASTER_AI_LOOP_GUIDE.md)
is the process that runs under it. The loop tells the assistant *what to do
this iteration* (define, plan, implement, verify, reflect); the constitution
tells it *how to behave* while doing any of that — same relationship a
process has to the policy it operates under.
