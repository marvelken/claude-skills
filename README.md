# Claude Skills — Acme Project Manager

A set of Claude Code skills purpose-built for this Next.js 16 / React 19 / Zustand / Tailwind v4 codebase. Each skill encodes the project's actual conventions so Claude doesn't have to rediscover them on every task.

---

## Quick reference

| Skill | Command | When to use |
|-------|---------|-------------|
| Plan with me | `/plan-with-me` | Before any feature that touches more than one file |
| Scaffold component | `/scaffold-component [Name]` | Creating a new React component |
| Generate tests | `/gen-tests [ComponentName]` | Writing or extending tests |
| Review PR | `/review-pr [branch or PR#]` | Reviewing code before merge |
| API pattern | `/api-pattern [description]` | Adding any data-fetching logic |
| CSS first | `/css-first` | Replacing JS with CSS, or auditing visual logic |
| Update project memory | `/update-project-memory` | After any structural file change |

---

## Skills

### `/plan-with-me`

Runs an interview before writing a single line of code. Claude asks questions about scope, data shape, state ownership, client/server split, error states, and responsive behavior — and provides a recommended answer for each so you can say "yes" to obvious ones. Only questions the codebase can't answer get asked.

After the interview, produces:

- Agreement summary
- Task breakdown with exact file paths
- Blocking relationships (what can be parallelized)
- Deferred decisions

No code is written until you explicitly approve the plan.

**Triggers on:** "build", "create", "add", "implement", "new feature", "new page", or any multi-file task.

---

### `/scaffold-component [ComponentName]`

Creates a new component that matches the project's conventions:

- Places it in `src/app/dashboard/components/` (flat structure, no subdirectories)
- Names the props interface `{ComponentName}Props`
- Defaults to `'use client'` (all existing components are client components) unless there's a clear server-component case
- Only uses Tailwind classes from the documented token list
- Creates a `.test.tsx` stub alongside the component
- Runs `tsc --noEmit` before reporting done

No barrel exports. Direct imports only.

---

### `/gen-tests [ComponentName or path]`

Generates behavior-driven tests using Vitest + React Testing Library.

**Hard rules baked in:**

- Tests describe what a user observes, not what React holds internally
- `userEvent` always, never `fireEvent`
- `within()` used when text appears in multiple DOM locations
- `getByText` only when the text is unique in the rendered output
- Never asserts on `useState` values or `useRef` contents
- Appends to existing `.test.tsx` stubs rather than overwriting them

This project has no test framework yet. First run installs Vitest + RTL and scaffolds the config.

---

### `/review-pr [branch or PR number]`

Reviews a diff against the project's documented conventions. Reads `PROJECT-STATE.md` and `CONVENTIONS.md` before looking at a single line of code.

**Checks:**

- Naming (PascalCase components, `{Name}Props` interfaces, camelCase utilities)
- Imports (no barrels, `@/` alias, `import type` for types)
- State management (no Context, minimum store selectors, prop drilling depth)
- Type safety (no `any`, no unguarded assertions)
- Component structure (`'use client'` placement, one export per file)
- Tailwind (no arbitrary values, `sm:`/`lg:` only)
- Performance (store over-subscription)

Each finding includes: file/line, one-sentence description, code fix, severity (`must fix` / `should fix` / `nit`). Empty categories are skipped entirely.

---

### `/api-pattern [describe the data]`

Enforces correct data-fetching patterns before any fetch code is written.

**Decision tree:**

1. Is it client state (interaction-derived, no server round-trip)? → Extend the Zustand store in `src/lib/store.ts`
2. Is it server state that doesn't change client-side? → Next.js Server Component
3. Is it server state that's user-triggered or real-time? → TanStack Query

**Hard rules:**

- No `fetch` inside `useEffect`
- No server state in `useState`
- All fetches include cancellation via `AbortController` or library signal
- Every data-fetching component handles: loading, error, empty, and success states
- Optimistic updates require a rollback path

This project currently has no API integration — all data is local mock data in `src/lib/data.ts`.

---

### `/css-first`

Audits visual behavior and replaces JavaScript with native CSS where possible.

**Substitution table:**

| Instead of | Use |
|-----------|-----|
| `ResizeObserver` / JS breakpoints | `@container` queries (93%+ support) |
| `scroll` event listeners | Scroll-driven animations (76%+) |
| JS virtualization for <10k items | `content-visibility: auto` (85%+) |
| JS parent-state toggling | `:has()` selector (93%+) |
| JS route animations | View Transitions API (80%+) |
| `marginLeft` / `paddingTop` | Logical properties `margin-inline` / `padding-block` (96%+) |

JavaScript is only allowed for: drag-and-drop, complex gestures, canvas/WebGL, and lists over 10,000 items.

Outputs changes as unified diffs. Cites browser support % for any new CSS feature.

---

### `/update-project-memory`

Keeps `PROJECT-STATE.md` accurate after structural changes. Runs automatically after any task that adds, removes, renames, or relocates files, components, routes, endpoints, dependencies, or design tokens.

**Rules:**

- Only edits sections that actually changed — never rewrites the whole file
- One line per entry — if it needs more, it's too detailed for an index
- Timestamps each changed section with today's date
- Does not update if only file contents changed (not structure)

---

## Reference files

| File | Purpose |
|------|---------|
| [`PROJECT-STATE.md`](.claude/skills/PROJECT-STATE.md) | Live index — components, routes, endpoints, dependencies, tokens, conventions |
| [`REFERENCE.md`](.claude/skills/REFERENCE.md) | Detailed directory map, naming conventions, full Tailwind token list, TypeScript patterns |
| [`CONVENTIONS.md`](.claude/skills/CONVENTIONS.md) | Naming rules, import/export rules, state management rules, Tailwind constraints |

`PROJECT-STATE.md` is the source of truth. `REFERENCE.md` and `CONVENTIONS.md` contain more detail and are secondary.

---

## Project snapshot

> As of initial scan — 2026-05-04

- **Framework:** Next.js 16.2.4 (App Router), React 19.2.4
- **State:** Zustand 5 — `useDashboardStore` in `src/lib/store.ts`
- **Styling:** Tailwind v4 — CSS-first config in `src/app/globals.css`, no `tailwind.config.js`
- **TypeScript:** strict mode, path alias `@/*` → `src/*`
- **Components:** 8, all `'use client'`, all in `src/app/dashboard/components/`
- **Routes:** 2 — `/` (redirect) and `/dashboard`
- **API:** none — synchronous mock data in `src/lib/data.ts`
- **Tests:** none yet — `/gen-tests` will install Vitest + RTL on first run

---

## How the skills connect

```
/plan-with-me
    └── produces a task list → you approve → work begins

/scaffold-component          reads PROJECT-STATE.md, REFERENCE.md
    └── creates component + test stub → /gen-tests fills in the tests

/gen-tests                   reads the component file
    └── appends to .test.tsx stub created by scaffold-component

/review-pr                   reads PROJECT-STATE.md, CONVENTIONS.md
    └── checks every diff against documented conventions

/api-pattern                 reads PROJECT-STATE.md → API Endpoints
    └── classifies data → picks Zustand / Server Component / TanStack Query

/css-first                   reads PROJECT-STATE.md → Design Tokens
    └── replaces JS visual logic with CSS alternatives

/update-project-memory       reads PROJECT-STATE.md
    └── patches only the changed sections, timestamps the edit
```
