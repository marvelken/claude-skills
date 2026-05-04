---
name: review-pr
description: ALWAYS read CONVENTIONS.md before reviewing. ALWAYS check diffs against the documented conventions — naming, imports, state management, prop patterns, TypeScript. NEVER invent problems — if nothing violates a category, skip that category entirely. ALWAYS provide a suggested fix as code, not prose. ALWAYS assign a severity to every finding. NEVER pass a review that introduces `any`, unguarded type assertions, or prop drilling past 2 levels without flagging it.
argument-hint: "[PR number or branch name]"
---

## Steps

**1. Load conventions**
Read `.claude/skills/PROJECT-STATE.md` (Conventions section — primary) and `.claude/skills/CONVENTIONS.md` (detailed rules — secondary). PROJECT-STATE.md takes precedence if they conflict.

**2. Get the diff**
```bash
git diff main...HEAD          # for local branch
# or: gh pr diff {number}    # for a GitHub PR
```

**3. Review against each category**

Only report a category if you found a violation. Skip empty categories.

---

### Naming
Check: component files (PascalCase), props interfaces (`{Name}Props`), utility files (camelCase), Zustand actions (camelCase imperative).

### Imports / Exports
Check: no barrel exports added, `@/` path alias used (not relative `../../`), `import type` for type-only imports.

### State Management
Check: no React Context introduced, store selectors select minimum slice (not entire store), no prop drilling past 2 components deep.

### Type Safety
Check: no `any`, no `as SomeType` assertions without a guard, discriminated unions preserved.

### Component Structure
Check: `'use client'` present when hooks/events used, props interface before function, one default export per file.

### Tailwind
Check: only classes from `REFERENCE.md` token list, no arbitrary values (`[...]`), responsive prefix usage matches `sm:`/`lg:` only.

### Performance
Check: store selectors not over-subscribing, no unnecessary re-render triggers.

---

## Output Format

```
## Review: {branch/PR}

### {Category}

**{file path}:{line}** — {one sentence: what's wrong}
```suggestion
{corrected code}
```
Severity: must fix | should fix | nit

---
```

At the end:
```
## Summary
Must fix: N | Should fix: N | Nits: N
```

If the diff is clean: output "No violations found." Do not add padding.

**5. Run type check on changed files**
```bash
npx tsc --noEmit
```
Report any new type errors as `must fix`.
