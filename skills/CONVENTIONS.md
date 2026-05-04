# Project Conventions — benchmark-claude-code

> Derived from codebase scan. Used by /review-pr.

## Naming

| Pattern | Convention | Violation example |
|---|---|---|
| Component files | PascalCase `.tsx` | `projectCard.tsx` ❌ |
| Props interfaces | `{Name}Props` suffix | `Props`, `IProjectCard` ❌ |
| Utility/lib files | camelCase `.ts` | `Store.ts` ❌ |
| Zustand actions | camelCase imperative verb | `TaskUpdate`, `task_update` ❌ |
| Handler functions | `handle{Event}` | `onClick`, `clickHandler` ❌ |

## Import / Export

- **No barrel exports** — no `index.ts` files. Import directly:
  ```tsx
  import ProjectCard from './ProjectCard'         ✅
  import { ProjectCard } from './index'           ❌
  ```
- **Path alias** — always use `@/` for non-relative imports:
  ```tsx
  import { useDashboardStore } from '@/lib/store' ✅
  import { useDashboardStore } from '../../../lib/store' ❌
  ```
- **Type imports** — use `import type` for type-only imports:
  ```tsx
  import type { Task } from '@/lib/data'          ✅
  import { Task } from '@/lib/data'               ❌
  ```

## Component Structure

- All components are `'use client'` — this project has zero Server Components
- Props interface declared immediately before the function, never inline
- One default export per file, named matching the file
- Hooks at the top of the function body, before any logic

```tsx
'use client'
// imports
interface FooProps { ... }
export default function Foo({ ... }: FooProps) {
  const x = useDashboardStore((s) => s.x)  // store hooks first
  const [y, setY] = useState(false)         // local state second
  // derived values
  // handlers
  return ( ... )
}
```

## State Management

- **Global state → Zustand.** NEVER use React Context for shared state.
- **Store selectors** — always select the minimum needed slice:
  ```tsx
  const tasks = useDashboardStore((s) => s.tasks)     ✅
  const store = useDashboardStore((s) => s)            ❌ (over-subscribes)
  ```
- **Local UI state** — `useState` for component-private state (collapsed, hover, dropdown)
- **No prop drilling past 2 levels** — if a value is passed through 2+ intermediate components, move it to the Zustand store

## Error Handling

- Empty states are handled inline (no separate ErrorBoundary components exist)
- Optional chaining for nullable values: `project?.name`
- No `try/catch` in components (data is synchronous/local; no async fetch)

## Prop Patterns

- Destructure props in the function signature, never `props.x`
- Optional props use `?:` in the interface, not default argument overloads
- Max prop depth before Zustand: 2 intermediate components

## TypeScript

- `strict: true` is enforced — no `any`, no unguarded assertions
- Discriminated unions for status/priority strings: `'todo' | 'in-progress' | 'done'`
- `Partial<T>` for update payloads (see `updateTask`)

## Tailwind

- Only classes present in `REFERENCE.md#tailwind-v4-tokens` are in use
- No arbitrary values (`w-[123px]` ❌)
- Responsive prefixes: `sm:` and `lg:` only (no `md:`, `xl:`)
- Dark mode: handled via CSS custom properties, not `dark:` prefix classes
