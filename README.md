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
│  Loop 3: SHIP (once all phases done)                            │
│                                                                 │
│   Demo → record Outcome → if keeping: Harden + Document         │
└─────────────────────────────────────────────────────────────────┘
```

---

## What's in this repo

```
ai-prototyping-workflow/
├── README.md                 ← you are here
├── constitution.md           ← fork this per project; defines the rules
├── AGENTS.md / CLAUDE.md     ← agent contract (AGENTS.md is source; CLAUDE.md points to it)
├── .claude/agents/           ← 6 core agents + 1 optional (visual-check)
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
│   ├── PROGRESS.md           ← living state + final outcome decision
│   └── ARCHITECTURE.md       ← generated at the end (if the prototype continues)
└── docs/
    ├── how-it-works.md       ← plain-English orientation; start here
    ├── quickstart.md         ← walkthrough of your first prototype
    └── full-proposal.md      ← the full reasoning, for depth
```

---

## The agent team

Six agents. Each has one clear job and produces a reviewable artefact, not a chat reply.

| Agent | Job | When to invoke | Produces |
|---|---|---|---|
| **analyst** | Validate the user story; ask clarifying questions | Stages 2–3 (Specify, Clarify) | `spec.md`, `clarifications.md` |
| **architect** | Decide tech stack, types, state shape, folder structure | Stage 4 (Plan) | `plan.md` architecture sections |
| **planner** | Break the plan into phases that fit one context window | Stage 4–5 (Plan, Tasks) | Phase breakdown + `phase-N-tasks.md` |
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
| 3 | **Clarify** | Analyst agent | `clarifications.md` |
| 4 | **Plan** | Architect + Planner + Doc-Fetcher agents | `plan.md` + `phases/` |
| 5 | **Tasks** | Planner agent | `phase-N-tasks.md` |
| 6 | **Implement** (per phase) | Implementer agent | Code + tests + `PROGRESS.md` updated |
| 7 | **Verify** (per phase) | Reviewer agent (+ Visual-Check if UI) | Spec-drift review; phase signed off |
| 8 | **Harden** (only if kept) | Reviewer agent | Clean code + `ARCHITECTURE.md` |

> After all phases pass, demo the prototype and record the **Outcome** (Continue / Rework / Stop / Productise) in `PROGRESS.md`. Stage 8 runs only if that verdict is Continue or Productise — don't polish a prototype you're not keeping.

---

## Human gates — non-negotiable

These are the four moments where the workflow stops and waits for a human. Skip any of them and the workflow degrades into vibe coding with extra steps.

1. **Constitution approval** — the rules of the road
2. **Spec sign-off** — what we're building
3. **Plan review** — how we're building it (expect 2–4 revisions; this is real engineering time)
4. **Per-phase approval** — proof this phase works before the next one starts

---

## Getting started — your first prototype

1. **Fork this repo** into your project.
2. **Edit `constitution.md`** to match your stack (Python? TypeScript? Kedro?).
3. **Copy `specs/_template/` to `specs/<your-feature>/`** and fill in `spec.md`.
4. **Open Claude Code** in the repo. The agents in `.claude/agents/` are auto-discovered.
5. **Run the loop**: `analyst` validates the spec → `architect` plans → `planner` phases it → `implementer` builds each phase (the `reviewer` checks each) → demo and record the Outcome → `reviewer` hardens **only if you're keeping it**.
6. **Update `PROGRESS.md`** after every phase. This is the single most important habit.

New to the workflow? Read [`docs/how-it-works.md`](docs/how-it-works.md) first — a plain-English walkthrough with a diagram. Then [`docs/quickstart.md`](docs/quickstart.md) for the exact commands, and [`docs/full-proposal.md`](docs/full-proposal.md) for the reasoning behind every design choice.

---

## Status

**Proposal v1.** Open for team feedback before we adopt as default.

Ping in `#kedro-eng` or comment on the PR.
