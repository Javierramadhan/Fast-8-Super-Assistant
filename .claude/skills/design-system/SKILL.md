---
name: design-system
description: "This skill should be used when reading, updating, or enforcing Fast-8's design token system. It is the single source of truth for colors, spacing, typography, and component specs — consumed by the prototype-coder skill to ensure all output stays on-system."
---

# Skill: design-system

This skill records Javier's design token decisions for Fast-8's product suite and keeps prototype output consistent. Token values are decided by Javier — this skill stores and enforces them. The prototype-coder skill reads from here before generating any code.

---

## What This Skill Is NOT

- Does not auto-generate token values
- Does not make font or palette choices on Javier's behalf
- Does not enforce aesthetics beyond what's in `tokens.md`

Font choice is always Javier's call. Palette decisions belong to him. This skill records what he decides.

---

## Design Philosophy

Any output that reads this skill must avoid the following:

| Anti-pattern | What it looks like |
|---|---|
| Predictable layouts | Cards in a 3-column grid because it's "safe"; hero → features → CTA because that's what everyone does |
| Timid palettes | Defaulting to blue/grey when tokens say something bolder |
| Decoration without meaning | Dividers, gradients, or icons added for visual noise rather than structure |
| Motion without purpose | Hover transitions or scroll animations added "for polish" with no UX reason |

When in doubt: use less. A layout should earn every element.

---

## File Map

| File | Contents | When to read |
|---|---|---|
| `tokens.md` | All design token values — colors, spacing, type, sizing, radius, shadows, breakpoints | Before generating any code; before filling `tailwind.config.js` |
| `components.md` | Anatomy, variants, states, and token usage for standard components | Before building a Button, Input, Card, Badge, or NavBar |

---

## How Tokens Map to Tailwind

Each token in `tokens.md` has a **Tailwind key** column. That key maps directly into the `theme.extend` block of `tailwind.config.js`.

Example pattern (values are illustrative — actual values come from `tokens.md`):

```js
// tailwind.config.js
export default {
  content: ['./index.html', './src/**/*.{js,jsx}'],
  theme: {
    extend: {
      colors: {
        brand: {
          primary: tokens['brand-primary'],   // e.g. '#1A1AFF'
          secondary: tokens['brand-secondary'],
        },
        semantic: {
          success: tokens['semantic-success'],
          error: tokens['semantic-error'],
        },
        neutral: {
          surface: tokens['neutral-surface'],
          text: tokens['neutral-text'],
        },
      },
      spacing: {
        '1': '4px',
        '2': '8px',
        // ... rest of scale
      },
      fontSize: { /* from type scale */ },
      borderRadius: { /* from radius tokens */ },
      boxShadow: { /* from shadow tokens */ },
      screens: { /* from breakpoint tokens */ },
    },
  },
  plugins: [],
}
```

Never hardcode a hex value or pixel value in component code. All values come from Tailwind classes sourced from this config.

---

## Updating Tokens

1. Edit the value in `tokens.md` (the relevant row's **Value** column)
2. Update the matching key in `tailwind.config.js`
3. Both files must stay in sync — a token in `tokens.md` with no matching Tailwind key is a gap

---

## Updating Components

1. Open `components.md` and find the component section
2. Edit the relevant field (anatomy, variants, states, token usage)
3. If adding a new component, copy the existing block structure and fill in all fields
