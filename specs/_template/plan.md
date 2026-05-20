# Implementation Plan: <Feature Name>

> How we're building it. Owned by the architect (architecture sections) and planner (phase breakdown onward) agents. Reviewed by a human before any code is written.

---

## Overview

One paragraph summarising the approach.

## Tech stack

Every choice gets a one-line justification. Honour the constitution.

- **Frontend:** ... — _why_
- **Backend:** ... — _why_
- **Storage:** ... — _why_
- **Testing:** ... — _why_
- **Build / deploy:** ... — _why_

## Core types / data model

The domain objects this feature operates on.

```typescript
// or Python dataclasses, or Pydantic models, depending on stack
interface FeatureItem {
  id: string;
  // ...
}
```

## State model

Where state lives. Who owns it. How it flows.

- Global state: ...
- Feature state: ...
- Local UI state: ...

## Folder structure

Where each piece lives. Match the parent repo's conventions — the tree below is just one shape.

```
<feature>/
├── <entry point>
├── <core types / data model>
├── <logic — services / state / pipeline>
└── <tests>
```

For a React feature this is usually `components/`, `hooks/`, `store/`, `types.ts`; for a Python package, `__init__.py`, `models.py`, `pipeline.py`, `tests/`.

## External dependencies

| Library | Version | Used for | Doc-fetch needed? |
|---|---|---|---|
| ... | ... | ... | yes / no |

## Risks

| Risk | Impact | Mitigation |
|---|---|---|
| ... | ... | ... |

## Simplifications

What we are deliberately not building. List explicitly so the implementer doesn't add them anyway.

- We will mock X
- We will skip Y
- We will not implement Z

---

## Phase breakdown

> Filled in by the planner agent.

### Phase 1 — Foundation
- **Goal:** Project scaffold, core types, empty containers that build
- **Deliverable:** App compiles, types exported, basic layout renders
- **Verification:** `npm run build` passes, types compile, basic layout visible
- **Cross-phase contracts:** The interfaces defined here are locked for all later phases

### Phase 2 — <Name>
- **Goal:** ...
- **Depends on:** Phase 1
- **Deliverable:** ...
- **Verification:** ...

### Phase 3 — <Name>
- **Goal:** ...
- **Depends on:** Phase 2
- **Deliverable:** ...
- **Verification:** ...

_(add as many phases as needed; 1–12, most features at 3–8 — a tiny prototype can be a single phase)_

---

## Open questions

Things still ambiguous after architect/planner work that need a human answer before implementation starts.
