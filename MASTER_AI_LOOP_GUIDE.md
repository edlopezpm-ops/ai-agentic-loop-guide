# Master Guide — Looping Codex + Claude for Mini Projects

**Stack covered:** SQL · Python · Java · TypeScript · PostgreSQL · HTML5

This guide is a reusable playbook. Drop it into any new mini project on this
stack and follow the loop below instead of re-deriving a workflow from
scratch each time.

---

## 1. What "AI Looping" actually means

An AI loop is **not** "ask an AI to build the whole thing in one prompt."
It's a short, repeating cycle — plan, implement, verify, reflect — where each
pass produces a small, checkable increment of working software. The loop
ends when the increment meets its exit criteria (see §5), not when the model
stops talking.

Three related terms, used precisely:

| Term | Meaning here |
|---|---|
| **AI Looping** | The repeating plan→build→verify→reflect cycle inside one task. |
| **AI Orchestration** | Coordinating *multiple* agents/roles (e.g. an architect agent and an implementer agent) across that loop, each with a distinct responsibility. |
| **AI Agentic** | The agent uses tools autonomously within a step (reads files, runs tests, greps code) instead of only producing text for a human to execute. |

Looping without orchestration works fine for a single small task. Orchestration
matters once a project is big enough that "design" and "build" genuinely
benefit from different review passes — which is most mini projects on this
stack, since they usually span a DB schema, a backend, and a frontend.

---

## 2. The two-role pattern: Architect vs. Implementer

Two AI coding tools, two jobs. Don't blur them — the value of orchestration
comes from the second pass being a genuine independent check, not an echo of
the first.

- **Architect role** (e.g. Codex): decides *what* to build and *why*.
  Data model, module boundaries, API contracts, tech choices, tradeoffs.
  Output: a short design note + a task list. No implementation.
- **Implementer role** (e.g. Claude): decides *how* to build it well.
  Writes the code, the tests, the docs, runs everything, reports back with
  evidence (test output, not just "should work").

If you only have one tool available, you can still run both roles —
just do them as two distinct prompts/sessions, not one blended pass. The
discipline of separating "decide" from "build" is what produces the benefit,
not which product name is attached to which role.

---

## 3. The loop, step by step

```
 ┌─────────────┐     ┌──────────────┐     ┌─────────────┐     ┌──────────┐
 │  1. DEFINE  │ --> │  2. PLAN     │ --> │ 3. IMPLEMENT│ --> │ 4. VERIFY│
 │ the smallest│     │ (Architect)  │     │(Implementer)│     │  (tests, │
 │ shippable   │     │ design +     │     │  code, tests│     │   run it)│
 │ slice       │     │ task list    │     │  docs       │     │          │
 └─────────────┘     └──────────────┘     └─────────────┘     └────┬─────┘
        ^                                                          │
        │                 ┌───────────────┐                        │
        └──────────────── │ 5. REFLECT    │ <──────────────────────┘
                           │ what broke,   │
                           │ what's next   │
                           └───────────────┘
```

### Step 1 — Define the smallest shippable slice
Pick a slice small enough to fully verify in one pass. "User can log in" is
a slice. "Full auth system" is not — that's several slices.

### Step 2 — Plan (Architect pass)
Prompt shape:

```
You are the architect for [slice]. Stack: SQL/Postgres + Python/Java + TS + HTML5.
Given [current state / schema / existing files], propose:
1. Data model or schema changes (SQL)
2. Module/service boundaries and where this slice's logic lives
3. Any new contracts (API shape, function signatures) — no implementation
4. A numbered task list an implementer can execute without further design decisions
Flag any assumption you're making explicitly.
```

Do not let the architect pass write implementation code. If it does, discard
the code and keep the design.

### Step 3 — Implement (Implementer pass)
Feed the architect's task list in as-is. Prompt shape:

```
Implement task list below exactly as scoped. For each task:
- Write the code
- Write a test that would fail without this change
- Run the test and paste the actual output
Stack constraints: [SQL dialect], [Python/Java version], [TS config], [HTML5 target].
Do not expand scope beyond the task list. If the task list is wrong, stop and say why instead of improvising.
```

