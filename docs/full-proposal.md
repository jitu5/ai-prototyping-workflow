# AI-Native Prototyping Workflow

*A team proposal for going from idea to working prototype with GenAI, repeatably.*

---

## TL;DR

We can build prototypes 5–10x faster with AI coding agents, but only if the team stops treating it as "vibe coding" and adopts a **spec-driven, multi-agent workflow**. This proposal lays out a concrete, end-to-end process — built on top of what worked when we shipped Kedro Builder in two weeks, and informed by what GitHub (Spec Kit), the BMAD community, and the OpenSpec project have already standardised.

The workflow has three commitments:

1. **Spec before code, always.** No agent starts implementing until the human and the AI agree on what's being built.
2. **Phases that fit one context window.** Long sessions rot; short, well-scoped phases stay sharp.
3. **A small team of specialised agents**, not one super-agent that does everything.

It works for frontend, backend, and Python/data-pipeline prototypes. About 70% of the workflow can be automated; the other 30% is where the human earns their keep.

---

## Why this matters now

Anyone who's tried building something non-trivial with Claude Code, Cursor, or Copilot has hit the same wall:

- The first hour is magic. The fourth hour, the model is forgetting decisions it made an hour ago.
- "Just describe what you want" works for a script. It does not work for an app with 12 phases of dependencies.
- Without a written spec, the agent's interpretation drifts every session. You re-explain the same thing three times.
- Reviewing 4,000 lines of AI-generated code is slower than reviewing 4,000 lines of human-written code, because nothing was reviewed *as it was being designed*.

The industry has converged on the same fix in the last 12 months: **Spec-Driven Development (SDD)**. GitHub shipped Spec Kit, the OpenSpec project gained traction, and BMAD popularised the idea of using specialised AI personas (Analyst, PM, Architect, Dev, QA) instead of one generalist. We don't need to invent this from scratch — we need to adapt it to how our team actually works.

---

## What Kedro Builder taught us

Before generalising, the lessons from our own two-week prototype, in plain terms:

**What worked**

- Starting with a **detailed user story + acceptance criteria** as context. Without that document, none of the rest would have happened.
- **Requirement validation up front** — asking the model to repeat back the requirement before any planning. This catches misinterpretation while it's cheap.
- Spending **4–5 days on the plan**, not the code. The plan went through four iterations. This was the real engineering work.
- Splitting the build into **~12 phases**, each scoped to fit inside one ~200K context window.
- A **`context.md` handoff file** written at the end of each phase: what was built, what files exist, what's still pending, what was deferred. The next session reads this instead of "remembering." (In this workflow that file is standardised as **`PROGRESS.md`**.)
- Pre-fetching **library documentation** (React Flow) and feeding the URLs as context, so the agent didn't waste tokens crawling docs.
- A **dedicated refactor cycle** (duplications → dead code → simplification), with tests as the safety net.
- Ending with a **`PROJECT_ARCHITECTURE.md`** so future changes have a map. (Standardised here as **`ARCHITECTURE.md`**.)

**What didn't work first time**

- No visual design at the start meant the first working build looked nothing like Kedro's actual brand. Visual alignment had to happen as a separate post-pass.
- Even with per-phase context files, ~10+ phases produced duplicated code in places. Refactor was non-optional, not nice-to-have.
- Some plan/spec alignment issues only surfaced mid-implementation. The acceptance criteria and the generated plan needed several rounds to lock.

The takeaway: the parts of Kedro Builder that worked map almost perfectly onto Spec-Driven Development. The parts that didn't work map onto gaps SDD frameworks have already solved. We can codify this.

---

## What the industry has already figured out

A quick lay of the land so we're not reinventing:

