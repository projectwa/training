# 11. Accessibility & Best Practices (Tailwind CSS)

> Updated: 2025-10-18 16:08:26

Designing with accessibility up-front makes your UI more usable for everyone. Tailwind gives you
atomic utilities and smart variants (like `focus-visible`, `aria-*`, `motion-*`) to bake in good
defaults.

---

## Key Principles

1. **Use semantic HTML first**  
   Prefer `<button>`, `<nav>`, `<main>`, `<form>`, etc. Utilities style *semantics*—they don’t replace them.

2. **Visible focus**  
   Never remove focus outlines without providing a clear replacement. Prefer `focus-visible:` so
   mouse users don’t see a focus ring unless they tab.

3. **Color contrast**  
   Target WCAG AA minimum: 4.5:1 for text under 24px (or 3:1 for bold ≥ 18.66px/24px). Tailwind’s
   color scale helps, but always test (e.g. with Axe or Lighthouse).

4. **Keyboard first**  
   Everything interactive must be reachable with Tab and operable with Enter/Space. Manage focus on
   open/close of modals, menus, etc.

5. **State communicated with ARIA**  
   When native semantics aren’t enough, add ARIA attributes and synchronize styles with
   `aria-*` variants.

6. **Respect motion settings**  
   Avoid large parallax/animations by default; use `motion-reduce:` to disable or simplify.

7. **Readable content**  
   Use adequate line length (`prose` or `max-w-prose`), spacing (`leading-relaxed`, `tracking-normal`),
   and size (`text-base`/`text-lg`).

8. **Performance & quality**  
   Ship minimal CSS, lazy-load non-critical UI, and test on real devices.

---

## Tailwind Utilities & Variants You’ll Use Often

- `sr-only` / `not-sr-only` — hide things visually but keep for screen readers.  
- `focus:` / `focus-visible:` / `focus-within:` — draw focus styles.  
- `aria-*:` — match ARIA attributes, e.g. `aria-expanded`, `aria-current`, `aria-invalid`.  
- `data-*:` — match component state when you use data attributes (e.g. `data-open`).  
- `group` / `group-*` and `peer` / `peer-*` — coordinate states between elements.  
- `motion-safe:` / `motion-reduce:` — respect user motion preference.  
- `rtl:` — when the RTL plugin or logical properties are enabled (or use logical spacing).

---

## Example: Accessible Button States

```html
<button
  type="button"
  class="inline-flex items-center gap-2 rounded-lg bg-blue-600 px-4 py-2
         text-white shadow focus:outline-none focus-visible:ring-2
         focus-visible:ring-blue-400 focus-visible:ring-offset-2
         disabled:opacity-50 disabled:cursor-not-allowed"
  aria-busy="false">
  <svg aria-hidden="true" class="h-5 w-5"><!-- icon --></svg>
  Save
</button>
```

**Why it’s good**  
- Uses native `<button>` (keyboard + semantics).  
- Strong focus via `focus-visible:ring-*`.  
- Disabled styles signal non-interactive state.  
- `aria-busy` can be toggled while saving.

---

## Example: Disclosure (Accordion) with ARIA + `aria-*` Variant

```html
<div class="border rounded-lg divide-y">
  <button
    type="button"
    class="w-full text-left px-4 py-3 flex items-center justify-between
           focus:outline-none focus-visible:ring-2 focus-visible:ring-blue-400"
    aria-expanded="false" aria-controls="faq-1">
    <span class="font-medium">What is Tailwind?</span>
    <svg class="h-5 w-5 transition-transform aria-expanded:rotate-180"><!-- chevron --></svg>
  </button>

  <div id="faq-1" class="hidden aria-expanded:block px-4 py-3 text-sm text-gray-700">
    Utility-first CSS framework for rapidly building modern UIs.
  </div>
</div>
```

**How it works**  
- Your JS toggles `aria-expanded` on the button.  
- The chevron rotates with `aria-expanded:rotate-180`.  
- The panel shows/hides with `aria-expanded:block` (paired via JS by also toggling a class/attribute on the panel, or by using `id`/`aria-controls` + a minimal controller).

> If you can’t place `aria-expanded` on the panel, use `data-open="true"` on a container and `data-[open]:block` on children.

---

## Example: Skip Link

