# 13. Beyond Basics — Tailwind CSS

> Take Tailwind from “comfortable” to “confident.” This lesson covers advanced patterns, performance tuning, maintainability, and real‑world tricks you’ll actually use.

## 🎯 What you’ll learn
- Advanced selectors & variants (arbitrary selectors, `:has()`, `&`, `data-*`, `aria-*`)
- Container queries & responsive patterns beyond breakpoints
- Design tokens with CSS variables + dynamic theming (light/dark/system)
- Composing utilities with `@layer`, `@apply`, and extracted components
- Animation & state patterns (groups, peers, nested states)
- Plugin power: tiny custom utilities (with safelisting tips)
- Performance: production builds, tree‑shaking, file size control
- Accessibility gotchas at scale

---

## 1) Advanced Selectors & Variants

### 1.1 Arbitrary selectors with `&` (nesting)
Use bracket notation to target **complex states** without leaving HTML.

```html
<!-- Change a card’s border when any child button is focused -->
<div class="group/card rounded-xl border p-4 [&:has(button:focus)]:border-blue-500">
  <p class="mb-3">Focusable button inside:</p>
  <button class="px-3 py-1 rounded bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-blue-300">
    Focus me
  </button>
</div>
```

```html
<!-- Style a direct child only -->
<ul class="[&>li]:py-2 [&>li]:border-b last:[&>li]:border-none">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

### 1.2 `:has()` parent‑aware styling
Modern browsers allow parent styling based on descendants.

```html
<!-- Highlight field wrapper if input is invalid -->
<label class="block p-3 rounded border
  [:has(input:invalid)]:border-red-500
  [:has(input:invalid)]:bg-red-50">
  <span class="text-sm text-gray-600">Email</span>
  <input type="email" required class="block w-full border-none p-2 focus:outline-none bg-transparent">
</label>
```

### 1.3 Data & ARIA variants
Tailwind ships `data-[state=open]:…` and `aria-[expanded=true]:…` styles without custom CSS.

```html
<button
  aria-expanded="false"
  class="rounded px-3 py-2 border
         aria-[expanded=true]:bg-blue-600
         aria-[expanded=true]:text-white">
  Toggle
</button>
```

Common built‑ins you can combine like `sm:hover:data-[active]:…`:
- `aria-*` → `aria-checked`, `aria-current`, `aria-expanded`, etc.
- `data-*` → any `data-` attr via `data-[key=value]:…`
- `supports-[]`, `motion-safe`, `motion-reduce`, `portrait`, `landscape`, `print`, `rtl`, `ltr`

---

## 2) Container Queries (beyond breakpoints)
Instead of the viewport, **style by parent width**. Great for embeddable widgets and cards.

1) Add the container type to the parent:
```html
<section class="container-type-inline-size p-4 border rounded-xl">
  <h3 class="text-lg font-semibold cq:md:text-xl">Card Title</h3>
  <p class="mt-2 text-sm cq:md:text-base">
    Resizes based on the container, not the page.
  </p>
</section>
```

2) Configure `tailwind.config.js` to map container query names (example):
```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      containers: {
        // rename as you prefer
        md: "45rem",
        lg: "64rem",
      },
    },
  },
  plugins: [
    require("@tailwindcss/container-queries"),
  ],
}
```

3) Use `cq:` like you use `sm:` or `lg:`:
```html
<div class="container-type-inline-size grid grid-cols-1 cq:md:grid-cols-2 gap-4">
  <article class="p-4 rounded-xl border">A</article>
  <article class="p-4 rounded-xl border">B</article>
</div>
```

> **Tip:** Container queries shine inside cards, dashboards, CMS blocks, and embedded widgets.

---

## 3) Design Tokens with CSS Variables

Move color/spacing/typography into variables to **theme at scale**.

```html
<!-- In your global CSS (e.g., src/input.css) -->
@layer base {
  :root {
    --brand: 222 89% 56%;          /* HSL without hsl() wrapper for Tailwind */
    --muted: 215 20% 96%;
    --text: 220 16% 22%;
  }
  .dark {
    --brand: 222 96% 60%;
    --muted: 215 15% 12%;
    --text: 220 15% 90%;
  }
}
```

Use variables inside Tailwind color utilities:

```html
<button class="px-4 py-2 rounded-xl
  bg-[hsl(var(--brand))]
  text-[hsl(var(--text))]
  hover:bg-[hsl(var(--brand)/0.9)]
  shadow">
  Branded Action
