---
name: plan-with-me
description: ALWAYS run this skill before starting any non-trivial feature. NEVER write code until the user approves the plan. ALWAYS ask targeted interview questions — walking every design branch — before producing a task breakdown. ALWAYS read the codebase to answer questions you can answer yourself (component structure, Zustand store shape, existing patterns). ALWAYS provide a recommended answer for each question so the user can reply "yes" to obvious ones. Trigger words — "build", "create", "add", "implement", "new feature", "new page", or any description requiring more than one file change.
---

## Interview Phase

Read relevant files first — NEVER ask questions the codebase can answer.

For this project, always check before asking:
- `src/app/dashboard/components/` — existing component patterns
- `src/lib/store.ts` — Zustand store shape and existing actions
- `src/lib/data.ts` — data types (User, Task, Project, Notification)
- `src/app/dashboard/page.tsx` — top-level layout and modal rendering

Ask questions in this order, with a recommended answer for each:

**1. Scope** — Where does this feature live? (Rec: `src/app/dashboard/components/`)
**2. Data** — Does it need new types, or does it use existing ones (Task, Project, User)?
**3. State** — New Zustand slice, or extend existing `DashboardStore`?
**4. Client/Server** — Will it use `useState`/`useEffect`/event handlers? (Rec: client component, matching all existing components)
**5. Side effects** — Does it write data, trigger a modal, filter tasks?
**6. Error states** — What should render if data is missing or empty?
**7. Responsive** — Mobile breakpoints needed? (Rec: `sm:`, `lg:` Tailwind prefixes)

Continue asking until you reach shared understanding. Stop when the user has answered every branch.

---

## Plan Output

After the interview, produce this — nothing else:

### Agreement Summary
One paragraph. What we decided and why.

### Task Breakdown
Each task must include:
- Exact file path from this repo
- What changes (add, edit, create)
- Dependencies (which tasks must finish first)

```
[ ] 1. src/lib/store.ts — add new action X (no deps)
[ ] 2. src/app/dashboard/components/Foo.tsx — create component (needs #1)
[ ] 3. src/app/dashboard/page.tsx — wire Foo into layout (needs #2)
```

### Parallelizable Tasks
Which tasks can run simultaneously.

### Deferred Questions
Decisions we explicitly chose not to make yet.

---

**Do NOT write any code. Wait for explicit approval.**

After approval, run `tsc --noEmit` after each file is created and fix errors before moving on.
