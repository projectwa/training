# 8. Components & Reusability (Tailwind CSS)

> Goal: Build reusable UI pieces with Tailwind’s utility classes, extract shared styles into component classes, and compose variants cleanly.

---

## Why components with a utility-first framework?

- **Consistency** — one source of truth for styles (e.g., a `btn` class used across the app).
- **Speed** — drop-in pieces that “just work” anywhere.
- **Maintainability** — change once, update everywhere.

Tailwind gives you two great options:

1) **Compose utilities inline** (quick and local):  
   ```html
   <button class="inline-flex items-center gap-2 rounded-lg bg-indigo-600 px-4 py-2 font-medium text-white hover:bg-indigo-500 focus:outline-none focus:ring-2 focus:ring-indigo-500/50">
     Save
   </button>
   ```

2) **Extract component classes** (global and reusable) using `@layer components` + `@apply`:  
   ```css
   @tailwind base;
   @tailwind components;
   @tailwind utilities;

   @layer components {
     .btn {
       @apply inline-flex items-center gap-2 rounded-lg px-4 py-2 font-medium
              focus:outline-none focus:ring-2 disabled:pointer-events-none disabled:opacity-50;
     }
     .btn-primary {
       @apply bg-indigo-600 text-white hover:bg-indigo-500 focus:ring-indigo-500/50;
     }
     .btn-outline {
       @apply border border-indigo-600 text-indigo-600 hover:bg-indigo-50;
     }

     .btn-sm { @apply text-sm px-3 py-1.5; }
     .btn-lg { @apply text-base px-5 py-3; }

     .card {
       @apply rounded-2xl border border-gray-200/60 bg-white p-6 shadow-sm;
     }

     .input {
       @apply block w-full rounded-lg border border-gray-300 bg-white px-3 py-2 text-gray-900
              placeholder:text-gray-400 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-500/30;
     }

     .badge {
       @apply inline-flex items-center gap-1 rounded-full bg-gray-100 px-2.5 py-1 text-xs font-medium text-gray-700;
     }
     .badge-success { @apply bg-emerald-100 text-emerald-700; }
     .badge-danger  { @apply bg-rose-100 text-rose-700; }
   }
   ```

> **Tip:** Prefer composition (utilities on the element) for one-offs. Use component classes when the pattern repeats or when you want semantic names.

---

## Variants & states without custom CSS

Tailwind’s variant modifiers make components smarter:

- **Responsive**: `sm:`, `md:`, `lg:`, `xl:`
- **Stateful**: `hover:`, `focus:`, `active:`, `disabled:`
- **Structural**: `group-*`, `peer-*`
- **Theme**: `dark:`
- **Data attributes**: `data-[state=open]:…`

Example using `group` & `peer` (no extra JS required):
```html
<label class="block">
  <span class="mb-1 block text-sm font-medium text-gray-700">Email</span>
  <input class="peer input" placeholder="you@example.com">
  <span class="mt-1 hidden text-xs text-rose-600 peer-invalid:block">Please enter a valid email.</span>
</label>

<a class="group inline-flex items-center gap-1 text-indigo-600 hover:text-indigo-500" href="#">
  Learn more
  <svg class="size-4 transition-transform group-hover:translate-x-0.5" viewBox="0 0 20 20" fill="currentColor"><path d="M7 5l5 5-5 5"/></svg>
</a>
```

---

## Designing a component API

Give each component **clear, minimal** modifiers:

- **Size**: `.btn-sm`, `.btn-lg`
- **Tone**: `.btn-primary`, `.btn-outline`
- **State**: relies on built-in `hover:`, `focus:`, `disabled:`
- **Slots**: use plain child elements (`.btn > .icon`, `.card > .card-title`, etc.)

Keep names **semantic** (`.btn`, `.card`, `.input`) rather than **visual** (`.rounded-8`, `.blue-button`).

---

## Reusable Patterns

### 1) Buttons
```html
<button class="btn btn-primary">Save</button>
<button class="btn btn-outline">Cancel</button>
<button class="btn btn-primary btn-sm">Small</button>
<button class="btn btn-primary btn-lg" disabled>Disabled</button>
```

### 2) Cards
```html
<article class="card space-y-3">
  <header class="flex items-start justify-between">
    <h3 class="text-lg font-semibold text-gray-900">Project Aurora</h3>
    <span class="badge badge-success">Active</span>
  </header>
  <p class="text-gray-600">A delightful UI toolkit built with Tailwind CSS.</p>
  <footer class="flex items-center gap-2">
    <button class="btn btn-primary">Open</button>
    <button class="btn btn-outline">Archive</button>
  </footer>
</article>
```

### 3) Inputs & Input Groups
```html
<label class="block space-y-1.5">
  <span class="text-sm font-medium text-gray-700">Name</span>
  <input class="input" placeholder="Jane Doe">
</label>

<div class="flex rounded-lg shadow-sm ring-1 ring-inset ring-gray-300 focus-within:ring-2 focus-within:ring-indigo-500/40">
  <span class="inline-flex items-center rounded-l-lg bg-gray-50 px-3 text-gray-500">@</span>
  <input class="block w-full rounded-r-lg border-0 bg-white px-3 py-2 text-gray-900 placeholder:text-gray-400 focus:outline-none" placeholder="username">
</div>
```

