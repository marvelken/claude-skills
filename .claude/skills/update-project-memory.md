---
name: update-project-memory
description: ALWAYS run this skill after any task that adds, removes, renames, or relocates files, components, routes, API endpoints, dependencies, or design tokens. ALWAYS run this after a convention change — new pattern adopted or old pattern deprecated. NEVER rewrite the entire PROJECT-STATE.md — only update the sections that changed. ALWAYS read PROJECT-STATE.md before making any edits. ALWAYS timestamp every change with today's date in the section header. NEVER write multi-line entries — one line per item. If an entry needs more than one line, it is too detailed for this index.
---

## When to run

Run this skill automatically after completing any task that causes one of these changes:

- New file created or existing file deleted/renamed
- Component added, removed, or reclassified (client ↔ server)
- New route or page added or removed
- New API endpoint or server action added or removed
- `package.json` changed (install, uninstall, version bump)
- Design token added or removed from `src/app/globals.css`
- A convention was intentionally adopted or deprecated

---

## Steps

**1. Read current state**
```
Read: .claude/skills/PROJECT-STATE.md
```
Identify which section(s) are affected by the task just completed.

**2. Identify changed sections only**

Do not touch sections that weren't affected. Targeted edits only.

**3. Update each changed section**

Update the section's `<!-- Updated: YYYY-MM-DD -->` comment to today's date.

For table sections (Components, Routes, API Endpoints, Dependencies):
- Add a new row for additions
- Remove the row for deletions
- Edit the row in place for renames or reclassifications
- One line per entry. No sub-bullets.

For list sections (Design Tokens, Conventions):
- Append new items; remove deprecated ones
- One line per item

**4. Entry formats**

```markdown
| ComponentName | src/app/dashboard/components/ComponentName.tsx | client | one-line purpose |
| /route/path   | src/app/route/page.tsx | RootLayout | one-line purpose |
| /api/endpoint | src/app/api/endpoint/route.ts | GET | one-line description |
| package-name  | src/lib/... | ^1.0.0 | why it exists |
```

**5. Save and report**

After editing PROJECT-STATE.md, output:
```
PROJECT-STATE.md updated
Section(s) changed: [list]
Entries added: N | Entries removed: N | Entries modified: N
```

---

## What NOT to update

- Do not update PROJECT-STATE.md if only file contents changed without structural changes
- Do not add comments, explanations, or prose to entries
- Do not update sections unrelated to the completed task
- Do not update if you are unsure what changed — ask the user first
