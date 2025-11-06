# 10. Integration & Tooling (Tailwind CSS)
_Last updated: 2025-10-18 16:02 IST_

This chapter shows how to integrate Tailwind CSS into different toolchains and supercharge your workflow with plugins, linters, IDE helpers, testing, and CI/CD. It’s practical and copy‑paste friendly.

---

## 1) Quick Start Paths

### A) Play CDN (fast prototypes)
Use this for throwaway prototypes, playgrounds, and workshops.
```html
<!doctype html>
<html>
<head>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="min-h-screen grid place-items-center bg-gray-50">
  <button class="rounded-lg bg-indigo-600 px-4 py-2 text-white hover:bg-indigo-500 focus:outline-none focus:ring-2 focus:ring-indigo-300">
    Hello Tailwind
  </button>
</body>
</html>
```
**Pros:** zero setup. **Cons:** no tree‑shaking, no custom config during runtime (unless using `tailwind.config` inline).

### B) CLI + PostCSS (recommended for most)
**1. Install**
```bash
npm i -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
This creates `tailwind.config.js` and `postcss.config.js`.

**2. Wire your input CSS**
```css
/* src/input.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

**3. Configure content paths (for tree-shaking)**
```js
// tailwind.config.js
export default {
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx,vue,svelte}"],
  theme: { extend: {} },
  plugins: [],
};
```

**4. Build scripts**
```json
// package.json
{
  "scripts": {
    "dev": "npx @tailwindcss/cli -i ./src/input.css -o ./public/tailwind.css --watch",
    "build": "npx @tailwindcss/cli -i ./src/input.css -o ./public/tailwind.css"
  }
}
```
Include `<link href="./public/tailwind.css" rel="stylesheet">` in your HTML.

### C) Vite (React/Vue/Svelte/Vanilla)
```bash
# Choose your flavor; example: React + TS
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
Update `tailwind.config.js` `content` to include `./index.html` and `./src/**/*...`, create `src/index.css` with the `@tailwind` directives, and import it in your app entry.

---

## 2) Framework Recipes

### Next.js (App Router)
1) Install deps: `npm i -D tailwindcss postcss autoprefixer && npx tailwindcss init -p`  
2) Set `content` to `["./app/**/*.{js,ts,jsx,tsx,mdx}", "./components/**/*.{...}", "./pages/**/*.{...}"]`  
3) Create `app/globals.css` with the `@tailwind` directives and import it in `app/layout.tsx`:
```tsx
import "./globals.css";
export default function RootLayout({ children }) {
  return <html lang="en"><body className="bg-white text-gray-900">{children}</body></html>;
}
```

### Vue (Vite)
Import `src/style.css` (with the `@tailwind` directives) in `main.ts`. Ensure `content` includes `./src/**/*.{vue,js,ts}` and `./index.html`.

### SvelteKit
Add `@tailwind` directives in `src/app.css`, import in `src/routes/+layout.svelte`. Include `./src/**/*.{svelte,ts,js}` + `./src/**/*.(md,mdx)` if you render markdown.

### Laravel (Vite)
```bash
php artisan breeze:install # optional scaffolding
npm i -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
Set `content` to `resources/**/*.blade.php`, `resources/**/*.{js,ts,vue}`.

### Rails
Use `importmap` or esbuild. Ensure `content` includes ERB/HAML/SLIM templates: `app/views/**/*.{erb,haml,slim}`, plus `app/javascript/**/*.{js,ts}`.

---

## 3) Official Plugins
Install and add to `plugins` array:
```bash
npm i -D @tailwindcss/typography @tailwindcss/forms @tailwindcss/aspect-ratio @tailwindcss/container-queries
```
```js
// tailwind.config.js
plugins: [
  require("@tailwindcss/typography"),
  require("@tailwindcss/forms"),
  require("@tailwindcss/aspect-ratio"),
  require("@tailwindcss/container-queries"),
]
```

---

## 4) IDE & DX Boosters

