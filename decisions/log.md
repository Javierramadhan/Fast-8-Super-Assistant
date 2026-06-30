# Decision Log

Append-only. When a meaningful decision is made, log it here.

Format: [YYYY-MM-DD] DECISION: ... | REASONING: ... | CONTEXT: ...

---

[2026-06-24] DECISION: Added design-system skill to .claude/skills/ | REASONING: Need a single source of truth for design tokens (colors, spacing, type, etc.) that maps to tailwind.config.js and is readable by prototype-coder; without this, token values get duplicated or drift | CONTEXT: Starting the Fast-8 prototype workflow; Javier decides all token values; skill scaffolds the structure with placeholders

[2026-06-24] DECISION: Added prototype-coder skill to .claude/skills/ | REASONING: Javier wants to ship stakeholder-ready React prototypes independently, without needing frontend dev involvement; skill covers the full Figma → code → screenshot → Vercel pipeline in one place | CONTEXT: Stack fixed at Vite + React (JS/JSX) + Tailwind CSS 3 + @phosphor-icons/react; Playwright used for automated screenshots (desktop 1440px, mobile 375px)

[2026-06-24] DECISION: Planned payuung-prototype as first prototype project | REASONING: Payuung is a priority product; starting with one product (not a monorepo) to validate the workflow before scaling | CONTEXT: Separate git repo (not inside Fast-8 Super Assistant workspace); JS/JSX stack; 1–2 screens to be chosen by Javier at build time

[2026-06-24] DECISION: Revised CLAUDE.md Memory section to remove "works out of the box" language | REASONING: The original text implied automatic cross-session memory, which overstates how continuity actually works; continuity in this workspace comes from context files, decisions/log.md, and skill files — not from automatic memory alone | CONTEXT: The auto-memory system supplements but does not replace the structured context files

[2026-06-24] DECISION: Updated prototype-coder backlog description in CLAUDE.md from HTML/CSS/JS to React + Tailwind + Phosphor Icons | REASONING: Stack is now fixed and should be reflected accurately in the backlog | CONTEXT: Bundled with the larger skills + prototype plan

[2026-06-24] DECISION: Proyek prototype hidup di repo terpisah; lokasi dicatat di projects/, skill tetap generik | REASONING: Skill prototype-coder harus reusable lintas produk, jadi tidak boleh hardcode lokasi; lokasi spesifik dicatat di context (projects/payuung-prototype.md) agar sesi berikutnya tahu tanpa diulang | CONTEXT: Claude Code dijalankan dari workspace Fast-8 (skill ter-load), output ditulis ke d:\payuung-prototype\

[2026-06-24] DECISION: payuung-prototype di-init sebagai git repo terpisah dengan push sendiri | REASONING: Repo terpisah berarti dua tempat commit/push yang berdiri sendiri; workspace dan proyek tidak saling tercakup oleh git, jadi kode prototype harus di-commit & push dari dalam folder proyek, bukan dari workspace | CONTEXT: .gitignore sudah benar sebelum commit pertama (node_modules tidak ter-commit); package-lock.json di-track; remote GitHub dibuat manual lalu di-connect; branch: main

[2026-06-30] DECISION: Adopted Design Engineer Workflow framing across workspace | REASONING: Javier read a design engineer workflow guide — 3-step process (Figma → Claude → iterate in codebase) that matches how we already work; making it explicit in CLAUDE.md and me.md ensures every session starts with the right mental model | CONTEXT: Key principle: Javier = designer (Figma is his domain for 0→1 exploration), Claude = coder/executor; this framing prevents Claude from asking Javier coding questions or deferring execution back to him; improvement iterations happen in Claude + codebase, not back in Figma
