# Framework 1 — General AI Usage

> Applicable to any LLM / coding agent. Three goals: **alignment, fidelity, control.** The root idea: an AI is a stochastic system; your job is to wring *predictability* out of it — the same *process* every run, not the same output.
> — *writing-great-skills/SKILL.md:7*

This framework restates the latent discipline behind the skills so it can be used without them. Each item: principle → source → anti-pattern.

---

## A. Align before you act

### A1. Nobody knows what they want up front
Treat the first prompt as the opening of an interview, not a spec. Walk the decision tree one branch at a time, and attach your recommended answer to every question.
— *grilling/SKILL.md:6*
**Anti-pattern:** asking the agent to "just build it" from a one-liner, then being surprised it built the wrong thing.

### A2. One question at a time
Pose a single question and wait. Multiple questions at once bewilder the human and produce shallow answers.
— *grilling/SKILL.md:8*
**Anti-pattern:** a five-question bullet list "to save round-trips" — it saves none, because the answers come back tangled.

### A3. Do not act until alignment is confirmed
The output of alignment is a shared understanding; only then does work begin.
— *grilling/SKILL.md:12*
**Anti-pattern:** the agent starts coding mid-interview "to be helpful."

---

## B. Divide labor: facts vs decisions

### B1. Look it up, don't ask
Anything findable in the environment (filesystem, tools, docs, code) should be retrieved, not put to the human.
— *grilling/SKILL.md:10*
**Anti-pattern:** "where should the config live?" — it burns a turn on something a 5-second `grep` settles.

### B2. Decisions are the human's
Every genuine decision goes to the human and waits for the answer. The line between B1 and B2 is the highest-leverage judgment in the whole craft.
— *grilling/SKILL.md:10*
**Anti-pattern:** the agent silently choosing a library / data model because "it seemed reasonable."

---

## C. Fight context rot

### C1. Respect the smart zone
Within roughly the first ~120k tokens the model reasons sharply; past it, reasoning degrades. Do not push on degraded.
— *ask-matt/SKILL.md:32*
**Anti-pattern:** stuffing 40 files into context "for completeness" until answers get visibly worse, then continuing anyway.

### C2. Keep alignment through the spec in one window
Do not compact or clear between idea → spec → tickets; let the thinking compound. Clear between *implementations*, restarting from the ticket each time.
— *ask-matt/SKILL.md:30*
**Anti-pattern:** compacting mid-spec because "the thread is long," losing the nuance that made the spec coherent.

### C3. Never compact mid-phase
Compacting inside a phase lets the agent lose its way. Compact only at intentional breaks *between* phases.
— *ask-matt/SKILL.md:64*
**Anti-pattern:** `/compact` right before the final review "to save tokens."

---

## D. Cross-context transfer: bridge with artifacts, not memory

### D1. Make decisions into durable artifacts
Freeze each decision as a persistent artifact (spec, ADR, glossary, issue, commit, prototype branch). The artifact, not the chat, is what survives.
— *handoff/SKILL.md:12*
**Anti-pattern:** relying on "the agent remembers we decided X three threads ago."

### D2. Reference, never restate
When crossing contexts, pass the artifact's path or URL — never duplicate content already captured there.
— *handoff/SKILL.md:12*
**Anti-pattern:** pasting the whole spec into the new thread, so two copies drift.

### D3. Handoff forks; compact continues
- **Handoff** → new session, context carried by a file. Use when the window is full, when branching off (e.g. into a prototype), or at a degraded boundary.
- **Compact** → same session, history summarized. Use only at phase boundaries.
— *ask-matt/SKILL.md:62–64*
**Anti-pattern:** using compact to "continue" into work that actually needs a clean window — the summarized history misleads the fresh task.

### D4. One meaning, one home
Each fact or decision lives in exactly one authoritative place; everything else links to it.
— *writing-great-skills/SKILL.md:55*
**Anti-pattern:** the same rule restated in the README, the spec, and a comment — change one and the other two lie.

---

## E. Build a shared language

### E1. A glossary compounds
Maintain a project glossary so the agent says "materialization cascade" instead of twenty words. README calls this the single coolest technique in the repo.
— *README.md:111–133*
**Anti-pattern:** letting every session re-derive the jargon from scratch — verbose, inconsistent naming, and wasted thinking tokens.

### E2. Keep the glossary pure
The glossary defines terms only — no implementation details, no scratch notes. It is a dictionary, nothing else.
— *domain-modeling/SKILL.md:64*
**Anti-pattern:** stuffing config examples and todos into `CONTEXT.md` until it stops being a dictionary.

---

## F. Feedback is the speed limit

### F1. No feedback, no progress
"The rate of feedback is your speed limit." Without feedback the agent flies blind.
— *README.md:144*
**Anti-pattern:** asking the agent to "write the whole feature, then we'll test" — one giant blind leap.

### F2. Build a tight loop before theorising
Before any hypothesis, construct one command that goes red on *this* bug. No red-capable command, no theory.
— *diagnosing-bugs/SKILL.md:14, 60*
**Anti-pattern:** reading code to "figure out" the bug before a reproducing command exists — the exact failure this prevents.

### F3. Tighten the loop like a product
Fast (seconds), deterministic, sharp signal, agent-runnable. A 30-second flaky loop is barely better than none.
— *diagnosing-bugs/SKILL.md:41*
**Anti-pattern:** a manual 2-minute click-through "repro" the agent can't run itself.

### F4. Red before green; one slice at a time
Write the failing test, then the minimum code to pass; one seam, one test, one implementation per cycle.
— *tdd/SKILL.md:34–35*
**Anti-pattern:** writing all tests first, then all implementation (horizontal slicing) — you test imagined shapes, not real behavior.

---

## G. Writing instructions for any LLM

### G1. State the positive; avoid negation
Prohibition drags the forbidden behavior into context and makes it *more* available. Describe the target behavior so the banned one is never spoken.
— *writing-great-skills/SKILL.md:83*
**Anti-pattern:** "don't write verbose comments" — the word "verbose" primes exactly that.

### G2. Borrow leading words
Use a compact concept the model already holds in pretraining to anchor a whole region of behavior in the fewest tokens.
— *writing-great-skills/SKILL.md:63*
**Anti-pattern:** spelling out "fast, deterministic, low-overhead" every time instead of one pretrained word like *tight*.

### G3. Run the no-op test
Does this line change behavior versus the model's default? If not, delete the whole sentence — don't trim it.
— *writing-great-skills/SKILL.md:82*
**Anti-pattern:** keeping "be thorough" when the model is already thorough-ish — load for nothing.

### G4. Progressive disclosure
Push reference behind a pointer; keep the top legible. The agent loads the detail only when the pointer fires.
— *writing-great-skills/SKILL.md:42*
**Anti-pattern:** a 400-line prompt where the actual instructions are buried under background.

---

## The four failure modes (cross-reference)

Every principle above treats one of README's four failure modes:

| Failure mode | Cured by |
|---|---|
| Misalignment — "didn't do what I want" | Section A + B |
| Verbosity / jargon | Section E |
| Code doesn't work | Section F |
| Ball of mud | (see Framework 2, routing) |

— *README.md:88–182*
