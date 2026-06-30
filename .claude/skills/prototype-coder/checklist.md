# Validation Checklist

Run this before capturing final Playwright screenshots. Fix all failures first.

---

## States

- [ ] Default / resting state renders correctly
- [ ] Hover states visible on interactive elements
- [ ] Active / pressed states visible
- [ ] Disabled state renders (greyed out, not clickable) — if applicable
- [ ] Loading / skeleton state — if applicable
- [ ] Empty state (zero items, no results, first-time user) — if applicable
- [ ] Error state (form validation, failed action) — if applicable

---

## Responsiveness

- [ ] Desktop (1440px) — layout intact, no horizontal overflow, no broken grids
- [ ] Tablet (768px) — layout adapts gracefully, nothing clipped
- [ ] Mobile (375px) — layout adapts, text readable, tap targets ≥ 44px

---

## Copy & Content

- [ ] All placeholder text replaced with real or realistic copy
- [ ] No "Lorem ipsum" remaining anywhere
- [ ] All button labels, CTAs, and microcopy reviewed
- [ ] Long strings handled: truncation or wrapping is intentional, not broken

---

## Tokens & Visual

- [ ] All colors come from Tailwind classes sourced from `tailwind.config.js` — no hardcoded hex
- [ ] All spacing uses Tailwind scale — no arbitrary `[]` values unless explicitly unavoidable
- [ ] Typography matches the type scale defined in `design-system/tokens.md`
- [ ] Icons are from `@phosphor-icons/react` only
- [ ] No decoration added without structural purpose (dividers, icons, gradients)

---

## Edge Cases

- [ ] Long content (long names, titles, descriptions) doesn't break layout
- [ ] Empty / null values are handled gracefully — no blank gaps or broken alignment
- [ ] Max items in a list — layout holds at worst-case data volume
