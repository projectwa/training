
# 6. Responsive Design (Tailwind CSS)

Tailwind is **mobile‑first**. Utilities apply to all screen sizes by default, and you add **breakpoint prefixes** to change styles at specific minimum widths.

## Breakpoints (default)
- `sm` → **640px**
- `md` → **768px**
- `lg` → **1024px**
- `xl` → **1280px**
- `2xl` → **1536px**

**Syntax:** `{breakpoint}:{utility}` — e.g., `md:text-lg` means “from 768px and up, use `text-lg`”.

> Tip: Tailwind 3.2+ includes **max-** variants like `max-md:hidden` (apply when viewport is **below** 768px).

---

## Core Patterns

### 1) Typography that scales
```html
<h1 class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl">Responsive heading</h1>
<p class="text-base sm:text-lg md:text-xl">Readable copy that grows with screen size.</p>
```

### 2) Stacking → Rows (Flexbox)
```html
<div class="flex flex-col md:flex-row gap-4">
  <div class="flex-1 p-4 bg-gray-100 rounded-lg">A</div>
  <div class="flex-1 p-4 bg-gray-100 rounded-lg">B</div>
  <div class="flex-1 p-4 bg-gray-100 rounded-lg">C</div>
</div>
```

### 3) Auto‑filling grids
```html
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
  <!-- cards -->
</div>
```

### 4) Responsive spacing and sizing
```html
<div class="p-4 sm:p-6 lg:p-10 max-w-md sm:max-w-xl lg:max-w-3xl mx-auto">...</div>
<img class="w-full sm:w-1/2 lg:w-1/3 rounded-xl" src="..." alt=""/>
```

### 5) Show/Hide per breakpoint
```html
<button class="block md:hidden">Menu</button>       <!-- only on small screens -->
<nav class="hidden md:flex"> ... </nav>             <!-- from md and up -->
```

---

## Example: Responsive Navbar
- Small screens: logo + hamburger button
- Medium+: logo + horizontal links + call‑to‑action

```html
<nav class="bg-gray-900 text-white">
  <div class="container mx-auto px-4 py-3 flex items-center justify-between">
    <a class="font-semibold tracking-tight">Brand</a>

    <button class="md:hidden inline-flex items-center gap-2 px-3 py-2 rounded-lg bg-gray-800">
      <span>Menu</span>
      <svg class="size-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"/>
      </svg>
    </button>

    <div class="hidden md:flex items-center gap-6">
      <a class="hover:text-indigo-300">Products</a>
      <a class="hover:text-indigo-300">Pricing</a>
      <a class="hover:text-indigo-300">Docs</a>
      <a class="px-4 py-2 rounded-xl bg-indigo-500 hover:bg-indigo-600">Get Started</a>
    </div>
  </div>
</nav>
```

---

## Example: Responsive Cards Grid (with utilities)
```html
<section class="container mx-auto px-4 py-8">
  <h2 class="text-2xl sm:text-3xl font-semibold mb-6">Featured</h2>

  <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
    <article class="rounded-2xl shadow p-5 bg-white">
      <h3 class="text-lg font-medium mb-2">Card title</h3>
      <p class="text-gray-600">Short description goes here...</p>
    </article>
    <!-- Duplicate article blocks for more cards -->
  </div>
</section>
```

---

## Containers & Centering
```html
<div class="container mx-auto px-4">...</div>
```
- `container` applies a responsive max-width at each breakpoint.
- Pair with `mx-auto` to center and `px-4` for side padding.

---

## Images & Media
```html
<img class="w-full sm:w-1/2 lg:w-1/3 h-auto rounded-xl object-cover" src="..." alt=""/>
<video class="w-full md:w-2/3 rounded-xl" controls src="..."></video>
```

---

## “Below a breakpoint” (max- variants)
```html
<div class="bg-green-100 md:bg-green-200 lg:bg-green-300 max-md:bg-red-100">
  <!-- Turns red only when viewport is below 768px -->
</div>
```

---

## Frequently used responsive utilities (cheat‑sheet)
- Layout: `flex`, `grid`, `hidden`, `block`, `inline`, `container`
- Direction: `flex-col md:flex-row`
- Gap: `gap-2 sm:gap-4 lg:gap-8`
- Spacing: `p-4 sm:p-6`, `mx-auto`, `space-y-3 md:space-y-0 md:space-x-4`
- Sizing: `w-full sm:w-1/2`, `max-w-sm md:max-w-2xl`
- Typography: `text-base sm:text-lg md:text-xl`
- Visibility: `hidden md:block`, `block md:hidden`

---

## Full Demo
Open the accompanying **HTML** file for a complete, working example you can resize.

---

### How to use in your project
- With **CDN**: add `<script src="https://cdn.tailwindcss.com"></script>` to your HTML **head** (good for learning/prototyping).
- With **build setup**: the same responsive classes work; just ensure your `content` globs include your templates so Tailwind picks up the classes.

---

### Exercises
1) Make a 2‑column layout on `md` and 3‑column on `lg`.
2) Hide the CTA button below `md`, show it from `md` up.
3) Scale a hero title from `text-3xl` → `sm:text-4xl` → `md:text-5xl` → `lg:text-6xl`.

Happy building!
