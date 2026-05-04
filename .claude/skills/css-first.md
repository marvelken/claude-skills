---
name: css-first
description: ALWAYS check if CSS handles the behavior natively before writing JavaScript. NEVER use JS resize observers when container queries work. NEVER use JS scroll listeners when scroll-driven animations work. NEVER use JS virtualization for lists under 10,000 items when content-visibility handles it. NEVER use arbitrary Tailwind values — ALWAYS use only tokens from REFERENCE.md. ALWAYS output code changes as unified diffs. ALWAYS verify browser support via caniuse before recommending a CSS feature.
---

## CSS-First Decision Tree

Before writing JS for any visual behavior, check this list:

| JS pattern you're considering | CSS-first alternative | Browser support |
|---|---|---|
| `ResizeObserver` / JS breakpoints | `@container` queries | 93%+ (caniuse: css-container-queries) |
| `scroll` event listeners | `@scroll-timeline` / scroll-driven animations | 76%+ (caniuse: css-scroll-driven-animations) |
| JS virtualization (`react-window`) | `content-visibility: auto` | 85%+ (caniuse: css-content-visibility) — use for lists <10k items |
| JS parent-state toggling | `:has()` selector | 93%+ (caniuse: css-has) |
| JS route animations | View Transitions API | 80%+ (caniuse: view-transitions) |
| `marginLeft` / `paddingTop` | `margin-inline` / `padding-block` | 96%+ (caniuse: css-logical-props) |

**Only allow JS for:** drag-and-drop, complex gesture recognition, canvas/WebGL, lists over 10,000 items.

---

## Tailwind v4 Tokens (this project)

This project uses Tailwind v4 with **no `tailwind.config.js`** — tokens are CSS custom properties in `src/app/globals.css`. Read that file before adding new tokens.

**In-use tokens (from PROJECT-STATE.md → Design Tokens, and REFERENCE.md):**
- Grays: `gray-50 gray-100 gray-200 gray-400 gray-500 gray-600 gray-700 gray-900`
- Blues: `blue-50 blue-100 blue-200 blue-500 blue-600 blue-700 blue-800`
- Status: `green-100 green-700 yellow-100 yellow-700 red-50 red-100 red-500 red-600 red-700`
- Responsive: `sm:` and `lg:` prefixes only

**No arbitrary values.** `w-[123px]` → find the nearest token or add a CSS variable.

---

## Container Queries (preferred over JS breakpoints)

```tsx
// In the component wrapper:
<div className="@container">
  <div className="flex-col @md:flex-row">
    {/* layout changes based on container, not viewport */}
  </div>
</div>
```

---

## content-visibility (preferred over virtualization)

```tsx
// For long lists — replaces react-window for <10k items
<ul>
  {items.map(item => (
    <li key={item.id} style={{ contentVisibility: 'auto', containIntrinsicSize: '0 80px' }}>
      <TaskRow task={item} />
    </li>
  ))}
</ul>
```

---

## :has() for parent state (preferred over JS toggling)

```css
/* In globals.css — card highlighted when its checkbox is checked */
.card:has(input:checked) {
  background-color: var(--color-blue-50);
}
```

---

## Reviewing existing code

When `/css-first` is run on existing code, output changes as unified diffs:

```diff
- const [isWide, setIsWide] = useState(false)
- useEffect(() => {
-   const observer = new ResizeObserver(([e]) => setIsWide(e.contentRect.width > 640))
-   observer.observe(ref.current)
-   return () => observer.disconnect()
- }, [])
+ // Remove the above — use @container instead
```

```diff
- <div className={isWide ? 'flex-row' : 'flex-col'}>
+ <div className="@container">
+   <div className="flex-col @sm:flex-row">
```

---

## Verification

After changes:
1. `npx tsc --noEmit` — no type errors
2. Confirm JS code removed, CSS alternative in place
3. Note browser support % for any new CSS feature used
