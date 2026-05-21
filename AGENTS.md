# Agent Instructions

> Read this file before making any changes in this repository. This is the contract.

## What this project is

A prototype built using the AI-Native Prototyping Workflow. See `README.md` for the workflow overview and `docs/full-proposal.md` for the reasoning.

## Required reading

`constitution.md` applies to every agent at every stage — read it first, always.

Before writing any code (the implementer's job), also read, in this order:

1. `specs/<feature>/spec.md` — what we're building and why
2. `specs/<feature>/plan.md` — how we're building it
3. `specs/<feature>/phases/phase-N-tasks.md` — the current phase only
4. `PROGRESS.md` — what's already been done and what's deferred

If any of these are missing at implementation time, stop and ask which stage hasn't been done yet. Earlier stages naturally run before some of these exist (the analyst writes the spec; the architect and planner write the plan and phases) — each reads whatever is already present.

> `PROGRESS.md` is the living state file. In this starter repo it ships as `templates/PROGRESS.md`; in a prototype repo, copy it to the root before implementation begins (see `docs/quickstart.md` Step 0).

## Working rules

- **Work on the current phase only.** Future phases are out of scope until the current one is approved.
- **Don't change unrelated files.** If a refactor seems needed elsewhere, log it in `PROGRESS.md` under "Deferred work" and leave it.
- **Don't introduce new libraries** unless `plan.md` already lists them.
- **Keep diffs small and reviewable.** A phase commit should be reviewable by a human in 10 minutes.
- **Preserve existing behaviour during refactors.** Tests are the contract.
- **Use the right subagent for the job.** See the agent table below. Don't have the implementer do clarification work, or the analyst write code.
- **End every response with a next-step footer.** Each agent closes with `✅ Done` / `🚦 Your call` / `➡️ Next` so the human always knows the next action and never has to guess which agent or gate comes next.

## Subagent reference

The agents below live in `.claude/agents/`. Invoke them by name.

| Agent | Use for |
|---|---|
| `analyst` | Validating a user story, asking clarifying questions, updating `spec.md` |
| `architect` | Tech stack, types, state shape, folder structure, the architecture section of `plan.md` |
| `planner` | Breaking the plan into phases, generating `phase-N-tasks.md` |
| `doc-fetcher` | Fetching and summarising external library docs before a phase uses them |
| `implementer` | Building one phase end-to-end with tests |
| `reviewer` | Spec-drift check per phase, refactor passes at the end |
| `visual-check` | (Frontend only) Screenshot + brand alignment |

## Completion rules

Before declaring any phase complete, the implementer must:

- Summarise what changed in `PROGRESS.md`
- List every file that was created or modified
- Confirm that all tests run and pass
- List any deferred work or known issues
- List any decisions made and why

If any of these is missing, the phase is not complete.

## When to ask

Ask a clarifying question rather than guess when:

- Two parts of the spec or plan conflict
- Acceptance criteria are ambiguous
- A library decision isn't covered by the plan
- A scope question isn't covered by the spec
- Something feels wrong but isn't explicitly forbidden

Don't ask permission for things that are clearly inside the current phase's scope.
