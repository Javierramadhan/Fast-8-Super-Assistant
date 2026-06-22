# Skill: QA Design v2

**Trigger:** `/qa-design <production-url> <figma-url>`

Compare a production website against a Figma design reference and generate a Word QA report with annotated screenshots.

> ⚠️ **Figma tool policy: ONLY `figma-desktop` MCP is allowed.**
> `figma-remote` and `mcp__figma__*` tools DO NOT work (different accounts, expired token).
> All Figma operations go through `mcp__figma-desktop__*` or the `--save-figma` / `--get-figma-specs` CLI modes.

---

## Usage

```
/qa-design https://payuung.com/page https://figma.com/design/FILE_ID/Name?node-id=22681-13816
```

- `production-url` — the live page to audit
- `figma-url` — must include `?node-id=` pointing to the specific FRAME (not component)

---

## First-Time Setup (run once)

```bash
pip install playwright pillow python-docx requests
python -m playwright install chromium
```

---

## What You Do (Step by Step)

### Step 1 — Enumerate Figma Sections

Before doing anything with Figma, get the child sections of the target frame.
This avoids calling `get_design_context` on the full page (which causes a connection drop).

Call `mcp__figma-desktop__get_metadata` with the Figma frame URL:
```
mcp__figma-desktop__get_metadata(figmaUrl: "<figma-url>")
```

This returns a list of child nodes. Each child with a name like "Section — Hero", "Section — Features", etc.
is a section node. **Copy the `nodeId` of each section.**

> If the frame has no named children, treat the frame itself as one section.

---

### Step 2 — Get Figma Screenshots (per section)

For each section node ID, either:

**Option A — via Claude tool directly** (if Figma Desktop is open and the file is the active tab):
```
mcp__figma-desktop__get_screenshot(nodeId: "22681-13820")
```

**Option B — via Python helper** (more reliable; use this when Option A fails):
```bash
python "d:\Fast-8 Super Assistant\.claude\skills\qa-design\generate_report.py" \
  --save-figma \
  --node-id "22681-13820" \
  --output "d:\Fast-8 Super Assistant\.claude\skills\qa-design\reports\screenshots\<session>\figma_section_hero.png"
```

Repeat for each section. Name files descriptively: `figma_hero.png`, `figma_features.png`, etc.

Also save a full-page Figma screenshot if the frame fits:
```bash
python ... --save-figma --node-id "22681-13816" --output "figma_desktop.png"
```

For mobile, use the mobile frame node ID and save as `figma_mobile.png`.

---

### Step 3 — Get Figma Design Specs (per section)

For each section, extract design specs — padding, fonts, colors, spacing:

**Option A — via Claude tool**:
```
mcp__figma-desktop__get_design_context(nodeId: "22681-13820")
```

**Option B — via Python helper**:
```bash
python "d:\Fast-8 Super Assistant\.claude\skills\qa-design\generate_report.py" \
  --get-figma-specs \
  --node-id "22681-13820" \
  --output "d:\Fast-8 Super Assistant\.claude\skills\qa-design\reports\screenshots\<session>\specs_hero.txt"
```

> Call per section, NOT on the full page frame — the full-page call is too large and drops the connection.

---

### Step 4 — Take Production Screenshots

```bash
python "d:\Fast-8 Super Assistant\.claude\skills\qa-design\generate_report.py" \
  --screenshot-only \
  --url "<production-url>" \
  --session "<product>-<YYYY-MM-DD>"
```

This creates — pixel-perfect, no HiDPI scaling:
```
reports/screenshots/<session>/
  desktop_1440.png   (exactly 1440px wide, full page)
  mobile_375.png     (exactly 375px wide, full page)
```

---

### Step 5 — Visual Analysis

Now look at:
- Production screenshots: `desktop_1440.png`, `mobile_375.png`
- Figma section screenshots: `figma_hero.png`, `figma_features.png`, etc.
- Figma specs: `specs_hero.txt`, etc.

Systematically scan section by section. Identify all issues in **two categories**:

#### Category 1: Content / Writing
- Wrong text vs Figma copy
- Missing text, labels, or UI copy
- Typos, wording differences
- Wrong button/link labels

