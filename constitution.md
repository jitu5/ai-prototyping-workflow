# Project Constitution

> Non-negotiable rules for this project. Read this before any planning or implementation.

This is a **sample constitution**. Fork it per project and adapt the specifics. The structure should stay; the contents should be tuned to your stack and team.

---

## Project principles

- **Prefer simple solutions** over premature abstraction. Two duplicated lines is fine; a needless framework is not.
- **No new major dependencies** without justification in `plan.md`. Use what the ecosystem already has.
- **Generated code must be readable and reviewable.** If a human can't review it in 10 minutes, the chunk is too big.
- **Prototypes optimise for learning**, not production completeness. Cut scope before cutting corners.
- **Preserve existing behaviour during refactors.** Tests are the contract.
- **Honour the ecosystem.** For Kedro work, follow Kedro conventions. For Kedro-Viz work, match its patterns.

---

## Tech stack rails

> Edit this section per project. The samples below are illustrative.

### For TypeScript/React projects

- TypeScript with `strict: true`
- React 18+, function components only, no class components
- Redux Toolkit (no legacy Redux, no `connect`, no `mapStateToProps`)
- Vite for build, Vitest for tests
- ESLint + Prettier with the team config

### For Python projects

- Python 3.11+
- Type hints everywhere; `mypy --strict` for libraries
- `ruff` for linting and formatting (replaces black, isort, flake8)
- `pytest` for tests
- For Kedro work: `kedro >= 0.19`, standard project structure

### For both

- No new state libraries, no new build tools, no new test frameworks without `plan.md` approval.
- Match the patterns of the parent repo. New file should look like its neighbours.

---

## Testing rules

- Tests are required for core logic, not for trivial getters.
- Run tests before declaring a phase complete. A phase is not done if tests are red.
- Do not skip or delete existing tests to make them pass. Fix the test or fix the code.
- New behaviour requires a new test.

---

## AI-agent rules

These apply to every agent in `.claude/agents/`.

- **Read before write.** Always read `constitution.md`, the relevant `spec.md`, and `PROGRESS.md` before making changes.
- **One phase at a time.** Implementer agents work on the current phase only — no scope creep into future phases.
- **Don't change unrelated files.** If a refactor is needed elsewhere, flag it in `PROGRESS.md` for a later refactor pass.
- **Update `PROGRESS.md` at session end.** Files changed, decisions made, deferred work, known issues.
- **Ask when in doubt.** Conflicting requirements get a clarifying question, not a guess.
- **Don't overkill.** Match the complexity of the solution to the complexity of the problem. Prototype ≠ production.

---

## Definition of done

A phase is done when:

- [ ] All acceptance criteria for the phase pass
- [ ] All existing tests still pass
- [ ] New behaviour has a test
- [ ] `PROGRESS.md` is updated
- [ ] No unrelated files were modified (or if they were, flagged with reason)

The whole prototype is done when:

- [ ] All phases are complete
- [ ] The Outcome (Continue / Rework / Stop / Productise) is recorded in `PROGRESS.md` after the demo
- [ ] If the outcome is Continue or Productise: refactor passes (duplication, dead code, simplification) are done with tests green, and `ARCHITECTURE.md` is generated

---

## Governance

This constitution supersedes individual preferences. Changes require:

1. A PR to this file
2. Approval from the tech lead
3. A note in `PROGRESS.md` if an in-flight prototype is affected

When the AI proposes something that conflicts with this document, the constitution wins.