</button>
```

You can also tokenise spacing/size:
```html
@layer base {
  :root { --radius: 1rem; }
}
.card { border-radius: var(--radius); } /* or with Tailwind: rounded-[var(--radius)] */
```

---

## 4) Composing Utilities Cleanly

### 4.1 `@apply` for frequently reused patterns
```css
/* src/input.css */
@layer components {
  .btn {
    @apply inline-flex items-center gap-2 px-4 py-2 rounded-xl
           font-medium shadow-sm transition
           focus:outline-none focus:ring-2 focus:ring-blue-300;
  }
  .btn-primary { @apply btn bg-blue-600 text-white hover:bg-blue-700; }
  .btn-outline { @apply btn border border-gray-300 hover:bg-gray-50; }
}
```

```html
<button class="btn-primary">Save</button>
<button class="btn-outline">Cancel</button>
```

### 4.2 `@layer` to keep cascade stable
Prefer `@layer components` or `@layer utilities` for your custom classes so Tailwind can **order them correctly** relative to framework utilities.

```css
@layer utilities {
  .text-balance { text-wrap: balance; }
  .safe-area-px { padding-top: env(safe-area-inset-top); }
}
```

### 4.3 Extracted React/Vue components
Encapsulate repeated utility strings to avoid duplication:
```jsx
export function Card({title, children}) {
  return (
    <section className="rounded-2xl border p-4 shadow-sm bg-white/60 dark:bg-white/5">
      <h3 className="text-lg font-semibold">{title}</h3>
      <div className="mt-2 text-sm text-gray-600 dark:text-gray-300">{children}</div>
    </section>
  );
}
```

---

## 5) Animation & State Patterns

### 5.1 Keyframes with utilities
```css
@layer utilities {
  @keyframes in-fade-up {
    from { opacity: 0; transform: translateY(6px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  .animate-in-fade-up {
    animation: in-fade-up 300ms ease-out both;
  }
}
```

```html
<div class="animate-in-fade-up">Hello!</div>
```

### 5.2 Group & Peer (nested states)
```html
<!-- Group hover to reveal actions -->
<div class="group relative rounded-xl border p-4">
  <img src="…" alt="" class="rounded-lg" />
  <div class="absolute inset-0 flex items-end justify-end p-3
              opacity-0 transition group-hover:opacity-100">
    <button class="btn-primary">Edit</button>
  </div>
</div>

<!-- Peer checked to toggle sibling -->
<label class="flex items-center gap-2">
  <input type="checkbox" class="peer sr-only">
  <span class="w-10 h-6 rounded-full bg-gray-300 peer-checked:bg-blue-600 relative">
    <span class="absolute top-0.5 left-0.5 h-5 w-5 bg-white rounded-full
                 transition peer-checked:translate-x-4"></span>
  </span>
  <span class="peer-checked:text-blue-700">Enable feature</span>
</label>
```

---

## 6) Tiny Plugins (when you need them)

> Keep it small, composable, and **document** where used.

```js
// plugins/scrollbar.js
module.exports = function({ addUtilities, theme, e }) {
  const utilities = {
    ".scrollbar-none": {
      "scrollbar-width": "none",
      "&::-webkit-scrollbar": { display: "none" }
    }
  };
  addUtilities(utilities);
};
```

```js
// tailwind.config.js
module.exports = {
  content: ["./src/**/*.{js,ts,jsx,tsx,html}"],
  theme: { extend: {} },
  plugins: [
    require("./plugins/scrollbar.js"),
  ],
}
```

> **Safelist tip:** If your classes are **generated at runtime** (e.g., from CMS values), add them to `safelist` in `tailwind.config.js` so production builds don’t purge them.

```js
// tailwind.config.js
module.exports = {
  safelist: [
    { pattern: /bg-(red|green|blue|yellow)-(100|500|700)/ },
    "prose", "prose-lg" // for typography plugin
  ],
}
```

---

## 7) Performance & Production Builds

1. **Enable JIT** (default in Tailwind 3+).  
2. **Keep `content` tight** to real template paths only.
3. **Prefer @apply for bundles** of utilities you reuse everywhere.
4. **Audit large components**; reduce duplicated utility strings.
5. **Use CSS variables** for themed colors to avoid duplicating palette variants.

**Minimal production script:**
```json
{
  "scripts": {
    "build": "npx @tailwindcss/cli -i ./src/input.css -o ./public/tailwind.css --minify"
  }
}
```

**Verify output size** and scan for unused classes in UI audits (Chrome Coverage tab).

---

## 8) Accessibility at Scale (quick checks)
- Maintain **focus states**: use `focus-visible:ring-2 ring-offset-2` for keyboard.
- Respect **motion**: pair animations with `motion-reduce:animate-none` alternatives.
- **Color contrast**: validate token choices (brand on both light/dark backgrounds).
- **Semantics**: Tabs, menus, and dialogs require ARIA attributes + roving focus.
- Ensure **hit areas** ≥ 44px (Apple HIG) — e.g., `min-h-[44px] min-w-[44px]`.

```html
<a class="inline-flex items-center gap-2 px-4 py-2 rounded-xl
   focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-blue-300
   motion-reduce:transition-none">
  <svg aria-hidden="true" class="h-5 w-5"></svg>
  <span class="sr-only">Open settings</span>
  <span aria-hidden="true">Settings</span>
</a>
```

---

## 9) Real‑world Patterns to Steal

### 9.1 Elevated section header
```html
<header class="sticky top-0 z-10 backdrop-blur bg-white/70 dark:bg-gray-900/40
               border-b">
  <div class="mx-auto max-w-5xl px-4 py-3 flex items-center justify-between">
    <h1 class="text-lg font-semibold">Dashboard</h1>
    <div class="flex items-center gap-2">
      <input placeholder="Search…" class="px-3 py-2 rounded-lg bg-gray-100 dark:bg-white/10
                                   focus:outline-none focus:ring-2 focus:ring-blue-300" />
      <button class="btn-primary">New</button>
    </div>
  </div>
</header>
```

### 9.2 Card grid that adapts to container width
```html
<section class="container-type-inline-size">
  <div class="grid grid-cols-1 cq:md:grid-cols-2 cq:lg:grid-cols-3 gap-4">
    <article class="rounded-xl border p-4">Card A</article>
    <article class="rounded-xl border p-4">Card B</article>
    <article class="rounded-xl border p-4">Card C</article>
  </div>
</section>
```

### 9.3 Theme toggle with data attributes
```html
<html data-theme="light" class="min-h-full">
  <script>
    // simple toggle
    const html = document.documentElement;
    function setTheme(t){ html.dataset.theme = t; html.classList.toggle('dark', t === 'dark'); }
  </script>
  <body class="bg-white text-gray-900 dark:bg-gray-950 dark:text-gray-100
               data-[theme=dark]:selection:bg-blue-600 data-[theme=dark]:selection:text-white">
    <!-- … -->
  </body>
</html>
```

---

## 10) Practice

### Exercise A — Turn utilities into components
Refactor 2 buttons and 1 card into `@apply` classes inside `@layer components`, then use them in templates.

### Exercise B — Container‑aware layout
Build a three‑card section that becomes a **single column** when its parent is `< 600px` wide, using `@tailwindcss/container-queries`.

### Exercise C — Animate with respect for motion
Create a fade‑in animation utility and disable it with `motion-reduce`.

---

## ✅ Checklist
- [ ] I can target complex states with `&`, `:has()`, `aria-*`, and `data-*`.
- [ ] I can apply container queries with `cq:` and know when they’re useful.
- [ ] I can extract components with `@layer` and `@apply`.
- [ ] My builds are lean with tight `content` and safelists where needed.
- [ ] My UI respects focus, motion preferences, and contrast.

---

### Reference snippets you can paste into `tailwind.config.js`

```js
// tailwind.config.js
module.exports = {
  content: ["./**/*.{html,js,ts,jsx,tsx}"],
  theme: {
    extend: {
      // optional: map container query breakpoints
      containers: { md: "45rem", lg: "64rem" },
    },
  },
  safelist: [
    { pattern: /bg-(red|green|blue|yellow)-(100|500|700)/ },
    "prose", "prose-lg"
  ],
  plugins: [
    require("@tailwindcss/forms"),
    require("@tailwindcss/typography"),
    require("@tailwindcss/container-queries"),
  ],
};
```

> Pro tip: keep this lesson beside your editor as a quick “beyond basics” cheat‑sheet.