---
name: api-pattern
description: ALWAYS determine whether data is server state or client state before writing any code. NEVER fetch inside useEffect directly. NEVER store server state in useState. ALWAYS include cancellation (AbortController or library-native) for any async fetch. ALWAYS handle loading, error, empty, and success states. ALWAYS include a rollback path for optimistic updates. This project currently uses Zustand with synchronous mock data — ALWAYS extend the existing store pattern for client state, and ALWAYS prefer Next.js Server Components or TanStack Query for real server state.
argument-hint: "[describe the data you need to fetch]"
---

## Step 1: Classify the data

**Client state** — derived from user interaction, no server round-trip needed:
→ Add to Zustand store (`src/lib/store.ts`), following existing action pattern.

**Server state** — fetched from an API endpoint, needs caching/revalidation:
→ Use Next.js Server Components (App Router) or TanStack Query.

This project has **no API integration yet** — confirm by checking `PROJECT-STATE.md → API Endpoints`. All current data is synchronous mock data in `src/lib/data.ts`. Read `src/lib/store.ts` to understand the current store shape before extending it.

---

## Step 2a: Client state → Zustand

```ts
// src/lib/store.ts — extend DashboardStore interface
interface DashboardStore {
  // existing fields...
  newThing: NewThingType
  setNewThing: (value: NewThingType) => void
}

// in create():
newThing: initialValue,
setNewThing: (value) => set({ newThing: value }),
```

Consume with a minimal selector:
```tsx
const newThing = useDashboardStore((s) => s.newThing)
```

---

## Step 2b: Server state → Server Component

Prefer this for data that doesn't change on the client:

```tsx
// src/app/dashboard/some-feature/page.tsx (no 'use client')
async function SomeFeaturePage() {
  const data = await fetchData()  // runs on server, zero client JS
  return <ClientComponent initialData={data} />
}
```

---

## Step 2c: Server state → TanStack Query (if real-time or user-triggered)

Install first: `npm install @tanstack/react-query`

```tsx
// src/hooks/useProjects.ts
import { useQuery } from '@tanstack/react-query'

export function useProjects() {
  return useQuery({
    queryKey: ['projects'],
    queryFn: async ({ signal }) => {
      const res = await fetch('/api/projects', { signal })  // signal = cancellation
      if (!res.ok) throw new Error('Failed to fetch projects')
      return res.json()
    },
  })
}

// In component:
const { data, isLoading, isError } = useProjects()
```

---

## Step 3: Handle all states

Every data-fetching component MUST render all four states:

```tsx
if (isLoading) return <div className="text-gray-500">Loading...</div>
if (isError)   return <div className="text-red-600">Failed to load. Try again.</div>
if (!data?.length) return <div className="text-gray-400">No items found.</div>
return <ul>{data.map(...)}</ul>
```

---

## Step 4: Optimistic updates (if needed)

```ts
updateTask: (taskId, updates) => {
  const previous = get().tasks  // snapshot before mutation
  set({ tasks: get().tasks.map(t => t.id === taskId ? { ...t, ...updates } : t) })
  apiCall(taskId, updates).catch(() => set({ tasks: previous }))  // rollback on failure
}
```

---

## Verification

```bash
npx tsc --noEmit
```

Report: which pattern was used and why, which states are handled.
