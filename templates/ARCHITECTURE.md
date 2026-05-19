# Architecture

> Generated at the end of the prototype by the reviewer agent. The map for anyone joining the project later or coming back to it months from now.

---

## Overview

One paragraph: what this system does, who it's for, what problem it solves.

## High-level structure

```
[Frontend] → [State store] → [API layer] → [Backend / external services]
```

Or for a Python / data prototype:

```
[Sources] → [Kedro pipeline] → [Catalog] → [Outputs]
```

## Main modules

| Module | Purpose | Lives in |
|---|---|---|
| Types | Domain types shared across the feature | `src/features/<feature>/types.ts` |
| Store | State management | `src/features/<feature>/store/` |
| Components | UI components | `src/features/<feature>/components/` |
| Hooks | Reusable logic | `src/features/<feature>/hooks/` |
| API | Backend communication | `src/features/<feature>/api/` |

## Data flow

How data moves through the system. A short paragraph plus a diagram if helpful.

```
User action
  → component event handler
  → dispatch action
  → reducer updates state
  → selector returns new value
  → component re-renders
```

## State flow

How state changes. What's global, what's local, what's persisted.

- **Global state:** ... (lives in `store/`)
- **Feature state:** ... (lives in `features/<feature>/store/`)
- **Local UI state:** ... (component-level, useState)
- **Persisted:** ... (localStorage, backend)

## Important files

The files a new contributor should read first.

| File | Why it matters |
|---|---|
| `src/features/<feature>/index.ts` | Public entry point |
| `src/features/<feature>/types.ts` | The domain model |
| `src/features/<feature>/store/featureSlice.ts` | State logic |
| ... | ... |

## Key technical decisions

The decisions that shaped this prototype, with reasons. Cross-reference `PROGRESS.md` for the full history.

| Decision | Reason | Alternative considered |
|---|---|---|
| Redux Toolkit | Matches Kedro-Viz pattern | Zustand, Context API |
| ... | ... | ... |

## External dependencies

| Library | Used for | Version |
|---|---|---|
| ... | ... | ... |

## Known limitations

What this prototype intentionally does not do. From the spec's "Non-goals" plus anything discovered along the way.

- ...
- ...

## Future improvements

If this prototype is continued, what's next:

- ...
- ...

## How to run locally

```bash
# install
...

# run
...

# test
...
```

## How to extend

If you want to add a new feature here, the rough shape is:

1. ...
2. ...
3. ...