```html
<a href="#main" class="sr-only focus:not-sr-only focus:absolute focus:m-4
   focus:rounded-md focus:bg-black focus:px-3 focus:py-2 focus:text-white">
  Skip to main content
</a>

<main id="main">...</main>
```

- Hidden normally via `sr-only`.  
- Becomes visible when focused.

---

## Example: Form with Errors and Hints

```html
<label for="email" class="block text-sm font-medium">Email</label>
<input id="email" name="email" type="email"
  class="mt-1 block w-full rounded-md border px-3 py-2
         aria-invalid:border-red-500 aria-invalid:ring-red-500
         focus-visible:ring-2 focus-visible:ring-blue-500"
  aria-describedby="email-hint email-error" aria-invalid="false">

<p id="email-hint" class="mt-1 text-xs text-gray-500">
  We’ll never share your email.
</p>
<p id="email-error" class="mt-1 hidden text-sm text-red-600" role="alert">
  Please enter a valid email address.
</p>
```

- Toggle `aria-invalid="true"` and show the error with JS (`hidden` → `block`).  
- Inputs get red affordances via `aria-invalid:*` utilities.

---

## Example: Respect Reduced Motion

```html
<div class="transition-transform motion-reduce:transition-none hover:scale-105">
  Hover me (motion-reduce disables zoom)
</div>
```

---

## Headings, Landmarks & Reading Order

- One `<h1>` per page. Use logical heading hierarchy (`h2`, `h3`, …).  
- Landmark elements (`<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`) improve screen reader navigation.  
- Keep visual order ≈ DOM order; avoid reordering with `order-*` that confuses focus/reading.

---

## Color & Theming Best Practices

- Prefer text classes that pass contrast on your background (`text-gray-900` on `bg-white`, or `text-white` on `bg-gray-900`).  
- When using opacity utilities (e.g. `text-white/80`), re-check contrast.  
- Offer dark mode with `dark:` variants and ensure contrast in both themes.  
- Use consistent focus color across themes (`ring-offset-2 ring-offset-white dark:ring-offset-gray-900`).

---

## Navigation & Menus

- Use a real `<button>` to toggle menus.  
- Manage focus when opening—move focus into the menu; close on `Escape`.  
- Trap focus in modals and restore on close. Tailwind handles visuals; accessibility logic comes from your JS or a component library.

---

## Internationalization (i18n) & RTL

- Set `lang` on `<html>` (e.g. `lang="en"`).  
- For RTL languages, prefer logical sizing/spacing or enable RTL support and use `rtl:` variants for exceptions.  
- Avoid embedding direction-specific icons in CSS; flip via `rtl:rotate-180` when necessary.

---

## Performance & Delivery

- **Content scan**: Ensure all template paths are in `tailwind.config.{{js,ts}}` `content` to purge unused CSS.  
- **Minify**: Use the Tailwind CLI `--minify` (or your bundler) for production builds.  
- **Critical UI**: Inline small critical rules if needed; defer the rest.  
- **Icons**: Prefer SVG over icon fonts for accessibility and clarity.  
- **Images**: Add `alt` text (or empty `alt=""` if decorative). Use responsive `<img srcset>` when appropriate.

---

## Testing Checklist

- Keyboard test: Tab through the entire page.  
- Screen reader smoke test (NVDA/JAWS/VoiceOver).  
- Color contrast audit (Axe, Lighthouse).  
- Reduced motion test (system setting + `motion-reduce:` behaviors).  
- Zoom to 200–400% and check reflow/responsiveness.  
- Lighthouse score for Best Practices/Accessibility.  

---

## Copy‑paste Snippets

### Focus ring helper
```html
class="focus:outline-none focus-visible:ring-2 focus-visible:ring-blue-500 focus-visible:ring-offset-2"
```

### Screen-reader only
```html
<span class="sr-only">Open menu</span>
```

### Live region for async status
```html
<div role="status" aria-live="polite" class="sr-only"></div>
```

### Hidden but accessible label
```html
<label class="sr-only" for="search">Search</label>
<input id="search" type="search" placeholder="Search">
```

---

## Further Reading

- WCAG 2.2 quick reference  
- WAI-ARIA Authoring Practices (APG)  
- Axe DevTools / Lighthouse

> Tip: Combine Tailwind with an accessible headless UI library (e.g., Radix, Headless UI) to get interaction logic right,
then style with utilities.
