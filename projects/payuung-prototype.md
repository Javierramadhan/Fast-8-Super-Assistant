# Project: payuung-prototype

## Location

**Repo:** `d:\payuung-prototype\` — separate git repo, outside this workspace.

This project lives outside the Fast-8 Super Assistant workspace intentionally. `node_modules` and build artifacts stay isolated. The two repos have separate git histories and separate push habits.

---

## Stack

- **Build tool:** Vite
- **Language:** React (JSX) — no TypeScript
- **Styling:** Tailwind CSS v3
- **Icons:** `@phosphor-icons/react` (not the deprecated `phosphor-react`)
- **Deploy:** Vercel

Design tokens live in `.claude/skills/design-system/tokens.md` (this workspace). Mirror them into `d:\payuung-prototype\tailwind.config.js` whenever they change.

---

## How to work on this project

Claude Code runs from this workspace (Fast-8 Super Assistant) — that's where `design-system` and `prototype-coder` skills are loaded. Files are written to `d:\payuung-prototype\` as the target path.

Do not switch Claude Code's working directory to `d:\payuung-prototype\` — skills won't load there.

---

## Git setup

Repo: **https://github.com/Javierramadhan/payuung-prototype** (private)
Branch: `main`. Remote `origin` sudah terhubung.

Subsequent pushes — dari dalam `d:\payuung-prototype\`:
```bash
git push
```

**Important:** Commit and push from `d:\payuung-prototype\`, not from this workspace. Two repos, two push habits.

---

## Screens

_Update this list as screens are built._

| Screen | File | Status |
|---|---|---|
| _(TBD — Javier picks 1–2 first screens)_ | — | — |

---

## Notes

- `screenshots/` is excluded from git (.gitignore) — Playwright output only
- `package-lock.json` is tracked — version pinning
- `node_modules/` is excluded — run `npm install` after cloning
