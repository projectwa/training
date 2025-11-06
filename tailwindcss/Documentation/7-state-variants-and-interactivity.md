# 7. State Variants & Interactivity (Tailwind CSS)
_Date: 2025-10-18_

This guide explains how to use **state variants** in Tailwind CSS to style interactive UI states without writing custom CSS. It includes live examples and best practices.

---

## What are “state variants”?
State variants are **prefixes** you add before a utility class to style a component when a particular **state** is true — e.g., `hover:`, `focus:`, `active:`, `disabled:`, `aria-selected:`, `data-open:`, `group-hover:`, `peer-checked:`, etc.

**Syntax:**

```html
<button class="bg-blue-600 hover:bg-blue-700 focus:ring-2 focus:ring-blue-400">
  Click me
</button>
```

---

## Core Pseudo‑class Variants

- `hover:` — when the mouse pointer is over the element.
- `focus:` — when the element receives keyboard/mouse focus.
- `focus-visible:` — only when focus should be visibly indicated (keyboard navigation). More accessible than `focus:`.
- `active:` — while the element is being activated (e.g., mouse down).
- `visited:` — for visited links.
- `disabled:` — when the element is disabled.
- `checked:` — for checked inputs (checkbox/radio).
- `required:` / `optional:` — for form fields.
- `read-only:` / `read-write:` — input/editor states.
- `invalid:` / `valid:` — form validation state.
- `placeholder-shown:` — when the placeholder is visible.

**Example:**

```html
<input
  type="email"
  class="w-full rounded-lg border border-gray-300 p-3
         focus:outline-none focus:ring-2 focus:ring-blue-400
         invalid:border-red-500 invalid:text-red-600
         disabled:bg-gray-100"
  placeholder="you@example.com"
  required
/>
```

---

## Parent/Context Variants

### 1) `group` and `group-*`
Wrap related elements in a parent with class `group`, then target children with `group-hover:`, `group-focus:`, `group-active:`, or `group-[state]:...`

```html
<a class="group block rounded-xl border p-4 hover:bg-gray-50">
  <span class="font-medium block">Learn more</span>
  <span class="text-gray-500 group-hover:text-gray-700">Details →</span>
</a>
```

There’s also `group/open` (when the parent has `[open]`), and `group-[.dark]:` if you scope by parent mode classes.

### 2) `peer` and `peer-*`
Mark a form control (or any element) as `peer`, then style **siblings** that follow it based on the peer’s state:

```html
<label class="flex items-center gap-3">
  <input type="checkbox" class="peer sr-only" />
  <span class="h-6 w-12 rounded-full bg-gray-300 relative
               after:absolute after:left-1 after:top-1 after:h-4 after:w-4 after:rounded-full after:bg-white after:transition-all
               peer-checked:bg-blue-600 peer-checked:after:translate-x-6"></span>
  <span class="text-gray-600 peer-checked:text-blue-700">Enable feature</span>
</label>
```

---

## Attribute-Based Variants

- **ARIA**: `aria-selected:`, `aria-expanded:`, `aria-disabled:`, etc.
- **Data**: `data-open:`, `data-state=open`, usable with the arbitrary variant syntax: `data-[state=open]:bg-blue-100`.
- **Open/Details**: `open:` or `group-open:` for `<details>`.

```html
<button aria-expanded="false"
  class="rounded-lg px-3 py-2 bg-gray-100 aria-expanded:bg-blue-100">
  Toggle
</button>
```

```html
<details class="group rounded-lg border p-3">
  <summary class="cursor-pointer list-none">
    <span class="font-medium group-open:text-blue-700">Section title</span>
  </summary>
  <div class="mt-2 text-gray-600">Hidden content…</div>
</details>
```

---

## Color Scheme & Motion

- **Dark mode**: `dark:` scopes styles when dark mode is active. (Enable `class` or `media` strategy in Tailwind config. With `class`, add `class="dark"` on a parent — often `<html>` or `<body>`.)

```html
<div class="p-4 bg-white text-gray-900 dark:bg-gray-900 dark:text-gray-100">
  I adapt to dark mode.
</div>
```

- **Reduced motion**: `motion-reduce:` and `motion-safe:` to respect users’ OS motion preferences.

```html
<button class="transition transform hover:scale-105 motion-reduce:transition-none motion-reduce:transform-none">
  Hover me
</button>
```

---

## Selection, Placeholder, File Input

- `selection:` — styles the selected text highlight.
- `placeholder:` — styles the placeholder text.
- `file:` — targets the file input button.