### Step 4 — Verify
Run it for real. This is a deterministic-space step — don't let the model
merely assert success.

- SQL: run the migration/query against a real (or scratch/temp) database.
- Python/Java: run the actual test suite, not a description of one.
- TypeScript: `tsc --noEmit` + test runner.
- HTML5/frontend: load it in a browser for the golden path, not just unit tests.

### Step 5 — Reflect
One or two sentences, written down (not just in chat scrollback):
- What broke and why (feeds the next Define step).
- What's the next smallest slice.

Then go back to Step 1. Stop the loop when the slice meets the exit criteria
below — not when it "feels done."

---

## 4. Exit criteria per slice (Definition of Done)

A slice is done when **all** of these are true:

- [ ] The functionality works for the golden path and the failure paths
      considered in Step 1.
- [ ] There's a test that fails without the change and passes with it.
- [ ] The schema/contract changes are reflected in the code that consumes them
      (no drift between SQL schema and the ORM/query layer, no drift between
      backend contract and frontend caller).
- [ ] Nothing else in the repo was changed outside the slice's scope.
- [ ] A human (you) actually ran it once, not just read the diff.

If any box is unchecked, the loop isn't done — go back to Step 3 or 4.

---

## 5. Applying this to each layer of the stack

| Layer | Architect pass produces | Implementer pass produces | Verify by |
|---|---|---|---|
| PostgreSQL / SQL | Schema, constraints, indexes, migration plan | Migration files, seed data | Run migration on a scratch DB, inspect `\d table` |
| Python | Module boundaries, function contracts | Implementation + `pytest` | Run the actual test suite |
| Java | Package/class boundaries, interfaces | Implementation + JUnit tests | `mvn test` / `gradle test` output |
| TypeScript | Type contracts, module structure | Implementation + unit tests | `tsc --noEmit`, test runner |
| HTML5 | Page/component responsibilities, data flow | Markup/JS/CSS | Load in a real browser, click the golden path |

Cross-layer slices (e.g. "add a field end-to-end") should still go through
one Define→Plan→Implement→Verify pass covering all affected layers at once —
don't split a single user-visible change into disconnected per-layer loops
that never get integration-tested together.

---

## 6. Guardrails — keep a human in the loop

An agentic loop that runs unsupervised for hours is a liability, not a
feature. Minimum guardrails:

1. **Never let the loop auto-commit or auto-push.** Implementation and
   verification can be autonomous; putting it into shared history is a
   human decision.
2. **Cap iterations.** If a slice hasn't met its exit criteria in ~3 loop
   passes, stop and re-scope — the slice was probably too large or the
   requirement was unclear (see: NEEDS_CONTEXT).
3. **Treat "it compiles" and "it works" as different claims.** Verification
   must involve running something real, not just absence of errors.
4. **Redact before you publish.** Anything the loop touches that could end
   up in a shared or public repo (this one included) gets scanned for
   secrets before commit — passwords, API keys, tokens, private keys.
5. **One in-progress task at a time.** Parallel uncoordinated loops on the
   same files produce merge conflicts and inconsistent designs.

---

## 7. Minimal starter prompt (copy/paste to kick off a new mini project)

```
New mini project. Stack: PostgreSQL, Python (or Java), TypeScript, HTML5.
Goal: [one sentence — who benefits, what changes].

Run this as a loop:
1. You (architect pass): propose the smallest end-to-end slice that proves
   the concept, including schema, module boundaries, and contracts. No code yet.
2. I'll approve or correct the plan.
3. You (implementer pass): build exactly that slice, with tests, and show me
   the actual test output.
4. I'll verify it myself before we move to the next slice.

Do not scope-creep. Do not skip verification. Ask if the goal is ambiguous
instead of guessing.
```

---

## 8. Why this works

Splitting "decide" from "build" catches bad assumptions before they're
implemented instead of after — cheaper to fix a wrong schema on paper than
after three modules depend on it. Keeping slices small keeps verification
honest: a human can actually check a 30-line change ran correctly; nobody
can meaningfully check a 3,000-line one. The loop is the mechanism that
keeps AI-assisted development from turning into unverified velocity.
