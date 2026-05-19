# AI-Native Prototyping Workflow

> A team workflow for building prototypes with AI coding agents — spec-driven, phase-based, repeatable.

---

## What this is

A working starter kit for our team to adopt **spec-driven development** with AI coding agents (Claude Code, Cursor, Copilot, etc.). Fork it per project, follow the workflow, and you'll ship prototypes 5–10× faster without sacrificing code quality or team handoff.

It is based on what worked when we built [Kedro Builder](https://github.com/kedro-org/kedro-builder) end-to-end with GenAI in two weeks, plus the parts the industry has already standardised: GitHub Spec Kit, OpenSpec, BMAD, and Claude Code subagents.

---

## The three commitments

1. **Spec before code, always.** No agent writes a line until the human and the AI agree on what's being built.
2. **Phases that fit one context window.** Long sessions drift; short, well-scoped phases stay sharp.
3. **A small team of specialised agents**, not one generalist that does everything.

> The agents do the typing. The humans do the deciding. That balance is the whole point.

---

## The workflow at a glance

```
┌─────────────────────────────────────────────────────────────────┐
│  Loop 1: SHAPE (one-time per feature)                           │
│                                                                 │
│   1. Constitution → 2. Specify → 3. Clarify → 4. Plan           │
│      (rules)        (what/why)   (edge cases)  (how/phases)     │
│         ↑                                              ↓        │
│         └──────────  human review at each gate  ───────┘        │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│  Loop 2: BUILD (repeats per phase)                              │
│                                                                 │
│   5. Tasks → 6. Implement → 7. Verify → PROGRESS.md updated     │
│                                                                 │
│         ↑  human approves phase before next starts  ↓           │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│  Loop 3: HARDEN (once all phases done)                          │
│                                                                 │
│   8. Refactor & Document → ARCHITECTURE.md                      │
└─────────────────────────────────────────────────────────────────┘
```

---

## What's in this repo

```
ai-prototyping-workflow/
├── README.md                 ← you are here
├── constitution.md           ← fork this per project; defines the rules
├── AGENTS.md / CLAUDE.md     ← tells AI agents how to behave in the repo
├── .claude/agents/           ← the 6 agent definitions (subagents)
│   ├── analyst.md
│   ├── architect.md
│   ├── planner.md
│   ├── doc-fetcher.md
│   ├── implementer.md
│   ├── reviewer.md
│   └── visual-check.md       ← optional, for frontend work
├── specs/_template/          ← copy this into specs/<your-feature>/
│   ├── spec.md
│   ├── plan.md
│   └── phases/phase-template.md
├── templates/
│   ├── PROGRESS.md           ← living state across phases
│   └── ARCHITECTURE.md       ← generated at the end
└── docs/
    ├── full-proposal.md      ← the full reasoning, for depth
    └── quickstart.md         ← walkthrough of your first prototype
```

---

## The agent team

Six agents. Each has one clear job and produces a reviewable artefact, not a chat reply.

| Agent | Job | When to invoke | Produces |
|---|---|---|---|
| **analyst** | Validate the user story; ask clarifying questions | Stages 2–3 (Specify, Clarify) | `spec.md`, clarifications |
| **architect** | Decide tech stack, types, state shape, folder structure | Stage 4 (Plan) | `plan.md` architecture sections |
| **planner** | Break the plan into phases that fit one context window | Stage 4–5 (Plan, Tasks) | Phase breakdown + `tasks.md` |
| **doc-fetcher** | Fetch and summarise external library APIs | Before any phase with a non-trivial dependency | `specs/<feature>/refs/<lib>.md` |
| **implementer** | Build one phase end-to-end with tests | Stage 6 (Implement) | Code, tests, `PROGRESS.md` update |
| **reviewer** | Spec-drift check per phase, refactor passes at the end | Stages 7–8 (Verify, Harden) | Review notes, refactor commits |
| **visual-check** (optional) | Screenshot + brand alignment for UI work | After any UI-touching phase | Visual diff report |

Why six and not twenty: fewer, sharper agents beat more, fuzzier ones. The Anthropic guidance is consistent on this.

---

## The eight stages

| # | Stage | Owned by | Output |
|---|---|---|---|
| 1 | **Constitution** (one-off per project) | Architect / Tech Lead | `constitution.md` |
| 2 | **Specify** | Engineer + Analyst agent | `spec.md` |
| 3 | **Clarify** | Analyst agent | clarifications appended to `spec.md` |
| 4 | **Plan** | Architect + Planner + Doc-Fetcher agents | `plan.md` + `phases/` |
| 5 | **Tasks** | Planner agent | `phase-N-tasks.md` |
| 6 | **Implement** (per phase) | Implementer agent | Code + tests |
| 7 | **Verify** (per phase) | Reviewer agent (+ Visual-Check if UI) | `PROGRESS.md` updated |
| 8 | **Harden** | Reviewer agent | Clean code + `ARCHITECTURE.md` |

---

## Human gates — non-negotiable

These are the four moments where the workflow stops and waits for a human. Skip any of them and the workflow degrades into vibe coding with extra steps.

1. **Constitution approval** — the rules of the road
2. **Spec sign-off** — what we're building
3. **Plan review** — how we're building it (expect 2–4 revisions; this is real engineering time)
4. **Per-phase approval** — proof this phase works before the next one starts

---

## What we automate vs where humans matter

**Fully automated** (agent owns it): spec scaffolding, library doc fetching, boilerplate, test generation from acceptance criteria, mechanical refactors, doc generation, cross-artifact consistency checks.

**Human-led, AI-assisted**: writing the actual user story, clarification answers, tech stack choices, plan reviews, visual/UX alignment, final code review.

The honest split is about 70/30 — AI does most of the typing; humans do most of the deciding.

---

## Works for any stack

The workflow is stack-agnostic. For a Kedro plugin, a PySpark prototype, or a backend API, the same eight stages apply with three swaps:

| Stage | Frontend | Backend / Python |
|---|---|---|
| Constitution | "React 18, TS strict, Redux Toolkit" | "Python 3.11, type hints, ruff, Kedro patterns" |
| Plan — architecture | Component tree, Redux slices, routes | Module layout, data contracts, pipeline graph |
| Verify | Screenshot + brand compare | Pipeline smoke-run + schema validation |
| Doc-Fetcher targets | React Flow, shadcn, Tailwind | PySpark, Kedro DataSets, pandas, Pydantic |

The shape of the workflow does not change. This matters because most of our team's work is data-pipeline-shaped, not UI-shaped.

---

## Getting started — your first prototype

1. **Fork this repo** into your project.
2. **Edit `constitution.md`** to match your stack (Python? TypeScript? Kedro?).
3. **Copy `specs/_template/` to `specs/<your-feature>/`** and fill in `spec.md`.
4. **Open Claude Code** in the repo. The agents in `.claude/agents/` are auto-discovered.
5. **Run the loop**: ask the `analyst` to validate your spec, then the `architect` to plan, then the `planner` to phase it, then the `implementer` (one phase at a time), then the `reviewer` to harden.
6. **Update `PROGRESS.md`** after every phase. This is the single most important habit.

For a complete walkthrough, see [`docs/quickstart.md`](docs/quickstart.md). For the full reasoning behind every design choice, see [`docs/full-proposal.md`](docs/full-proposal.md).

---

## Team adoption — six weeks

| Week | Goal |
|---|---|
| **1** | Pilot of one. One engineer, one greenfield feature, journal the friction. |
| **2** | Build shared templates. Constitutions per repo type (Kedro-Viz, Builder, plugins, core). |
| **3–4** | Two-team pilot. One frontend, one Python. Bi-weekly retro. |
| **5** | Broaden — all new prototypes use the workflow. |
| **6** | Stabilise — workflow becomes the default. Quarterly review thereafter. |

---

## Why not just keep vibe coding?

Anyone who has tried building something non-trivial with AI agents has hit the same wall: the first hour is magic, the fourth hour the model has forgotten decisions it made an hour ago. Without a written spec, the agent's interpretation drifts every session. Without phases, context rots. Without specialist agents, one over-eager generalist over-engineers everything.

This workflow is the smallest set of habits that fixes those problems. It is not bureaucracy — it is the engineering process that makes AI speed actually compound instead of creating tech debt at machine speed.

---

## Status

**Proposal v1.** Open for team feedback before we adopt as default.

Ping in `#kedro-eng` or comment on the PR.
