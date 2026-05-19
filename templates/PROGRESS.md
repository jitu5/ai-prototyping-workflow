# PROGRESS

> The living state of this prototype across phases. Updated by the implementer at the end of every phase. Read by every subagent at the start of every session.
>
> This file replaces "remembering" across context windows. If it's not in PROGRESS.md, assume the next session doesn't know it.

---

## Current state

**Feature:** <feature name>
**Current phase:** Phase N
**Last updated:** YYYY-MM-DD
**Status:** In progress / Blocked / Complete

## What currently works

End-user-visible behaviour that exists in the prototype right now.

- ...
- ...

## Phase log

### Phase 1 — Foundation — 2025-MM-DD

**Status:** Complete

**Files created or changed:**

| File | Purpose | Notes |
|---|---|---|
| `src/types/feature.ts` | Domain types | Locked: do not change in later phases |
| ... | ... | ... |

**Key decisions:**

| Decision | Reason | Trade-off |
|---|---|---|
| Use Redux Toolkit slices | Matches Kedro-Viz pattern | Slight boilerplate |
| ... | ... | ... |

**Tests added:**
- `feature.test.ts` — covers types and slice reducers

**Known issues:**

| Issue | Impact | Follow-up |
|---|---|---|
| ... | ... | ... |

**Deferred work:**
- ...

**Hand-off to next phase:**
What the Phase 2 implementer needs to know to start cold.

**Verification run:**
- [x] Tests pass
- [x] Build passes
- [x] Linting passes

---

### Phase 2 — <Name> — YYYY-MM-DD

_(template repeats per phase)_

---

## Cross-phase contracts

Interfaces and types locked in early phases that later phases must honour. Changes here require a plan update.

```typescript
// from Phase 1, src/types/feature.ts
interface FeatureItem {
  id: string;
  // ...
}
```

## Open questions across phases

Anything still ambiguous that affects more than one phase.

- ...

## Do not change

Important constraints to preserve across all phases.

- Existing public API of `<module>` — external consumers depend on it
- ...
