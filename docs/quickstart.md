# Quickstart — Your First Prototype With This Workflow

A walkthrough for the first time you use this workflow end-to-end. Plan for half a day of setup on your first run; subsequent prototypes will be much faster.

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

## Step 5 — Run the doc-fetcher (if needed)

For each library flagged "doc-fetch needed: yes" in `plan.md`:

```
Use the doc-fetcher subagent to fetch React Flow API docs for the upcoming canvas phase
```

The doc-fetcher writes to `specs/my-feature/refs/<library>.md`. Skip this and the implementer will waste tokens crawling docs mid-build.

---

## Step 6 — Run the planner

```
Use the planner subagent to break plan.md into phases and generate task files
```

The planner adds the phase breakdown to `plan.md` and creates `phase-N-tasks.md` files. Review the breakdown:

- Does each phase look like one context window's worth of work?
- Does Phase 1 lock the cross-phase contracts?
- Are dependencies between phases clear?

> **Human gate 3:** You approve the plan before any code is written.

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

> **Human gate 4:** You approve each phase before the next one starts.

Repeat for every phase.

---

## Step 8 — Harden

Once all phases are complete:

```
Use the reviewer subagent in harden mode to run the three refactor passes
```

The reviewer will:
- Pass 1 — Find and consolidate duplication
- Pass 2 — Remove dead code
- Pass 3 — Simplify for readability
- Generate `ARCHITECTURE.md`

Tests stay green throughout.

---

## Step 9 — Visual check (frontend only)

```
Use the visual-check subagent to compare the prototype against the brand reference
```

If there's no brand reference, the visual-check will flag that and stop. Either add one or skip this step explicitly.

---

## Step 10 — Demo and capture learning

The prototype is now demo-ready. After the demo:

- Write a `learning.md` capturing what worked, what didn't, what the team should do next
- Make the Continue / Rework / Stop / Productise call

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

## Your second prototype onwards

Steps 0 and 1 (forking + constitution) only happen once per repo. Steps 2–10 become routine. By your third prototype, the workflow disappears into the background and you're just shipping prototypes fast.