#### Category 2: Design Properties
- Padding/margin discrepancies (use the specs txt for exact values)
- Wrong font size or weight
- Wrong color (compare hex from specs)
- Wrong component spacing
- Responsive issues at 375px (overflow, wrong stacking, broken layout)

#### For each issue, define:
```json
{
  "id": 1,
  "category": "content | design",
  "severity": "critical | major | minor",
  "viewport": "desktop | mobile | both",
  "title": "Short issue title",
  "location": "Section name / element (e.g. Hero CTA button)",
  "production_screenshot": "desktop_1440.png",
  "production_crop": [x, y, width, height],
  "figma_screenshot": "figma_hero.png",
  "figma_crop": [x, y, width, height],
  "problem": "What is wrong in production",
  "should_be": "What Figma specifies it should be"
}
```

`production_crop` and `figma_crop` are [x, y, w, h] in pixels.
If you can't determine exact coordinates, set to `null` — the full screenshot will be used instead.
Add 40px of context around the problem element — the script handles padding automatically.

---

### Step 6 — Save Issues to JSON

```
reports/screenshots/<session>/issues.json
```

```json
{
  "product": "Payuung — Page Name",
  "url": "https://...",
  "date": "2026-06-22",
  "qa_by": "Javier Ramadhan",
  "figma_url": "https://figma.com/...",
  "issues": [ ...array of issue objects... ]
}
```

---

### Step 7 — Generate Word Report

```bash
python "d:\Fast-8 Super Assistant\.claude\skills\qa-design\generate_report.py" \
  --report \
  --input "d:\Fast-8 Super Assistant\.claude\skills\qa-design\reports\screenshots\<session>\issues.json" \
  --output "d:\Fast-8 Super Assistant\.claude\skills\qa-design\reports\QA-<ProductName>-<Date>.docx"
```

---

### Step 8 — Confirm & Share

Tell Javier:
- Where the `.docx` was saved
- Total issues (by category + severity)
- Any critical issues to flag immediately

---

## generate_report.py CLI Reference

| Mode | Flags | Purpose |
|------|-------|---------|
| `--screenshot-only` | `--url URL --session NAME` | Playwright screenshots (desktop + mobile) |
| `--save-figma` | `--node-id ID --output FILE.png` | Save a Figma section via Desktop MCP HTTP |
| `--get-figma-specs` | `--node-id ID --output FILE.txt` | Extract design specs for a Figma section |
| `--report` | `--input issues.json --output FILE.docx` | Generate Word report |

---

## Report Structure

```
Header: Product | URL | Date | QA by: Javier Ramadhan

Section 1: Content / Writing Issues
  #1 · [Title]                             [CONTENT] [MAJOR]
  ┌─────────────────┬──────────────────────────────────────┐
  │   PRODUCTION    │              FIGMA                   │
  │ [screenshot +   │ [screenshot + green box]             │
  │  red box]       │                                      │
  └─────────────────┴──────────────────────────────────────┘
  Masalah: ...
  Seharusnya: ...

Section 2: Design Properties (Padding & Responsive)
  (same structure)

Summary Table:
  Category  | Critical | Major | Minor | Total
  Content   |    0     |   2   |   1   |   3
  Design    |    1     |   3   |   0   |   4
  TOTAL     |    1     |   5   |   1   |   7
```

---

## Report Naming Convention

```
QA-[ProductName]-[YYYY-MM-DD].docx
```

Examples:
- `QA-Hadirr-2026-06-22.docx`
- `QA-Payuung-SolusiKonseling-2026-07-01.docx`

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `--save-figma` fails "No active tab" | Open Figma Desktop, switch to the correct file tab, retry |
| `get_design_context` drops mid-call | You called it on the full frame — call per section node only |
| Production screenshot is wrong width | Check that `device_scale_factor=1` is in the script (v2 always sets this) |
| Mobile screenshot is 558px not 375px | Old v1 bug — upgrade to v2 of generate_report.py which fixes this |
| `requests` not installed | `pip install requests` |
| `networkidle` timeout on SPA | v2 uses `wait_until='load'` — this is already fixed |

---

## Notes

- **Only `figma-desktop` MCP** — `figma-remote` requires matching accounts, which Javier doesn't have set up
- Production screenshots are always taken fresh (no caching)
- If a page requires login, note it in the report header and skip — never try to bypass auth
- `full_page=True` in Playwright captures the full scrollable page including sections below the fold
