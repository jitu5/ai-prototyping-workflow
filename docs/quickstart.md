# Quickstart — Your First Prototype With This Workflow

A walkthrough for the first time you use this workflow end-to-end. Plan for half a day of setup on your first run; subsequent prototypes will be much faster.

> **Tiny, throwaway feature?** You don't need the full pipeline. Jump to [The lean path](#the-lean-path-for-tiny-prototypes) at the bottom — same gates, far less ceremony.

---

## Prerequisites

- Claude Code installed (or another AI coding agent that reads `AGENTS.md` and `.claude/agents/`)
- Your prototype's repo created (can be a new directory)
- A rough user story you want to turn into a prototype

---

## Step 0 — Fork this workflow into your project

```bash
# in your new prototype's repo
cp -r /path/to/ai-prototyping-workflow/.claude .
cp /path/to/ai-prototyping-workflow/AGENTS.md .
cp /path/to/ai-prototyping-workflow/CLAUDE.md .
cp /path/to/ai-prototyping-workflow/constitution.md .
cp -r /path/to/ai-prototyping-workflow/templates ./templates
cp -r /path/to/ai-prototyping-workflow/specs ./specs
cp templates/PROGRESS.md ./PROGRESS.md   # the living state file lives at the repo root
```

You can also clone this repo and rename it, or use it as a GitHub template.

---

## Step 1 — Edit the constitution

Open `constitution.md` and update the tech stack rails to match your project. Delete the sections that don't apply.

This takes 10–30 minutes the first time. After that, you reuse it across prototypes in the same repo.

> **Human gate 1:** Tech lead approves the constitution before any feature work starts.

---

## Step 2 — Create the feature folder

```bash
cp -r specs/_template specs/my-feature
```

Fill in `specs/my-feature/spec.md` with your user story and acceptance criteria. Don't worry about being perfect — the analyst will help.

---

## Step 3 — Run the analyst

In Claude Code:

```
Use the analyst subagent to validate the spec at specs/my-feature/spec.md
```

The analyst will:
- Restate the requirement back to you (catches misinterpretation early)
- Ask clarifying questions, grouped by category
- Save them to `specs/my-feature/clarifications.md`

Answer the questions. Re-run the analyst until it says "no further questions." Usually 1–2 rounds.

> **Human gate 2:** You sign off the spec before planning starts.

---

## Step 4 — Run the architect

```
Use the architect subagent to design the implementation plan
```

The architect will:
- Read the constitution, spec, and clarifications
- Decide tech stack, types, state, folder structure
- Write the architecture sections of `specs/my-feature/plan.md`
- Flag libraries that need the doc-fetcher

**Read the architecture carefully.** This is where over-engineering creeps in. Reject anything that feels like overkill.

Expect 2–4 revisions. This is the part of the workflow that took us 4–5 days on Kedro Builder. It's not a bug; it's the engineering work.

---

## Step 5 — Run the planner

```
Use the planner subagent to break plan.md into phases and generate task files
```

The planner adds the phase breakdown to `plan.md` and creates `phase-N-tasks.md` files. Review the breakdown:

- Does each phase look like one context window's worth of work?
- Does Phase 1 lock the cross-phase contracts?
- Are dependencies between phases clear?
- For a tiny prototype, a single phase is fine — don't force a split.

> **Human gate 3:** You approve the plan before any code is written.

---

## Step 6 — Run the doc-fetcher (if needed)

For each library flagged "doc-fetch needed: yes" in `plan.md`, run this *after* the planner and *before* the phase that uses the library — the doc-fetcher reads that phase's `phase-N-tasks.md`:

```
Use the doc-fetcher subagent to fetch React Flow API docs for the upcoming canvas phase
```

The doc-fetcher writes to `specs/my-feature/refs/<library>.md`. Skip this and the implementer will waste tokens crawling docs mid-build.

---

## Step 7 — Build each phase

For each phase, in order, in a fresh Claude Code session:

```
Use the implementer subagent to build Phase 1
```

