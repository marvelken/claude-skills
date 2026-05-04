# PROJECT-STATE.md — Acme Project Manager

> Living index of project structure. One line per entry. Updated by /update-project-memory.
> Source project: `/Users/marvelken-anele/Documents/benchmark-claude-code`
> Initial scan: 2026-05-04

---

## Components
<!-- Updated: 2026-05-04 -->

| Name | Path | Type | Purpose |
|------|------|------|---------|
| Sidebar | src/app/dashboard/components/Sidebar.tsx | client | collapsible nav with project list, status badges, and task filters |
| DashboardHeader | src/app/dashboard/components/DashboardHeader.tsx | client | top bar with notification bell, unread badge, dropdown, and user avatar |
| ContentArea | src/app/dashboard/components/ContentArea.tsx | client | layout shell wrapping ProjectList and StatsFooter with toolbar |
| ProjectList | src/app/dashboard/components/ProjectList.tsx | client | filters and renders ProjectCard list based on selectedProjectId |
| ProjectCard | src/app/dashboard/components/ProjectCard.tsx | client | expandable card showing project info, progress bar, and inline task list |
| TaskRow | src/app/dashboard/components/TaskRow.tsx | client | single task row with inline status cycling, priority badge, assignee avatar, edit/delete |
| TaskModal | src/app/dashboard/components/TaskModal.tsx | client | create/edit task form with title, description, status, priority, assignee, project fields |
| StatsFooter | src/app/dashboard/components/StatsFooter.tsx | client | 6-column stats grid showing project and task counts by status |

---

## Routes
<!-- Updated: 2026-05-04 -->

| Route | Page File | Layout | Purpose |
|-------|-----------|--------|---------|
| / | src/app/page.tsx | RootLayout | immediate redirect to /dashboard |
| /dashboard | src/app/dashboard/page.tsx | RootLayout | main dashboard — sidebar + content area + conditional TaskModal |

---

## API Endpoints
<!-- Updated: 2026-05-04 -->

| Path | Method | Description |
|------|--------|-------------|
| *(none)* | — | all data is synchronous mock data in src/lib/data.ts |

---

## Dependencies
<!-- Updated: 2026-05-04 -->

| Package | Used In | Version | Why |
|---------|---------|---------|-----|
| next | entire app | 16.2.4 | App Router framework, server rendering, routing |
| react | all components | 19.2.4 | UI component model |
| react-dom | src/app/layout.tsx | 19.2.4 | DOM renderer for React |
| zustand | src/lib/store.ts | ^5.0.12 | client state management (tasks, projects, UI state, filters) |
| tailwindcss | src/app/globals.css | ^4 | utility CSS — v4 CSS-first setup, no config file |
| @tailwindcss/postcss | postcss.config.mjs | ^4 | PostCSS plugin to process Tailwind v4 |
| typescript | all .ts/.tsx | ^5.9.3 | static typing, strict mode enabled |
| eslint | project root | ^9 | linting |
| eslint-config-next | eslint.config.mjs | 16.2.4 | Next.js ESLint rules + TypeScript rules |
| @types/node | tsconfig | ^20 | Node.js type definitions |
| @types/react | all components | ^19 | React type definitions |
| @types/react-dom | src/app | ^19 | ReactDOM type definitions |

---

## Design Tokens
<!-- Updated: 2026-05-04 -->

Tailwind v4 — configured via CSS custom properties in `src/app/globals.css`. No `tailwind.config.js`.

**CSS variables defined:**
- `--background` / `--color-background`: `#ffffff` (light) / `#0a0a0a` (dark)
- `--foreground` / `--color-foreground`: `#171717` (light) / `#ededed` (dark)
- `--font-sans`: Geist Sans (next/font/google)
- `--font-mono`: Geist Mono (next/font/google)

**Tailwind scale in active use:**
- Grays: `50 100 200 400 500 600 700 900`
- Blues: `50 100 200 500 600 700 800`
- Greens: `100 700`
- Yellows: `100 700`
- Reds: `50 100 500 600 700`

**Responsive prefixes in use:** `sm:` and `lg:` only (no `md:`, `xl:`, `2xl:`)

---

## Conventions
<!-- Updated: 2026-05-04 -->

**File naming:**
- Components: PascalCase `.tsx` — `ProjectCard.tsx`
- Utilities / lib: camelCase `.ts` — `store.ts`, `data.ts`
- No barrel exports — no `index.ts` files anywhere in the project

**Component structure:**
- All components are `'use client'` — zero Server Components currently in use
- Props interface named `{ComponentName}Props`, declared immediately before the function
- One default export per file, name matches filename
- Hooks at top of function body: store selectors first, then `useState`

**Imports:**
- Path alias `@/*` maps to `src/*` — always use `@/` for non-relative imports
- `import type` for type-only imports
- No barrel imports — always import from the source file directly

**State management:**
- Global/shared state → Zustand (`useDashboardStore`) — no React Context
- Store selectors select minimum slice: `useDashboardStore((s) => s.field)` not `(s) => s`
- Local UI state (collapsed, dropdown open) → `useState`
- Max prop drilling depth before moving to Zustand: 2 intermediate components

**TypeScript:**
- `strict: true` — no `any`, no unguarded `as` assertions
- Status/priority fields use discriminated unions: `'todo' | 'in-progress' | 'done'`
- `Partial<T>` for update payloads

**Tailwind:**
- No arbitrary values — `w-[123px]` is prohibited
- Dark mode via CSS custom properties only — no `dark:` prefix classes

**IDs:**
- Users: `u{n}` — Projects: `p{n}` — Tasks: `t{n}` — Notifications: `n{n}`
- Generated task IDs: `t-{timestamp}-{randomString}`
