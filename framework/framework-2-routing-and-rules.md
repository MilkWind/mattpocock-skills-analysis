# Framework 2 — Routing & Rules

> *When* to reach for which tool, and *when/how* to write rules for the AI.
> This framework assumes a skill set is available (this one, or an equivalent).

The spine is the `ask-matt` router's main flow, plus the `writing-great-skills`
discipline for authoring rules.

---

## Part I — Routing: which tool, when

### The main flow: idea → ship
— *ask-matt/SKILL.md:13–26*

1. **Grill first.** Codebase present → `/grill-with-docs` (stateful: writes
   `CONTEXT.md` + ADRs). No codebase → `/grill-me` (stateless). Both run the
   `/grilling` primitive.
   — *ask-matt/SKILL.md:17*
   **Anti-pattern:** skipping grilling on a "small" change — small changes misalign
   the same way big ones do.

2. **Branch — does the question need to run?** If a question needs a runnable answer
   (state, logic, a UI you must see): `/handoff` out → `/prototype` → `/handoff` back.
   — *ask-matt/SKILL.md:18–22*

3. **Branch — is this multi-session?**
   - **Yes** → `/to-spec` → `/to-tickets` (tracer-bullet tickets, each declaring its
     blocking edges) → one `/implement` per ticket, **clearing context between each**.
   - **No** → `/implement` in the same window.
   Either way `/implement` drives `/tdd` internally and closes with `/code-review`.
   — *ask-matt/SKILL.md:23–26*

### When to use the grilling family
Use **every** time you intend a change — it's the most-used skill in the repo.
— *README.md:103*
- `/grill-me` — no codebase; sharpen any plan/design.
- `/grill-with-docs` — codebase present; build domain language + ADRs as you go.
- `/grilling` — the reusable loop behind both.
**Anti-pattern:** treating grilling as overhead for "obvious" work — the obvious work
is where misalignment hides.

### When to hand off vs compact
- `/handoff` — you want a fresh session but must preserve this conversation (window
  full, branching to a prototype, degraded).
- `/compact` (built-in) — stay in the same conversation at an intentional phase break.
- `/claude-handoff` — same as handoff but launches a background agent immediately.
**Anti-pattern:** compacting when you actually need a clean window — summarized history
misleads the new task.
— *ask-matt/SKILL.md:62–64*