### 4) Badges & Alerts
```html
<span class="badge">Draft</span>
<span class="badge badge-success">Deployed</span>
<span class="badge badge-danger">Failed</span>
```

### 5) Accordion (no JS via `<details>`)
```html
<details class="group rounded-xl border border-gray-200 p-4 open:shadow-sm">
  <summary class="flex cursor-pointer list-none items-center justify-between">
    <h4 class="font-medium text-gray-900">What is Tailwind?</h4>
    <svg class="size-4 transition-transform group-open:rotate-180" viewBox="0 0 20 20" fill="currentColor"><path d="M7 5l5 5-5 5"/></svg>
  </summary>
  <div class="mt-3 text-gray-600">A utility-first CSS framework for rapidly building custom UIs.</div>
</details>
```

### 6) Modal (CSS-only, `:target` trick)
```html
<a href="#demo-modal" class="btn btn-primary">Open Modal</a>
<div id="demo-modal" class="fixed inset-0 z-50 hidden place-items-center bg-black/40 p-4 [#:target_&]:grid">
  <div class="w-full max-w-lg rounded-2xl bg-white p-6 shadow-lg">
    <div class="flex items-center justify-between">
      <h3 class="text-lg font-semibold text-gray-900">Confirm action</h3>
      <a href="#" class="rounded p-1 text-gray-400 hover:bg-gray-100 hover:text-gray-600">
        <span class="sr-only">Close</span>
        ✕
      </a>
    </div>
    <p class="mt-2 text-gray-600">Are you sure you want to proceed?</p>
    <div class="mt-4 flex justify-end gap-2">
      <a href="#" class="btn btn-outline">Cancel</a>
      <a href="#" class="btn btn-primary">Continue</a>
    </div>
  </div>
</div>
```

> `[#\:target_&]` is the arbitrary variant “selector list” syntax applied to `&` (the element). It toggles display when the element is `:target`.

---

## Dark mode & theming

Use `dark:` variants globally or per-component:

```html
<html class="dark">
  <article class="card dark:bg-gray-900 dark:text-gray-100 dark:border-gray-800">...</article>
</html>
```

Or add a theme attribute and target it via arbitrary variants:
```html
<html data-theme="corporate">
  <button class="btn data-[theme=corporate]:bg-sky-600">Themed button</button>
</html>
```

---

## Project structure suggestions

```
src/
  input.css          # @tailwind base; @tailwind components; @tailwind utilities;
  components.css     # your @layer components blocks
  pages/
    dashboard.html
```

- Keep **component CSS** small and intentional. Use utilities for layout.
- Co-locate **examples** with the page that uses them (e.g., `pages/`).
- Document the **public API** of each component (required classes, size/tone variants, slots).

---

## Testing reusability

1. Drop the component into at least **two different pages** and verify spacing, colors, and responsive behavior.
2. Toggle **states** (`disabled`, `hover`, `focus`) and **themes** (`dark:`) to confirm coverage.
3. Review **accessibility**: focus outlines, color contrast, and `<label>` connections.

---

## Copy-ready component pack

Paste into your `components.css` (loaded after Tailwind) and start using:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .btn {
    @apply inline-flex items-center gap-2 rounded-lg px-4 py-2 font-medium
           focus:outline-none focus:ring-2 disabled:pointer-events-none disabled:opacity-50;
  }
  .btn-primary { @apply bg-indigo-600 text-white hover:bg-indigo-500 focus:ring-indigo-500/50; }
  .btn-outline { @apply border border-indigo-600 text-indigo-600 hover:bg-indigo-50; }
  .btn-ghost   { @apply text-gray-600 hover:bg-gray-100; }
  .btn-sm      { @apply text-sm px-3 py-1.5; }
  .btn-lg      { @apply text-base px-5 py-3; }

  .card        { @apply rounded-2xl border border-gray-200/60 bg-white p-6 shadow-sm; }
  .card-title  { @apply text-lg font-semibold text-gray-900; }
  .card-subtle { @apply text-gray-600; }

  .input {
    @apply block w-full rounded-lg border border-gray-300 bg-white px-3 py-2 text-gray-900
           placeholder:text-gray-400 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-500/30;
  }

  .badge          { @apply inline-flex items-center gap-1 rounded-full bg-gray-100 px-2.5 py-1 text-xs font-medium text-gray-700; }
  .badge-success  { @apply bg-emerald-100 text-emerald-700; }
  .badge-danger   { @apply bg-rose-100 text-rose-700; }

  .nav {
    @apply flex items-center gap-6;
  }
  .nav-link {
    @apply text-sm font-medium text-gray-600 hover:text-gray-900 data-[active=true]:text-gray-900;
  }
}
```
