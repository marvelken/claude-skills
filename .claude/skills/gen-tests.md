---
name: gen-tests
description: ALWAYS test observable user behavior — what renders, what changes after interaction, what is accessible. NEVER assert on internal state (useState values, useRef contents). NEVER mock hooks unless testing a specific error boundary or loading state. ALWAYS use userEvent over fireEvent for interactions. ALWAYS use within() to scope queries when the same text appears in multiple DOM locations. NEVER use getByText without first confirming the text is unique in the rendered output. ALWAYS extend an existing .test.tsx stub rather than overwriting it. ALWAYS run the test suite after generating and report results.
argument-hint: "[ComponentName or file path]"
---

## Setup (run once if not present)

This project has no test framework. Install Vitest + React Testing Library:

```bash
npm install -D vitest @vitejs/plugin-react jsdom @testing-library/react @testing-library/user-event @testing-library/jest-dom
```

Create `vitest.config.ts`:
```ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: ['./src/test-setup.ts'],
  },
  resolve: { alias: { '@': '/src' } },
})
```

Create `src/test-setup.ts`:
```ts
import '@testing-library/jest-dom'
```

Add to `package.json` scripts: `"test": "vitest"`.

---

## Steps

**1. Read the component**
Read `src/app/dashboard/components/{ComponentName}.tsx`. Identify:
- What props it accepts
- What it renders based on props/state
- What user interactions trigger changes
- What it calls from `useDashboardStore`

**2. Identify testable behaviors**
List behaviors in plain English before writing any test:
- "Shows project name in heading"
- "Clicking delete button calls deleteTask"
- "Shows empty state when tasks array is empty"

**3. Check for existing stub**
If `{ComponentName}.test.tsx` exists, read it. Append new tests — never overwrite.

**4. Mock Zustand store**
```tsx
vi.mock('@/lib/store', () => ({
  useDashboardStore: vi.fn((selector) => selector({
    // provide only what this component uses
    tasks: mockTasks,
    updateTask: vi.fn(),
  })),
}))
```

**5. Write tests**

Naming: `it('should [behavior] when [condition]', ...)`

```tsx
import { render, screen, within } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { describe, it, expect, vi } from 'vitest'
import ProjectCard from './ProjectCard'

const mockProject = { id: 'p1', name: 'Test Project', status: 'active', ... }
const mockTasks = [{ id: 't1', title: 'Task One', status: 'todo', ... }]

describe('ProjectCard', () => {
  it('should display project name', () => {
    render(<ProjectCard project={mockProject} tasks={[]} isSelected={false} />)
    expect(screen.getByRole('heading', { name: /test project/i })).toBeInTheDocument()
  })

  it('should show task count when tasks exist', () => {
    render(<ProjectCard project={mockProject} tasks={mockTasks} isSelected={false} />)
    expect(screen.getByText(/1 task/i)).toBeInTheDocument()
  })

  it('should call openTaskModal when add task is clicked', async () => {
    const user = userEvent.setup()
    const openTaskModal = vi.fn()
    // mock store with openTaskModal
    render(<ProjectCard project={mockProject} tasks={[]} isSelected={false} />)
    await user.click(screen.getByRole('button', { name: /add task/i }))
    expect(openTaskModal).toHaveBeenCalledWith(undefined)
  })
})
```

**6. Run suite**
```bash
npx vitest run
```

**7. Report**
```
Tests generated: N
Passed: N | Failed: N
File: src/app/dashboard/components/{ComponentName}.test.tsx
```
Fix any failures before reporting done.