### When to prototype / research
- `/prototype` — one design question needs to *run* to be answered ("does this state
  model feel right?" / "what should this look like?"). Throwaway from day one; keep the
  answer, delete the code.
  — *prototype/SKILL.md:21*
- `/research` — delegate the reading to a **background agent** working against
  **primary sources**; it leaves a cited markdown file. Keep working while it reads.
  — *research/SKILL.md:6, 10*
**Anti-pattern:** prototyping the *whole feature* "to see if it works" — a prototype
answers one question, not all of them.

### When to use wayfinder
For a **huge, foggy effort** — a greenfield project or giant feature too big for one
session. It produces **decisions, not deliverables**, charting a shared map of
decision tickets until the route is clear, then hands off to `/to-spec`.
— *ask-matt/SKILL.md:44*
**Anti-pattern:** wayfinder on a well-scoped feature — it's slow and dense; save it for
when you genuinely can't see the way.

### When to triage / diagnose / improve architecture
- `/triage` — only for issues **you didn't create** (incoming bugs/requests). Tickets
  from `/to-tickets` are already agent-ready; don't triage them.
  — *ask-matt/SKILL.md:38–40*
- `/diagnosing-bugs` — hard bugs / perf regressions; refuses to theorise before a tight
  loop exists.
  — *ask-matt/SKILL.md:42*
- `/improve-codebase-architecture` — run in spare moments to surface deepening
  opportunities; revisit every few days.
  — *README.md:178*
**Anti-pattern:** triaging your own generated tickets — double-handling work that's
already specified.

### When to code-review / tdd
- `/code-review` — whenever you review a branch/PR/diff against a fixed point. Two axes
  (Standards + Spec) run as **parallel sub-agents** so neither pollutes the other.
  — *code-review/SKILL.md:11, 84–88*
- `/tdd` — build a concrete behavior test-first without a full spec.
**Anti-pattern:** one omnibus review that mixes "does it meet the spec?" with "does it
follow conventions?" — one axis masks the other.

### The vocabulary layer (runs *beneath* the others)
- `/domain-modeling` — *actively* build/sharpen the domain model (challenge terms,
  edge-case scenarios, write ADRs, update `CONTEXT.md` inline). Note: merely *reading*
  `CONTEXT.md` for vocabulary is **not** this skill.
  — *invocation.md:18*
- `/codebase-design` — the deep-module vocabulary (module, interface, depth, seam,
  adapter, leverage, locality).

---

## Part II — When & how to write rules for the AI

### When to turn something into a skill
Make it a skill when the same *process* will repeat, **or** when behavior is
unpredictable and drifts each run and you want to anchor it.
**Anti-pattern:** writing a skill for a one-off task — you pay load forever for
something used once.

### Information hierarchy: how to lay out rules
— *writing-great-skills/SKILL.md:30–44*
- **Steps** — ordered actions, primary tier. Each step ends on a **completion
  criterion** that is *checkable* (done vs not-done) and, where it matters,
  *exhaustive* ("every modified model accounted for", not "produce a change list").
  — *writing-great-skills/SKILL.md:34*
- **Reference** — definitions/rules consulted on demand; push behind a pointer when
  only some branches need it (progressive disclosure).
  — *writing-great-skills/SKILL.md:42*
**Anti-pattern:** a vague criterion like "understanding reached" — it invites
premature completion.

### model-invoked vs user-invoked: the invocation decision
— *writing-great-skills/SKILL.md:11–20*
- **model-invoked** — the agent must reach it on its own, or another skill must call
  it. Pays **context load** (description always in the window).
- **user-invoked** — only the human fires it by typing the name. Pays **cognitive
  load** (you are the index); zero context load.
- **Router skill** — when user-invoked skills multiply past memory, add one
  user-invoked skill (e.g. `ask-matt`) that names the rest and when to use each.
**Anti-pattern:** making everything model-invoked "so it always fires" — every
description competes for attention every turn.

### Rule-writing failure-mode checklist (diagnose a struggling skill)
— *writing-great-skills/SKILL.md:74–83*
- **Premature completion** — sharpen the completion criterion first (cheap, local);
  only if irreducibly fuzzy *and* you observe the rush, hide the later steps by
  splitting.
- **Duplication** — one meaning in >1 place → collapse to a single source of truth.
- **Sediment** — stale layers from "add feels safe, remove feels risky" → prune.
- **Sprawl** — too long even if all-live → disclose reference behind pointers, split
  by branch or sequence.
- **No-op** — changes nothing vs default → delete the whole sentence.
- **Negation** — prohibition backfires → restate the positive.

### When to write an ADR (be sparing)
Only when **all three** hold: (1) hard to reverse, (2) surprising without context,
(3) the result of a real trade-off. If any is missing, skip it.
— *domain-modeling/SKILL.md:68–72*
**Anti-pattern:** an ADR for "we use prettier" — reversible, unsurprising, no real
alternatives. It's noise.

### When to split a skill
- **By invocation** — a distinct leading word should trigger it on its own, or another
  skill must reach it.
- **By sequence** — when the steps still ahead tempt the agent to rush the current one.
— *writing-great-skills/SKILL.md:48–52*
**Anti-pattern:** splitting every concept into its own model-invoked skill — context
load stacks until the window drowns in descriptions.

---

## The four failure modes (cross-reference)

| Failure mode | Routed to |
|---|---|
| Misalignment | grilling family; routing step 1 |
| Verbosity / jargon | `/grill-with-docs` + `/domain-modeling` |
| Code doesn't work | `/tdd`, `/diagnosing-bugs`, `/code-review` |
| Ball of mud | `/improve-codebase-architecture`, `/codebase-design`, `/to-spec` |

— *README.md:88–182*