| Framework | What it is | What we take from it |
|---|---|---|
| **GitHub Spec Kit** | Slash-command workflow: `/constitution → /specify → /clarify → /plan → /tasks → /implement`. Constitution holds non-negotiable project principles. | The linear "Specify → Plan → Tasks → Implement" backbone, and the **constitution.md** concept for project-level guardrails. |
| **OpenSpec** | Lightweight SDD, brownfield-first. Separates `specs/` (source of truth) from `changes/` (proposals). Works with 20+ AI tools via AGENTS.md. | The **brownfield mindset** — most of our work isn't 0→1, it's adding features to Kedro/Viz/Builder. Plus the lightweight spec format (~250 lines vs Spec Kit's ~800). |
| **BMAD Method** | Persona-driven multi-agent: Analyst → PM → Architect → Scrum Master → Dev → QA. Each agent produces a verifiable artifact. | The **specialised agent roles** and the idea that each agent must produce a reviewable document, not a chat reply. |
| **Claude Code Subagents** | First-class subagent support: each gets its own context window, system prompt, and tool permissions. Defined in `.claude/agents/*.md`. | The **technical primitive** we'll use to implement the agent team. |
| **AGENTS.md / CLAUDE.md** | Convention for putting agent-readable project rules at the repo root. Works across Claude Code, Cursor, Copilot, etc. | The **portable contract** so we're not locked into one tool. |

We don't pick one of these. We borrow the parts that fit and skip the heavy machinery.

---

## The proposed workflow

Eight stages, organised into three loops. The arrows are where humans must approve before the next stage runs.

```
┌─────────────────────────────────────────────────────────────────┐
│  Loop 1: SHAPE (one-time per project / per major feature)       │
│                                                                 │
│   1. Constitution  →  2. Specify  →  3. Clarify  →  4. Plan     │
│      (rules)         (what/why)     (edge cases)    (how/phases)│
│         ↑                                              ↓        │
│         └──────────────  human review  ────────────────┘        │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│  Loop 2: BUILD (repeats per phase)                              │
│                                                                 │
│   5. Tasks  →  6. Implement  →  7. Verify  →  PROGRESS.md       │
│                                                                 │
│         ↑  human approves phase before next starts  ↓           │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│  Loop 3: SHIP (once all phases done)                            │
│                                                                 │
│   Demo → record Outcome → if keeping: Harden + Document         │
└─────────────────────────────────────────────────────────────────┘
```

### Stage 1 — Constitution (one-off per project)

**Goal:** Establish the non-negotiables before any feature work.

**Output:** `constitution.md` at the repo root, plus `CLAUDE.md` / `AGENTS.md` that point to it.

**What goes in:**

- Tech stack rails ("React 18 + Redux Toolkit, no class components, no legacy Redux patterns")
- Coding standards (TS strict, naming, folder structure)
- Project-specific rules ("must align with Kedro ecosystem conventions", "don't introduce new state libraries")
- Testing posture (unit tests required per phase, no skipped tests merged)
- "Don't overkill" clause (prefer simple solutions, no premature abstraction)

**Who owns it:** Architect / Tech Lead, reviewed by the team. Updated rarely.

**Time:** Half a day, once. Pays back forever.

### Stage 2 — Specify

**Goal:** Write down *what* we're building and *why*, never *how*.

**Output:** `specs/<feature>/spec.md`

**What goes in:**

- User story
- Acceptance criteria (testable, specific)
- Out-of-scope list (equally important)
- Visual reference if one exists (Figma link, sketch, screenshot)
- Success metric ("user can drag a node onto the canvas and it persists across reload")

**Who owns it:** Engineer driving the feature, optionally with PM input.

**Automation:** Agent can scaffold from a template, but the human writes the substance. Spec quality is the single biggest predictor of how well the rest of the workflow goes — this is the Kedro Builder lesson.

### Stage 3 — Clarify

**Goal:** Force ambiguities out before they become bugs.

**Output:** `specs/<feature>/clarifications.md` — questions and their answers, kept alongside the spec.

The agent reads the spec and asks structured questions: "What happens if the user drags a node that already exists?", "Should the autosave run on every change or debounced?", "What's the empty state?"

The human answers. Loop until the agent says "no further questions."

This is the stage we skipped on Kedro Builder, and it's why mid-implementation we kept finding plan/spec alignment gaps. Adding 30 minutes here saves 3 hours later.

### Stage 4 — Plan

**Goal:** Decide *how* we're building it. Tech stack, architecture, phase breakdown.

**Output:** `specs/<feature>/plan.md` + `specs/<feature>/phases/*.md`

**Sub-decisions, in order:**

1. **Tech stack** — versions, libraries, why. Reject overkill.
2. **Architecture** — types, state shape (e.g. Redux slices), folder structure, key interfaces.
3. **External dependencies** — list every library we'll lean on and flag the big ones (React Flow, PySpark) for the **Doc-Fetcher**. The fetch itself happens later — after the phase breakdown, just before the phase that needs each library — saving the API surface to `specs/<feature>/refs/<library>.md`. This is the trick that unblocked the canvas work on Kedro Builder.
4. **Phase breakdown** — slice the work so each phase fits a single Claude Code session. Rule of thumb: a phase is ~200–600 lines of new code, or one cohesive layer (types, then state, then UI, then integration).
5. **Cross-phase contracts** — the types and interfaces that get fixed in Phase 1 and that all later phases honour.

**Time investment:** This is where you spend 30–40% of the total project time. Kedro Builder spent 4–5 days here. That was not a bug.

**Iteration:** Expect 2–4 plan revisions before it's locked. Plan reviews are a human responsibility — agents will write a plausible-sounding plan that's subtly wrong.

### Stage 5 — Tasks

**Goal:** Break each phase into atomic, reviewable tasks.

**Output:** `specs/<feature>/phases/phase-N-tasks.md`

Each task is:

- One commit's worth of work
- Has an acceptance check ("tests pass + this specific behaviour observable")
- Lists the files it will touch
- Tagged with the phase ID for traceability

**Automation:** Tasks can be generated by an agent from `plan.md` + the phase definition. Human spot-checks before kicking off the phase.

### Stage 6 — Implement (per phase)

**Goal:** Build one phase, end-to-end, in one focused session.

**Inputs the implementer agent reads:**

- `constitution.md`
- `spec.md`
- The current phase's plan and tasks
- `PROGRESS.md` (state from previous phases)
- Any relevant `refs/<library>.md`

**Outputs:**

- Code
- Unit tests for the phase
- An updated `PROGRESS.md` written at session end:
  - Files created / modified
  - Public interfaces added
  - Decisions made (and why)
  - Anything deferred to a later phase
  - Known issues

**Hard rule:** No phase ships without `PROGRESS.md` updated. This is the single most important habit — it's what lets the next session start cold and stay accurate. We learned this the hard way on Kedro Builder; codifying it makes it routine.

### Stage 7 — Verify (per phase)

**Goal:** Catch problems while they're small.

Three checks, automated by agents but signed off by human:

1. **Tests pass.** All of them, including previous phases' tests. No flaky-skip merges.
2. **Visual check** (frontend only). The Visual-Check agent takes a screenshot via Playwright/MCP and compares against the brand reference. Kedro Builder skipped this and paid for it.
3. **Spec drift check.** The Reviewer agent re-reads the spec and confirms the phase's output matches the acceptance criteria. If it doesn't, either the spec changes (deliberate) or the code changes (corrective).

### Stage 8 — Harden (only if the prototype lives on)

Once all phases are done and approved, demo the prototype and record the **Outcome** in `PROGRESS.md` — an explicit Continue / Rework / Stop / Productise decision. A prototype that taught us nothing on record taught us nothing.

If the verdict is **Rework or Stop**, you're done — don't polish a prototype you're not keeping.

If the verdict is **Continue or Productise**, harden it with three refactor passes (the Kedro Builder pattern, now codified):

1. **Duplication pass** — Reviewer agent finds and reports duplicate logic; Refactorer agent consolidates with tests as guardrail.
2. **Dead-code pass** — anything generated and then abandoned across phases.
3. **Simplification pass** — readability, naming, file-size sanity.

Behaviour must not change. Tests are the contract. Then generate `ARCHITECTURE.md` (the living map of the system), and update `CLAUDE.md` if patterns emerged that future work should follow.

---

## The agent team

Six agents (plus an optional seventh for frontend work), each with one clear, non-overlapping job — defined as Claude Code subagents in `.claude/agents/` and committed so the whole team shares them. The full table (who does what, when to invoke, what each produces) is the [agent-team section of the README](../README.md#the-agent-team) — that's the reference you'll return to. The argument for the line-up is below.

### Why six and not 11 or 20

We don't need a sprawling cast. The wshobson "185 agents" library on GitHub exists, but most of those overlap heavily. Six is the smallest number that maps cleanly onto the phases of work and gives each agent a clear, non-overlapping job. The Anthropic guidance is consistent: **fewer, sharper agents beat more, fuzzier ones.**

---

## What to automate vs where humans matter

The honest breakdown:

### Fully automated (the agent owns it end-to-end)

- Spec template scaffolding
- Library doc fetching and summarisation
- Boilerplate / Phase 1 type and folder setup
- Generating unit tests from acceptance criteria
- Mechanical refactors (duplication, dead code) with tests as guardrail
- Generating `ARCHITECTURE.md` and `PROGRESS.md`
- Cross-artifact consistency checks (does the plan reflect the spec? do tasks cover the plan?)

### Human-led, AI-assisted

- Writing the actual user story and acceptance criteria — AI can polish but cannot invent intent
- Clarification rounds — AI asks, human decides
- Tech stack choices (the team has opinions; the agent has none)
- Plan reviews — this is where bad assumptions get caught
- Visual / UX alignment when there's no design reference
- Final code review before merge

### Non-negotiable human gates

These are the four moments where the workflow stops and waits for a human:

1. **Constitution approval** — the rules of the road
2. **Spec sign-off** — what we're building
3. **Plan review** — how we're building it
4. **Per-phase approval** — proof this phase actually works before the next one starts

Skip any of these and the workflow degrades into vibe coding with extra steps.

---

## Making it work for backend / Python / data pipelines

The whole workflow is stack-agnostic. For a Kedro plugin, a PySpark prototype, or an API service, the same eight stages apply, with three swaps:

| Stage | Frontend swap | Backend / Python swap |
|---|---|---|
| Constitution | "React 18, Redux Toolkit, TS strict" | "Python 3.11, type hints, ruff, Kedro patterns" |
| Plan — architecture | Component tree, Redux slices, routes | Module layout, data contracts, pipeline graph |
| Verify — visual check | Screenshot + brand compare | Pipeline smoke-run on sample data + schema validation |
| Doc-Fetcher targets | React Flow, shadcn, Tailwind | PySpark, Kedro DataSets, pandas, Pydantic |

That's it. The shape of the workflow doesn't change. The Architect and Planner agents pull from a different constitution; the Implementer reads different reference docs. Everything else is identical.

This matters because most of our team's work is data-pipeline-shaped, not UI-shaped. A workflow that only works for React would be useless.

---

## Team adoption roadmap

A six-week rollout. Slow enough to learn, fast enough to feel real.

### Week 1 — Pilot of one

- One engineer (volunteer) picks one greenfield feature
- Uses the workflow end-to-end with the six agents
- Captures friction in a journal
- Goal: prove the loop works on something small (~3 phases)

### Week 2 — Templates and tooling

- Build the shared `specs/` and `.claude/agents/` directories
- Draft `constitution.md` for each repo type:
  - `kedro-viz` (React/TS frontend)
  - `kedro-builder` (React/TS app)
  - `kedro-plugins` (Python)
  - `kedro` core (Python library)
- Stand up a `prototyping-workflow/` repo with the templates and agent definitions

### Week 3–4 — Two-team pilot

- One frontend feature, one backend feature, both using the workflow
- Bi-weekly retro: what cost too much time? what saved time?
- Refine agent definitions based on real failures

### Week 5 — Broaden

- All new prototypes use the workflow
- Existing in-flight work continues as is
- Pair each first-timer with a pilot-week engineer

### Week 6 — Stabilise

- Documentation goes into the team handbook
- `constitution.md` files are reviewed and merged
- Workflow becomes the default for any "let's prototype X" conversation

### After

- Quarterly review of agent definitions (what's drifted, what's been bypassed)
- Skills library grows organically — anything reusable across projects gets promoted to a shared skill

---

## Risks and how we handle them

**Risk: spec fatigue.** Engineers stop writing specs because they feel like paperwork.
- *Mitigation:* Keep specs short (OpenSpec-style, ~250 lines max). The spec is for the agent to read, not for an audit. If it's longer than the feature, it's wrong.

**Risk: spec drift.** The code diverges from the spec mid-build and nobody updates the spec.
- *Mitigation:* The Reviewer agent runs a spec-drift check at the end of every phase. Drift is either corrected (spec was right) or absorbed (code was right and spec updates).

**Risk: the workflow becomes the work.** Spending more time on ceremony than coding.
- *Mitigation:* The workflow has an explicit "lean path" — for genuinely tiny features, skip the Clarify stage (no `clarifications.md`) and collapse the plan to a single phase with one lightweight `phase-1-tasks.md`. You still go Spec → Plan → Implement, and the implementer still gets its one required task file. Reserve the full pipeline for anything you'd hesitate to one-shot.

**Risk: tool lock-in.** What if Claude Code changes or we want to use Cursor?
- *Mitigation:* Everything lives as plain markdown in the repo (`AGENTS.md`, `CLAUDE.md`, `constitution.md`, `spec.md`, `plan.md`, `PROGRESS.md`). Tool-specific config is minimal. The work product is portable.

**Risk: junior engineers becoming passive.** Just clicking "approve" without thinking.
- *Mitigation:* The four human gates require a written justification, not a thumbs-up. Code review still happens. The agent is a multiplier, not a replacement.

---

## Closing thought

What we built on Kedro Builder wasn't lucky. It was a workflow we discovered as we went — spec, plan, phase, handoff, refactor. The proposal here is to stop rediscovering it every time and turn it into the team's default.

The agents do the typing. The humans do the deciding. That balance is the whole point.
