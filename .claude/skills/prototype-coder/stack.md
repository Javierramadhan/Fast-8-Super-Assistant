# Stack — prototype-coder

No version pinning. Vite scaffold pulls latest stable at the time of project creation.

---

## Packages

| Package | Purpose |
|---|---|
| `react` + `react-dom` | UI library |
| `vite` + `@vitejs/plugin-react` | Build tool and JSX transform |
| `tailwindcss` + `postcss` + `autoprefixer` | Utility CSS (v3 config format — v4 has a different API; stay on v3 until Javier decides to migrate) |
| `@phosphor-icons/react` | Icon library — **use this, not `phosphor-react`** (deprecated) |
| `playwright` | Headless browser for screenshots (chromium only) |

---

## tailwind.config.js pattern

This is the shape of the config. Fill in values from `.claude/skills/design-system/tokens.md`.

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./index.html', './src/**/*.{js,jsx}'],
  theme: {
    extend: {
      colors: {
        brand: {
          primary: '',       // brand-primary from tokens.md
          secondary: '',     // brand-secondary
          accent: '',        // brand-accent
        },
        semantic: {
          success: '',
          warning: '',
          error: '',
          info: '',
        },
        neutral: {
          bg: '',
          surface: '',
          border: '',
          textPrimary: '',
          textSecondary: '',
          textDisabled: '',
        },
      },
      spacing: {
        '1': '4px',
        '2': '8px',
        '3': '12px',
        '4': '16px',
        '6': '24px',
        '8': '32px',
        '12': '48px',
        '16': '64px',
        '24': '96px',
        '32': '128px',
      },
      fontSize: {
        // populate from type scale in tokens.md
        // e.g. 'display': ['48px', { lineHeight: '56px', letterSpacing: '-0.02em' }]
      },
      borderRadius: {
        // populate from radius tokens
        // e.g. 'sm': '4px', 'md': '8px', 'lg': '16px'
      },
      boxShadow: {
        // populate from shadow tokens
      },
      screens: {
        // populate from breakpoint tokens (or leave blank to use Tailwind defaults)
      },
    },
  },
  plugins: [],
}
```

---

## vite.config.js

Standard Vite + React setup, no TypeScript:

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
})
```

---

## Playwright screenshot script

Run this after `npm run dev` is serving on localhost. Save it as a one-off script (e.g. `screenshot.js`) at the project root — do not commit it, or do commit it if useful to rerun.

```js
import { chromium } from 'playwright'
import { mkdirSync } from 'fs'

const PAGE = process.argv[2] || 'home'
const URL = `http://localhost:5173`

mkdirSync('screenshots', { recursive: true })

const browser = await chromium.launch()

// Desktop
const desktop = await browser.newPage()
await desktop.setViewportSize({ width: 1440, height: 900 })
await desktop.goto(URL, { waitUntil: 'networkidle' })
await desktop.screenshot({ path: `screenshots/${PAGE}-desktop.png`, fullPage: true })

// Mobile
const mobile = await browser.newPage()
await mobile.setViewportSize({ width: 375, height: 812 })
await mobile.goto(URL, { waitUntil: 'networkidle' })
await mobile.screenshot({ path: `screenshots/${PAGE}-mobile.png`, fullPage: true })

await browser.close()
console.log(`Saved: screenshots/${PAGE}-desktop.png + screenshots/${PAGE}-mobile.png`)
```

Run: `node --experimental-vm-modules screenshot.js <page-name>`

Output goes to `screenshots/` — excluded from git.

---

## First-time setup checklist

```
[ ] npm create vite@latest payuung-prototype -- --template react
[ ] cd payuung-prototype
[ ] npm install
[ ] npm install -D tailwindcss postcss autoprefixer
[ ] npx tailwindcss init -p
[ ] npm install @phosphor-icons/react
[ ] npm install -D playwright
[ ] npx playwright install chromium   ← one-time, ~100-150MB
[ ] Update tailwind.config.js with content glob and token values
[ ] Add @tailwind directives to src/index.css
```
