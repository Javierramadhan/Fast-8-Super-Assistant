# Deploy to Vercel

---

## Prerequisites

- Vercel account (free tier is fine for prototypes)
- Vercel CLI: `npm i -g vercel`
- Project must build cleanly: `npm run build` with no errors

---

## First deploy (one-time link)

```bash
vercel
```

Follow the interactive prompts:
1. Log in (opens browser)
2. Confirm project name and scope
3. Vercel detects Vite automatically — accept the default build settings
4. First deploy completes; you get a preview URL

This links the local project to a Vercel project. Run once per repo.

---

## Subsequent deploys

```bash
vercel --prod
```

Builds and deploys to the production URL. The URL never changes between deploys — safe to share with stakeholders ahead of time.

---

## Sharing with stakeholders

Copy the production URL from the CLI output or from the Vercel dashboard. Stakeholders do not need a Vercel account or login. The URL is public by default.

Format: `https://<project-name>.vercel.app`

---

## Build config

Vercel auto-detects Vite. No `vercel.json` needed.

Default settings Vercel applies:
- Framework: Vite
- Build command: `npm run build`
- Output directory: `dist`
- Install command: `npm install`

Only add `vercel.json` if you need to override these (e.g. custom rewrites for multi-page routing).

---

## Custom domain (optional)

Add a custom domain in the Vercel dashboard under **Project → Settings → Domains**. Not required for stakeholder sharing.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Build fails on Vercel but works locally | Run `npm run build` locally first; fix all errors before deploying |
| 404 on page refresh (multi-page) | Add `vercel.json` with a rewrite rule: `{ "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }` |
| Environment variables missing | Add them in Vercel dashboard → Settings → Environment Variables |