```html
<p class="selection:bg-yellow-200 selection:text:black">
  Try selecting this text.
</p>

<input class="placeholder:text-gray-400" placeholder="Search…" />

<input type="file" class="file:mr-3 file:rounded-md file:border-0 file:bg-blue-600 file:px-3 file:py-2 file:text-white
                         hover:file:bg-blue-700 focus:file:ring-2 focus:file:ring-blue-400" />
```

---

## Combining Variants

You can chain multiple variants:

```html
<button class="bg-blue-600 hover:bg-blue-700 active:bg-blue-800 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-400 disabled:opacity-50">
  Save
</button>
```

Order generally follows **state → breakpoint → others**. If something isn’t applying, check for specificity clashes and the order of classes.

---

## Example Patterns

### 1) Button states (accessible focus)
```html
<button
  class="rounded-lg bg-indigo-600 px-4 py-2 font-medium text-white
         hover:bg-indigo-700 active:bg-indigo-800
         focus:outline-none focus-visible:ring-2 focus-visible:ring-indigo-400 focus-visible:ring-offset-2
         disabled:opacity-50 disabled:cursor-not-allowed">
  Continue
</button>
```

### 2) Input with validation
```html
<input type="email"
  class="w-full rounded-md border p-2
         focus:ring-2 focus:ring-blue-400 focus:border-blue-400
         invalid:border-red-500 invalid:text-red-600
         aria-[invalid=true]:border-red-500"
  aria-invalid="false"
  required
/>
```

### 3) Accordion with `<details>`
```html
<details class="group rounded-lg border">
  <summary class="cursor-pointer p-3 font-medium group-open:text-blue-700">
    What is Tailwind?
  </summary>
  <div class="p-3 text-gray-600">A utility-first CSS framework.</div>
</details>
```

### 4) Tooltip with `group-hover`
```html
<button class="group relative rounded-md bg-gray-900 px-3 py-2 text-white">
  Hover me
  <span class="pointer-events-none absolute -top-10 left-1/2 -translate-x-1/2 whitespace-nowrap rounded-md bg-black/80 px-2 py-1 text-xs opacity-0 transition
               group-hover:opacity-100">
    Tooltip text
  </span>
</button>
```

### 5) Toggle with `peer-checked`
```html
<label class="flex items-center gap-3">
  <input type="checkbox" class="peer sr-only" />
  <span class="h-5 w-10 rounded-full bg-gray-300 relative
               after:absolute after:left-1/2 after:top-1/2 after:-translate-x-1/2 after:-translate-y-1/2 after:h-3 after:w-3 after:rounded-full after:bg-white after:transition-all
               peer-checked:bg-emerald-600 peer-checked:after:translate-x-5"></span>
  <span class="text-gray-600 peer-checked:text-emerald-700">Notifications</span>
</label>
```

---

## Arbitrary Variants (advanced)
Tailwind lets you create variants for **any selector** with the arbitrary variant syntax:

```html
<!-- When parent has data-state="open" -->
<div data-state="open" class="[&[data-state=open]_.chevron]:rotate-180">
  <button class="flex items-center gap-2">
    Title
    <svg class="chevron size-4 transition-transform"></svg>
  </button>
</div>
```

Or using bracketed variant prefixes:
```html
<div data-state="open" class="data-[state=open]:bg-blue-50">
  Content
</div>
```

---

## Dark Mode Strategy Quickstart
- In `tailwind.config.js`, set either:
  - `darkMode: 'class'` (recommended for manual toggling), or
  - `darkMode: 'media'` (auto by user’s OS).
- With `class`: add `class="dark"` to `<html>` or `<body>` to enable dark styles.
- Use `dark:` prefix in utilities you want to adapt.

```js
// tailwind.config.js
module.exports = { darkMode: 'class', content: ['./**/*.html'], theme: { extend: {} }, plugins: [] }
```

---

## Accessibility Tips
- Prefer `focus-visible:` over `focus:` to avoid removing focus rings for mouse users.
- Always ensure sufficient color contrast for `hover:`/`active:` states.
- Respect user preferences with `motion-reduce:`.
- Use ARIA and `aria-*` variants for semantic states, not just visuals.

---

## Try it live
Open the companion HTML demo included with this guide for working examples of every concept above.

---

## Appendix: Quick Reference

- Hover/focus/active/disabled/visited
- focus-visible
- Checked/required/invalid/read-only
- Placeholder, selection, file
- group-*, group-open
- peer-*
- aria-*
- data-* and arbitrary variants
- open:, details/summary
- dark:
- motion-safe:, motion-reduce:

> You generally combine: **state → size → color → layout** utilities to reach the final design.