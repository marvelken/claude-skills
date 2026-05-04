# Component Reference — benchmark-claude-code

> Auto-generated from codebase scan. Source: `src/app/dashboard/components/`

## Directory Structure

```
src/
├── app/
│   ├── globals.css          ← Tailwind v4 imports + CSS custom properties
│   ├── layout.tsx           ← Root layout (Geist font, metadata)
│   ├── page.tsx             ← Redirects to /dashboard
│   └── dashboard/
│       ├── page.tsx         ← Top-level client component; renders sidebar + content + modal
│       └── components/      ← ALL components live here (flat, no subdirectories)
│           ├── Sidebar.tsx
│           ├── DashboardHeader.tsx
│           ├── ContentArea.tsx
│           ├── ProjectList.tsx
│           ├── ProjectCard.tsx
│           ├── TaskRow.tsx
│           ├── TaskModal.tsx
│           └── StatsFooter.tsx
└── lib/
    ├── data.ts              ← Types + mock data + sync getter functions
    └── store.ts             ← Zustand store (useDashboardStore)
```

## Naming Conventions

| Thing | Convention | Example |
|---|---|---|
| Component files | PascalCase | `ProjectCard.tsx` |
| Utility/lib files | camelCase | `store.ts`, `data.ts` |
| Props interfaces | `{Name}Props` | `interface ProjectCardProps` |
| Zustand actions | camelCase imperative | `updateTask`, `openTaskModal` |
| IDs | prefixed shorthand | `u1`, `p1`, `t1`, `n1` |

## Component Pattern

```tsx
'use client'  // ← ALL components are client components

interface FooProps {
  bar: string
  baz?: boolean
}

export default function Foo({ bar, baz }: FooProps) {
  // hooks at top
  const action = useDashboardStore((s) => s.action)  // individual selectors
  const [local, setLocal] = useState(false)

  return (
    <div className="...tailwind classes...">
      {/* JSX */}
    </div>
  )
}
```

## No Barrel Exports

Components import directly — no `index.ts` files:
```tsx
import ProjectCard from './ProjectCard'
import DashboardHeader from './components/DashboardHeader'
```

## Path Alias

```tsx
import { useDashboardStore } from '@/lib/store'  // @/* → src/*
import type { Task } from '@/lib/data'
```

## Tailwind v4 Tokens

Tailwind v4 is configured via CSS custom properties in `src/app/globals.css` (no `tailwind.config.js`).

**CSS variables defined:**
```css
--background: #ffffff / #0a0a0a (dark)
--foreground: #171717 / #ededed (dark)
--color-background, --color-foreground
--font-sans (Geist Sans), --font-mono (Geist Mono)
```

**Tailwind classes in active use:**

Layout: `flex flex-col grid grid-cols-3 grid-cols-6 gap-4 gap-6 space-y-2 overflow-auto overflow-hidden`

Sizing: `h-screen h-full w-64 w-16 w-8 min-w-0 flex-1 flex-shrink-0`

Colors (grays): `bg-gray-50 bg-gray-100 bg-gray-200 text-gray-400 text-gray-500 text-gray-600 text-gray-700 text-gray-900 border-gray-200`

Colors (status):
- Active/In-progress: `bg-blue-50 bg-blue-100 text-blue-700 text-blue-800`
- Done/Success: `bg-green-100 text-green-700`
- Warning/Draft: `bg-yellow-100 text-yellow-700`
- Danger/High: `bg-red-50 bg-red-100 text-red-500 text-red-600 text-red-700`

Spacing: `px-3 px-4 px-6 py-2 py-3 py-4 mx-auto`

Transitions: `transition-all transition-colors duration-200 hover:bg-gray-100`

Borders: `border border-t border-b rounded rounded-full rounded-lg`

Responsive: `sm:grid-cols-3 lg:grid-cols-6 hidden sm:block`

## TypeScript Types (src/lib/data.ts)

```typescript
User    { id, name, email, role: 'admin'|'manager'|'member', avatar }
Task    { id, title, description, status: 'todo'|'in-progress'|'done', priority: 'low'|'medium'|'high', assigneeId, projectId, createdAt }
Project { id, name, description, status: 'active'|'archived'|'draft', ownerId, memberIds, createdAt }
Notification { id, message, read, createdAt }
```

## State Management (src/lib/store.ts)

```typescript
const value = useDashboardStore((s) => s.value)   // selector pattern
const action = useDashboardStore((s) => s.action) // action pattern

// Available actions: updateTask, deleteTask, saveTask, markNotificationRead,
// setFilterChange, toggleSidebar, selectProject, openTaskModal, closeModal
```