The implementer will:
- Read the full context (constitution, spec, plan, phase tasks, PROGRESS.md, refs)
- Restate its understanding before writing code (sanity check — read this!)
- Implement the phase
- Write tests
- Run the test suite
- Update `PROGRESS.md`

After the implementer finishes, run the reviewer:

```
Use the reviewer subagent to check Phase 1 for spec drift
```

The reviewer will Approve, Approve with notes, or Block.

For any phase that touches UI, also run the visual-check subagent before approving:

```
Use the visual-check subagent to compare this phase against the brand reference
```

If there's no brand reference, visual-check will flag that and stop — add one or skip it explicitly.

> **Human gate 4:** You approve each phase before the next one starts.

Repeat for every phase.

---

## Step 8 — Demo and capture the outcome

Once all phases are complete, the prototype is demo-ready. After the demo, record the **Outcome** in `PROGRESS.md`:

- The Continue / Rework / Stop / Productise decision, plus one line on why
- This decision gates the next step: harden only if you're keeping it

---

## Step 9 — Harden & document (only if the prototype continues)

If the outcome is **Rework or Stop**, you're done — skip this step. Don't polish a prototype you're not keeping.

If the outcome is **Continue or Productise**:

```
Use the reviewer subagent in harden mode to run the three refactor passes
```

The reviewer will:
- Pass 1 — Find and consolidate duplication
- Pass 2 — Remove dead code
- Pass 3 — Simplify for readability
- Generate `ARCHITECTURE.md` from `templates/ARCHITECTURE.md`

Tests stay green throughout.

---

## Common pitfalls

**"I'll just skip the spec for this small one."** Don't. Even a one-day prototype benefits from 30 minutes of spec. Without it, every session re-interprets your intent.

**"The plan looks fine, let's start coding."** The plan will look fine on round 1 and be subtly wrong. Read it twice. Push back on overkill.

**"The implementer wants to refactor Phase 1 while doing Phase 3."** No. Tell it to log the refactor in `PROGRESS.md` under "Deferred work" and stay in scope.

**"PROGRESS.md feels like paperwork."** It's the single most important habit. Skip it once and Phase 5 will start cold with no idea what Phases 1–4 actually produced.

**"Tests are slowing me down."** Tests are how the refactor pass is safe. Without them, the harden stage can't preserve behaviour.

---

## When something goes wrong

- **Spec drift mid-phase** → Stop the implementer. Ask: is the spec wrong or is the code wrong? Update one of them. Restart the phase.
- **Phase too big, ran out of context** → Stop. Re-run the planner to split the phase.
- **Tests red after the implementer "finished"** → Phase is not done. Send back to the implementer with the failing test list.
- **Implementer changed files outside the phase's scope** → Send back. Ask why. Either accept the change with an updated plan, or revert.

---

## The lean path (for tiny prototypes)

For a genuinely small, throwaway prototype, the full pipeline is overkill. Keep the **gates**, drop the **ceremony**:

1. **Constitution** — reuse the repo's existing one; don't write a new one.
2. **Spec** — fill in `spec.md`. **Skip Clarify** (no `clarifications.md`) unless something is genuinely ambiguous. → 🚦 sign off the spec.
3. **Plan + one phase** — run the architect, then the planner, and accept a **single `phase-1-tasks.md`** — don't force a split. → 🚦 review the plan.
4. **Build** — `Use the implementer subagent to build Phase 1`, then `Use the reviewer subagent to check Phase 1`. → 🚦 approve.
5. **Ship** — demo it and record the **Outcome** in `PROGRESS.md`. For a throwaway the Outcome is usually Stop or Rework, so you **skip Harden and `ARCHITECTURE.md`** entirely.

`PROGRESS.md` stays short — one phase entry plus the Outcome. That's the whole thing: **spec → plan → one phase → progress → outcome**, four quick gates, no multi-phase machinery.

---

## Your second prototype onwards

Steps 0 and 1 (forking + constitution) only happen once per repo. Steps 2–9 become routine. By your third prototype, the workflow disappears into the background and you're just shipping prototypes fast.
