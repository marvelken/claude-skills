---
name: scaffold-component
description: ALWAYS read REFERENCE.md before scaffolding. ALWAYS place new components in src/app/dashboard/components/. NEVER create subdirectories — the project uses a flat component structure. NEVER add barrel exports (no index.ts files exist in this project). ALWAYS default to Server Components unless the component needs useState, useEffect, or event handlers — check the requirement before deciding. ALWAYS use only Tailwind classes documented in REFERENCE.md. ALWAYS run tsc --noEmit after creation and fix any errors before reporting done.
argument-hint: "[ComponentName]"
disable-model-invocation: true
---

## Steps

**1. Read reference**
Read `.claude/skills/PROJECT-STATE.md` (primary — current live state) and `.claude/skills/REFERENCE.md` (secondary — detailed token list). PROJECT-STATE.md takes precedence if they conflict.

**2. Determine client vs server**
- Needs `useState` / `useEffect` / event handlers / `useDashboardStore` → `'use client'`
- Pure display from props → Server Component (no directive)
- When in doubt: this project currently uses client components everywhere; match existing pattern unless there's a clear server-component win.

**3. Determine props interface**
Read relevant types from `src/lib/data.ts`. Name the interface `{ComponentName}Props`.

**4. Create component file**

Path: `src/app/dashboard/components/{ComponentName}.tsx`

Template:
```tsx
// Add 'use client' only if needed
import type { Task } from '@/lib/data'  // import only types you use

interface {ComponentName}Props {
  // props here
}

export default function {ComponentName}({ ...props }: {ComponentName}Props) {
  return (
    <div className="...">
      {/* implementation */}
    </div>
  )
}
```

**5. Create test stub**

Path: `src/app/dashboard/components/{ComponentName}.test.tsx`

```tsx
import { render, screen } from '@testing-library/react'
import { describe, it, expect } from 'vitest'
import {ComponentName} from './{ComponentName}'

describe('{ComponentName}', () => {
  it('should render without crashing', () => {
    render(<{ComponentName} />)
    // TODO: add behavior tests with /gen-tests
  })
})
```

**6. Import in parent if needed**
Check `src/app/dashboard/page.tsx` or the relevant parent. Import directly — no barrel.

**7. Run type check**
```bash
npx tsc --noEmit
```
Fix all errors before proceeding.

**8. Report**
```
Created:
  src/app/dashboard/components/{ComponentName}.tsx  (client|server)
  src/app/dashboard/components/{ComponentName}.test.tsx

Client/Server decision: [reason]
Type check: passed | [N errors fixed]
```