### VS Code
- **Tailwind CSS IntelliSense**: class name autocomplete, linting, and previews.
- **Prettier + tailwindcss plugin**: sorts classes consistently.
  ```bash
  npm i -D prettier prettier-plugin-tailwindcss
  ```
  ```json
  // .prettierrc
  { "plugins": ["prettier-plugin-tailwindcss"] }
  ```

### ESLint
- `eslint-plugin-tailwindcss` flags unknown class names & enforces conventions.
```bash
npm i -D eslint eslint-plugin-tailwindcss
```
```js
// .eslintrc.cjs
module.exports = {
  plugins: ["tailwindcss"],
  extends: ["plugin:tailwindcss/recommended"],
};
```

---

## 5) Storybook Integration
```bash
npx storybook@latest init
```
Add your Tailwind CSS file to `.storybook/preview.js`:
```js
import "../src/index.css"; // contains @tailwind directives
```
Update `tailwind.config.js` `content` to include `.storybook/**/*.{js,ts,jsx,tsx,mdx}` and `stories/**/*`.

---

## 6) Testing Workflows
- **Unit**: React Testing Library / Vue Test Utils — just render components; Tailwind is static CSS.
- **E2E**: Playwright or Cypress. Assert on visible styles via `toHaveCSS` or class presence.
- **Visual regression**: Storybook + Chromatic or Playwright screenshots.

Example Playwright check:
```ts
await expect(page.getByRole("button", { name: "Submit" })).toHaveCSS("border-radius", "8px");
```

---

## 7) Performance & Production
- **Correct `content` globs** → tiny CSS bundles by default.
- **Safelist** dynamic classes (e.g., from CMS) when needed:
```js
// tailwind.config.js
safelist: [
  { pattern: /bg-(red|green|blue)-(100|500|700)/ },
  "md:grid-cols-3"
]
```
- **Dark mode**: `"class"` strategy is cache‑friendly. Toggle `<html class="dark">`.
- **Critical path**: keep using the generated file; Tailwind v3+ already purges unused CSS during build.
- **HTTP**: enable compression, HTTP/2, long‑term caching with hashed filenames in bundlers.

---

## 8) CI/CD Tips
- Cache `~/.npm` and your bundler cache (`node_modules/.cache`).
- Run `npm ci` for reproducible installs.
- Build: `npm run build` → upload artifact to your static host (Vercel/Netlify/Cloudflare Pages/Render).
- Consider running lint (`eslint .`) and format check (`prettier -c .`) in CI.

---

## 9) Debugging Cheats
- Add a global *debug outline* utility during dev:
```css
/* dev-only.css */
*:not(path):not(svg) { outline: 1px dashed rgba(0,0,0,.1); outline-offset: 2px; }
```
- Use focus rings: `focus:ring-2 focus:ring-blue-300` to verify focus states.
- Inspect final CSS in `public/tailwind.css` when something doesn’t apply.
- Common gotchas:
  - Wrong `content` globs ⇒ missing classes in build.
  - Colliding styles from legacy CSS. Prefer Tailwind's layer order or `@layer` to author overrides.
  - Missing `rel="stylesheet"` link to compiled CSS.

---

## 10) Commands Cheat‑Sheet
```bash
# Install
npm i -D tailwindcss postcss autoprefixer

# Init
npx tailwindcss init -p

# Dev (CLI)
npx @tailwindcss/cli -i ./src/input.css -o ./public/tailwind.css --watch

# Build (CLI)
npx @tailwindcss/cli -i ./src/input.css -o ./public/tailwind.css

# Prettier class sorting
npx prettier -w .
```

---

## Mini Demo Snippet
```html
<button class="inline-flex items-center gap-2 rounded-lg bg-emerald-600 px-4 py-2 font-medium text-white shadow hover:bg-emerald-500 focus:outline-none focus:ring-2 focus:ring-emerald-300">
  <svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M16.707 5.293a1 1 0 0 1 0 1.414l-7.778 7.778a1 1 0 0 1-1.414 0L3.293 11.263a1 1 0 1 1 1.414-1.414l3.101 3.101 7.071-7.071a1 1 0 0 1 1.414 0z"/></svg>
  Integrated!
</button>
```
