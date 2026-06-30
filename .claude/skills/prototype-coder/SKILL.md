---
name: prototype-coder
description: "This skill should be used when turning a Figma frame into a hosted React prototype. It handles the full pipeline: read Figma via MCP → generate React/Tailwind/Phosphor code → capture Playwright screenshots → deploy to Vercel. It depends on the design-system skill as its token source."
---

# Skill: prototype-coder

This skill takes a Figma frame and produces a working, hosted React prototype — ready to share with stakeholders via a live URL. It covers the full pipeline end to end. All visual output must use tokens from `.claude/skills/design-system/tokens.md`; never hardcode colors or spacing.

**Invoke with:** `/prototype-coder <figma-frame-url> <page-name>`

---

## Prerequisites

Before the first run, confirm these are in place:

- [ ] Node 18+ installed
- [ ] Vercel CLI: `npm i -g vercel`
- [ ] Playwright chromium: `npx playwright install chromium`
  — one-time download, ~100–150MB; skip if already installed
- [ ] Figma Desktop MCP connected and the frame is open in Figma
- [ ] `design-system/tokens.md` has values filled in (at minimum: colors and spacing)

---

## Full Pipeline

### Step 1 — Read the Figma frame

Use `mcp__figma-desktop__get_design_context` to extract layout, components, and spacing from the frame. Use `mcp__figma-desktop__get_screenshot` to get a visual reference. Note: Figma Desktop MCP is read-only.

### Step 2 — Load design tokens

Read `.claude/skills/design-system/tokens.md`. Map token names to the corresponding Tailwind keys before writing any code. If a token is missing a value (still `[PLACEHOLDER]`), stop and ask Javier to fill it in.

### Step 3 — Generate React components

- Pages go in `src/pages/` — one file per screen
- Reusable pieces go in `src/components/`
- Follow the anatomy and variant rules in `.claude/skills/design-system/components.md`
- File extension: `.jsx` (not `.tsx`)

### Step 4 — Apply tokens via Tailwind

- All colors: Tailwind classes only (e.g. `bg-brand-primary`, `text-neutral-textPrimary`)
- All spacing: Tailwind scale (e.g. `p-4`, `gap-6`)
- No inline styles; no hardcoded hex outside `tailwind.config.js`
- Icons: `@phosphor-icons/react` only — never `phosphor-react` (deprecated)

### Step 5 — Run the validation checklist

Open `checklist.md` and go through every item before screenshots. Fix anything that fails.

### Step 6 — Capture Playwright screenshots

Run the screenshot script from `stack.md`. Output:
- `screenshots/<page-name>-desktop.png` — 1440px viewport
- `screenshots/<page-name>-mobile.png` — 375px viewport

The `screenshots/` folder is excluded from git (see `.gitignore`).

### Step 7 — Deploy to Vercel

Follow the steps in `deploy.md`. Share the production URL with Javier. No stakeholder login required.

---

## Constraints

- All token values from `tailwind.config.js` — no exceptions
- `@phosphor-icons/react` only (`phosphor-react` is deprecated)
- No `utils/`, `hooks/`, `services/` unless the prototype genuinely needs them
- JS/JSX — no TypeScript

---

## Design Philosophy

Same principles as `design-system` — avoid:
- Predictable/safe layouts that don't match the Figma
- Timid palettes (use the tokens Javier defined)
- Decoration without structural purpose
- Transitions or motion added "for polish" without a UX reason

---

## Running Context

Claude Code must be running from the **Fast-8 Super Assistant workspace** (where `.claude/skills/` lives) for this skill and `design-system` to be loaded. Do not invoke this skill from inside a prototype project folder — the skills won't be available there.

The prototype project itself is a separate git repo. Before writing any files, read the relevant `projects/<project-name>.md` in the workspace for the project's exact path and stack notes.

---

## File Map

| File | Contents |
|---|---|
| `stack.md` | Package list, `tailwind.config.js` pattern, Playwright screenshot script |
| `deploy.md` | Vercel setup and deploy steps |
| `checklist.md` | Validation checklist to run before final screenshots |
