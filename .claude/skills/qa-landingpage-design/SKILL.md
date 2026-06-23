# Skill: QA Landing Page Design v6

**Trigger:** `/qa-landingpage-design <production-url> <figma-desktop-url> <figma-mobile-url>`

Compare a production website against Figma design references (desktop + mobile) and generate a Word QA report with annotated screenshots.

> 🎯 **Design issues are just as important as content issues.** In real QA sessions, most issues are design/layout on mobile — wrong font sizes, wrong flex direction, wrong element positioning. Never only report content issues.

> ⚠️ **Figma tool policy: ONLY `figma-desktop` MCP is allowed.**
> `figma-remote` and `mcp__figma__*` tools DO NOT work (different accounts, expired token).
> All Figma operations go through `mcp__figma-desktop__*` or the `--save-figma` / `--get-figma-specs` CLI modes.

---

## Usage

```
/qa-design https://payuung.com/page \
  https://figma.com/design/FILE_ID/Name?node-id=22681-14591 \
  https://figma.com/design/FILE_ID/Name?node-id=22856-14430
```

- `production-url` — the live page to audit
- `figma-desktop-url` — must include `?node-id=` pointing to the desktop FRAME
- `figma-mobile-url` — must include `?node-id=` pointing to the mobile FRAME

---

## First-Time Setup (run once)

### macOS
```bash
pip3.12 install --break-system-packages playwright pillow python-docx requests
python3.12 -m playwright install chromium
```

### Windows
```bash
pip install playwright pillow python-docx requests
python -m playwright install chromium
```

> ⚠️ **macOS gotcha:** `python` and `pip` commands do NOT exist by default on macOS.
> Always use `python3.12` and `pip3.12`. If pip blocks with "externally managed environment" error,
> add `--break-system-packages` flag.

---

## What You Do (Step by Step)

### Step 1 — Enumerate Figma Sections (Desktop + Mobile)

Call `mcp__figma-desktop__get_metadata` on **both** the desktop and mobile frames.
This avoids calling `get_design_context` on the full page (which causes a connection drop).

```
mcp__figma-desktop__get_metadata(nodeId: "22681:14591")   ← desktop
mcp__figma-desktop__get_metadata(nodeId: "22856:14430")   ← mobile
```

Extract the direct child `<frame>` nodes — each is a section. Copy their `id` attributes.

> ⚠️ Node ID format: in the URL it's `22681-14591` (dash), in MCP calls it's `22681:14591` (colon).
> The metadata output also uses colon format.

**Typical sections to expect:**
- Navbar (instance — skip, not needed)
- Hero / Container
- USP / Problem cards
- Features / Benefits
- How It Works / Checklist
- Target Audience
- Counselor / Testimonial
- CTA section
- Footer (instance — skip, usually)

---

### Step 2 — Take Production Screenshots

```bash
python3.12 "/Users/user/Fast-8 Super Assistant/.claude/skills/qa-design/generate_report.py" \
  --screenshot-only \
  --url "<production-url>" \
  --session "<product>-<YYYY-MM-DD>"
```

This creates — pixel-perfect, no HiDPI scaling:
```
reports/screenshots/<session>/
  desktop_1440.png   (1440px wide, full page)
  mobile_375.png     (375px wide, full page)
```

> ⚠️ **Cek lebar mobile setelah screenshot.** Jika output bukan 375–390px (misal 470px atau 558px), jalankan workaround snippet di bagian Troubleshooting sebelum lanjut.

---

### Step 2b — Crop Production Per Section

> ⚠️ **JANGAN baca `desktop_1440.png` atau `mobile_375.png` langsung di Step 6.** Full-page screenshot dikompres ekstrem oleh Claude saat ditampilkan — teks kecil, flex direction, warna background, dan posisi elemen tidak terbaca. **Selalu crop per section dulu.**

Setelah full-page screenshot ada, crop ke per-section PNG menggunakan PIL. Section y-positions bisa diestimasi dari Figma metadata (posisi y tiap frame child) lalu disesuaikan:

```python
from PIL import Image

SESSION = "payuung-xxx-2026-06-23"
BASE = f"/Users/user/Fast-8 Super Assistant/.claude/skills/qa-design/reports/screenshots/{SESSION}"

# Desktop — sesuaikan y_start dan height per section dari Figma metadata
desktop = Image.open(f"{BASE}/desktop_1440.png")
sections_desktop = [
    ("prod_s1_hero",        0,    700),
    ("prod_s2_usp",       700,    500),
    ("prod_s3_features", 1200,    520),
    ("prod_s4_kapan",    1720,    570),
    ("prod_s5_pilih",    2290,    950),
    ("prod_s6_teg",      3240,    490),
    ("prod_s7_didukung", 3730,    590),
    ("prod_s8_cta",      4320,    430),
]
for name, y, h in sections_desktop:
    desktop.crop((0, y, 1440, y + h)).save(f"{BASE}/{name}.png")

# Mobile — skala proportional dari Figma frame heights
mobile = Image.open(f"{BASE}/mobile_375.png")
sections_mobile = [
    ("prod_m1_hero",        0,   1100),
    ("prod_m2_usp",      1100,   1400),
    ("prod_m3_features", 2500,   1200),
    ("prod_m4_kapan",    3700,   1100),
    ("prod_m5_pilih",    4800,   2000),
    ("prod_m6_teg",      6800,   1300),
    ("prod_m7_didukung", 8100,   1400),
    ("prod_m8_cta",      9500,    520),
]
for name, y, h in sections_mobile:
    mobile.crop((0, y, mobile.width, y + h)).save(f"{BASE}/{name}.png")

print("Crop done.")
```

> 💡 **Y-positions di atas adalah estimasi.** Setelah crop, baca hasilnya — jika section terpotong atau ada overlap, adjust y_start dan height lalu re-run. Tidak harus sempurna, yang penting setiap section punya PNG sendiri yang cukup jelas dibaca.

Jalankan sebagai:
```bash
python3.12 /tmp/crop_sections.py
```

Hasil: `prod_s1_hero.png` … `prod_s8_cta.png` dan `prod_m1_hero.png` … `prod_m8_cta.png`

---

### Step 3 — Get Figma Section Screenshots (Desktop)

For each desktop section node ID, first preview via Claude tool to verify:
```
mcp__figma-desktop__get_screenshot(nodeId: "22681:14593")
```

Then save to disk via Python helper (parallel batches of 3):
```bash
cd "/Users/user/Fast-8 Super Assistant" && \
python3.12 ".claude/skills/qa-design/generate_report.py" --save-figma \
  --node-id "22681:14593" \
  --output ".claude/skills/qa-design/reports/screenshots/<session>/figma_s1_hero.png" 2>&1 &
python3.12 ".claude/skills/qa-design/generate_report.py" --save-figma \
  --node-id "22681:14612" \
  --output ".claude/skills/qa-design/reports/screenshots/<session>/figma_s2_usp.png" 2>&1 &
python3.12 ".claude/skills/qa-design/generate_report.py" --save-figma \
  --node-id "22681:14632" \
  --output ".claude/skills/qa-design/reports/screenshots/<session>/figma_s3_features.png" 2>&1 &
wait && echo "BATCH DONE"
```

**Desktop file naming:** `figma_s1_hero.png`, `figma_s2_usp.png`, `figma_s3_features.png`, etc.

---

### Step 4 — Get Figma Section Screenshots (Mobile)

> ⚠️ **NEVER save the full mobile frame as a single image.** The Python helper saves it with
> wrong/corrupt dimensions (e.g. 55×1024px instead of 390×795px). The result is unusable.
>
> **Always screenshot mobile per-section** — same approach as desktop. This gives clean,
> properly-sized PNGs (390px wide) for every section.

Get section node IDs from the mobile metadata (Step 1), then save per section in parallel batches:

```bash
cd "/Users/user/Fast-8 Super Assistant" && \
python3.12 ".claude/skills/qa-design/generate_report.py" --save-figma \
  --node-id "22856:14432" \
  --output ".claude/skills/qa-design/reports/screenshots/<session>/figma_m1_hero.png" 2>&1 &
python3.12 ".claude/skills/qa-design/generate_report.py" --save-figma \
  --node-id "22856:14454" \
  --output ".claude/skills/qa-design/reports/screenshots/<session>/figma_m2_usp.png" 2>&1 &
python3.12 ".claude/skills/qa-design/generate_report.py" --save-figma \
  --node-id "22856:14492" \
  --output ".claude/skills/qa-design/reports/screenshots/<session>/figma_m3_features.png" 2>&1 &
wait && echo "BATCH DONE"
```

**Mobile file naming:** `figma_m1_hero.png`, `figma_m2_usp.png`, `figma_m3_features.png`, etc.

---

### Step 5 — Get Figma Font Sizes (via direct MCP on text nodes)

> ⚠️ **`--get-figma-specs` via Python helper ALWAYS times out** — do NOT use it. Instead, call `mcp__figma-desktop__get_design_context` DIRECTLY on individual TEXT NODE IDs.

From the metadata in Step 1, identify text node IDs for:
- **h1/h2** — main heading in Hero section (e.g. the big headline node)
- **h3** — card/feature title node from any card section
- **body/paragraph** — description text node from any card section
- **button label** — CTA button text node

Then call directly:
```
mcp__figma-desktop__get_design_context(nodeId: "22681:13831")  ← h2 heading node
mcp__figma-desktop__get_design_context(nodeId: "22681:13844")  ← h3 card title node
mcp__figma-desktop__get_design_context(nodeId: "22681:13845")  ← paragraph node
```

From the response, extract `fontSize` values. Record them as the **Figma spec** for comparison in Step 6.

**Typical Figma font sizes to verify:**
| Element | Desktop Figma | Mobile Figma |
|---------|--------------|--------------|
| h1/h2 heading | ~40–48px | ~28–32px |
| h3 card title | ~18px | ~16–18px |
| paragraph | ~14–16px | ~14px |
| button | ~16px | ~16px |

> Call on LEAF TEXT NODES (from metadata — look for `<text id="...">` elements), not container frames. Leaf nodes return immediately without timeout.

---

### Step 6 — Visual Analysis

Read each image using the `Read` tool (it renders PNGs visually). Compare **section by section**, viewport by viewport.

> ⚠️ **Baca per-section crops dari Step 2b — BUKAN full-page screenshot.**
> - Desktop: baca `prod_s1_hero.png` ↔ `figma_s1_hero.png`, lalu `prod_s2_*.png` ↔ `figma_s2_*.png`, dst.
> - Mobile: baca `prod_m1_hero.png` ↔ `figma_m1_hero.png`, dst.
> - Full-page screenshot (`desktop_1440.png`, `mobile_375.png`) hanya dipakai sebagai source untuk crop, TIDAK untuk analisis langsung.

> ⚠️ **Viewport comparison rule — strictly enforced:**
> - Desktop production (`prod_sN_*.png`) ↔ Desktop Figma (`figma_sN_*.png`) ONLY
> - Mobile production (`prod_mN_*.png`) ↔ Mobile Figma (`figma_mN_*.png`) ONLY
> - NEVER cross-compare desktop vs mobile — they are separate deliverables.

---

#### Design Checks — Mandatory Per Section

> ⚠️ **Run ALL of these checks for EVERY section, both desktop and mobile.** Don't skip design checks even if the section looks "roughly correct" — font sizes and flex direction issues are invisible at a glance.

**1. Flex direction: row → col**
Compare Figma desktop vs Figma mobile, then verify production matches Figma mobile:
- Cards in a row on desktop → stacked vertically on mobile? (check each card grid)
- Hero buttons side-by-side on desktop → stacked full-width on mobile? (or vice versa — check Figma)
- Two-column sections (text + image) → single column on mobile?
- **Specific risk:** Hero CTA buttons — in some designs they are flex-COL on mobile (stacked atas-bawah), tapi production masih flex-ROW (kanan-kiri). SELALU verify terhadap Figma, jangan assume.
- **Inner element flex direction** — selain card container, cek juga flex direction DALAM tiap item:
  - Icon + teks dalam satu item: flex-row (kanan-kiri) di desktop → flex-col (atas-bawah / icon centered di atas teks) di mobile?
  - Image + text block: 2-col di desktop → image di atas atau di bawah teks di mobile? Cek Figma.
- Flag if: production flex direction (baik container maupun inner element) tidak match Figma mobile

**2. Font size: desktop vs mobile (compare to specs from Step 5)**
For EVERY section heading and body text:
- Is h1/h2 visually smaller on mobile vs desktop? If same size → flag
- Is h3 card title the right size vs Figma spec? (common error: 20px in prod, should be 18px)
- Is paragraph text the right size? (common error: 18px in prod, should be 14px)
- Flag exact values: "h3 = 20px, seharusnya 18px" — be specific

**3. Element positioning**
For every section:
- Are icons centered/positioned where Figma shows them? (above title, to the left, etc.)
- Is the image on the correct side (left vs right) on desktop? On mobile, is image above or below text per Figma?
- Are badge numbers (01, 02, 03) in the correct position relative to the icon?
- Flag if: icon is top-center in Figma but bottom-left in production

**4. Button widths on mobile**
- Hero CTA buttons: full-width (100%) on mobile?
- Service card buttons ("Pelajari Produk"): full-width on mobile?
- CTA section button: matches Figma (may be centered auto-width, not full-width — check Figma)

**5. Element order on mobile**
- Does image appear above or below the text on mobile? Verify against Figma mobile.
- Do checklist items maintain the correct order?

**6. Spacing & padding**
- Is section padding proportionally tighter on mobile vs desktop? (expected)
- Are card gaps smaller on mobile? (expected)
- Flag obviously excessive or missing padding

**7. Background color per section**
- Cek warna background tiap section — putih, abu muda, biru muda, dll.
- Bandingkan dengan Figma: jika Figma putih tapi production abu/biru atau sebaliknya → flag
- Ini sering kelewatan karena terlihat "mirip" di thumbnail, tapi jelas berbeda di crop yang lebih besar
- Common error: section yang seharusnya `background: white` malah inherit background section sebelumnya

> ❌ **JANGAN laporkan "halaman production lebih tinggi dari Figma frame" sebagai issue.** Perbedaan total page height tidak actionable — laporkan perbedaan SPESIFIK yang terlihat (font wrap, spacing, flex direction) di section masing-masing.

---

#### Category 1: Content / Writing
- Wrong text vs Figma copy
- Missing text, labels, or UI copy
- Typos or wording differences (including capitalization errors like "AKtifkan")
- Wrong button/link labels
- Wrong/copied text from another section
- Inconsistent naming across desktop and mobile (e.g. "Telekonseling" vs "Telekonsultasi")

#### Category 2: Design / Layout
- Flex direction not matching Figma (row vs col)
- Font size not scaling correctly desktop → mobile
- Button width wrong on mobile (should be full-width but isn't, or vice versa)
- Wrong color (compare hex from specs — e.g. red `#ef4444` instead of purple `#a855f7`)
- Wrong font weight
- Padding/margin discrepancies
- Wrong design token (hardcoded value vs token reference)
- Element order different from Figma on mobile
- Missing responsive state (element present on desktop but missing on mobile or vice versa)

---

#### Screenshot Scope — Always Whole Section

> ⚠️ **Never crop to a specific element.** Always show the entire section in both production and Figma.

- Even for a small wording error: show the full section, not just the word
- Even for a wrong badge number: show the full step section, not just the badge
- This keeps the report consistent — left and right panels are always the same scale and the reviewer can orient themselves without confusion

**How to set crop coordinates:**
- `production_screenshot`: gunakan per-section crop file dari Step 2b — e.g. `prod_s1_hero.png` atau `prod_m3_features.png`
- `production_crop`: karena sudah per-section, biasanya `[0, 0, full_width, full_height]` dari crop file itu. Kalau mau highlight sub-area, baru set koordinat spesifik.
- `figma_screenshot`: per-section Figma file — `figma_s1_hero.png` atau `figma_m1_hero.png`
- `figma_crop`: `[0, 0, full_width, full_height]` karena Figma file sudah per-section
- Set `production_crop` ke `null` hanya jika crop section belum dibuat

---

#### For each issue, define:
```json
{
  "id": 1,
  "category": "content | design",
  "severity": "critical | major | minor",
  "viewport": "desktop | mobile | both",
  "title": "Short issue title",
  "location": "Section name / element (e.g. Hero CTA button)",
  "production_screenshot": "desktop_1440.png | mobile_375.png",
  "production_crop": [x, y, width, height],
  "figma_screenshot": "figma_s1_hero.png | figma_m1_hero.png",
  "figma_crop": [x, y, width, height],
  "problem": "What is wrong in production or Figma",
  "should_be": "What the correct value should be"
}
```

`production_crop` and `figma_crop` are [x, y, w, h] in pixels. Set to `null` if uncertain.

**Viewport rule:**
- `"desktop"` or `"mobile"` — always one specific viewport for design/layout issues
- `"both"` — **only allowed for content/writing issues** (e.g. a typo that exists in both desktop and mobile Figma)
- Never use `"both"` for layout, font size, flex direction, button width, or responsive issues — those must be reported per viewport separately

**Severity guide:**
- `critical` — layout completely broken, element in wrong position, missing section
- `major` — wrong flex direction, font not scaling, button width wrong, wrong copy on CTA
- `minor` — minor spacing diff, text encoding artifact, non-prominent typo

---

### Step 7 — Save Issues to JSON

```
reports/screenshots/<session>/issues.json
```

```json
{
  "product": "Payuung — Page Name",
  "url": "https://...",
  "date": "2026-06-23",
  "qa_by": "Javier Ramadhan",
  "figma_desktop_url": "https://figma.com/design/...?node-id=22681-14591",
  "figma_mobile_url": "https://figma.com/design/...?node-id=22856-14430",
  "issues": [ ...array of issue objects... ]
}
```

---

### Step 8 — Generate Word Report

```bash
python3.12 "/Users/user/Fast-8 Super Assistant/.claude/skills/qa-design/generate_report.py" \
  --report \
  --input "/Users/user/Fast-8 Super Assistant/.claude/skills/qa-design/reports/screenshots/<session>/issues.json" \
  --output "/Users/user/Fast-8 Super Assistant/.claude/skills/qa-design/reports/QA-<ProductName>-<Date>.docx"
```

---

### Step 9 — Confirm & Share

Tell Javier:
- Where the `.docx` was saved
- Total issues (by category + severity)
- Any **critical** issues to flag immediately

---

## generate_report.py CLI Reference

| Mode | Flags | Purpose |
|------|-------|---------|
| `--screenshot-only` | `--url URL --session NAME` | Playwright screenshots (desktop + mobile) |
| `--save-figma` | `--node-id ID --output FILE.png` | Save a Figma section via Desktop MCP HTTP |
| `--get-figma-specs` | `--node-id ID --output FILE.txt` | Extract design specs for a Figma section |
| `--report` | `--input issues.json --output FILE.docx` | Generate Word report |

> All paths on macOS should use absolute paths with `/Users/user/Fast-8 Super Assistant/...`

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
  Content   |    1     |   4   |   2   |   7
  Design    |    0     |   1   |   1   |   2
  TOTAL     |    1     |   5   |   3   |   9
```

---

## Report Naming Convention

```
QA-[ProductName]-[YYYY-MM-DD].docx
```

Examples:
- `QA-Payuung-TelekonsultasiKesehatan-2026-06-23.docx`
- `QA-Hadirr-2026-06-22.docx`
- `QA-Payuung-SolusiKonseling-2026-07-01.docx`

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `python` not found on macOS | Use `python3.12` instead. macOS does not have `python` in PATH by default |
| `pip install` blocked on macOS ("externally managed") | Add `--break-system-packages` flag: `pip3.12 install --break-system-packages <pkg>` |
| `--save-figma` fails "No active tab" | Open Figma Desktop, switch to the correct file tab, retry |
| Mobile full-frame screenshot is 55×1024px (corrupt) | Never save the full mobile frame — always screenshot mobile **per section** (see Step 4) |
| `mcp__figma-desktop__get_metadata` returns "No node found" | The file/page isn't the active tab in Figma Desktop. Navigate to the correct frame and retry. Note: `--save-figma` via Python helper may still work even when the Claude tool fails |
| `get_design_context` drops mid-call | You called it on the full frame — call per section node only |
| Production screenshot is wrong width | Check that `device_scale_factor=1` is in the script (v2 always sets this) |
| Mobile screenshot production is 558px not 375px | `device_scale_factor=1` alone doesn't fix this on macOS. Workaround: use `is_mobile=True` in Playwright context + PIL resize to 390px after capture. See snippet below. |
| `requests` not installed | `pip3.12 install --break-system-packages requests` |
| `networkidle` timeout on SPA | v2 uses `wait_until='load'` — this is already fixed |
| Batch `--save-figma` runs slow | Run 3 at a time using bash background jobs (`&`) + `wait` — see Step 3/4 |

**Mobile screenshot 558px workaround snippet:**
```python
from playwright.sync_api import sync_playwright
from PIL import Image

url = "https://..."
out = "mobile_375.png"
tmp = "/tmp/mobile_raw.png"

with sync_playwright() as p:
    browser = p.chromium.launch()
    context = browser.new_context(
        viewport={"width": 390, "height": 844},
        device_scale_factor=1,
        is_mobile=True,
        user_agent="Mozilla/5.0 (iPhone; CPU iPhone OS 16_0 like Mac OS X) AppleWebKit/605.1.15"
    )
    page = context.new_page()
    page.goto(url, wait_until="load", timeout=30000)
    page.wait_for_timeout(2500)
    page.screenshot(path=tmp, full_page=True)
    img = Image.open(tmp)
    w, h = img.size
    new_h = int(h * 390 / w)
    img.resize((390, new_h), Image.LANCZOS).save(out)
    context.close()
    browser.close()
```

---

## Common Issues to Watch For

Patterns found during real QA sessions — check these proactively:

#### Content / Writing
| Issue Type | Where to Look | Example |
|-----------|---------------|---------|
| Button text capitalization | All CTA buttons | "AKtifkan" (capital K) vs "Aktifkan" |
| Wrong language word | Headlines, CTAs | "Financial" (English) instead of "Finansial" (Indonesian) |
| Inconsistent naming across sections | How It Works steps | "Telekonseling" vs "Telekonsultasi" |
| Mobile card title copied from section title | Target Audience cards | Card shows "Layanan Ini Cocok Untuk" instead of "Perusahaan" |
| Mobile subtitle copied from wrong section | Any section subtitle | Wrong text copy-pasted from another section |
| Copyright typo | Footer | "All Right Reserved" vs "All Rights Reserved" |
| Em dash encoding error | Subtitles with dashes | `â` appearing instead of `—` |
| Desktop vs mobile content drift | All sections | Same section has different (or wrong) copy in mobile Figma |

#### Layout & Responsive
| Issue Type | Where to Look | Example |
|-----------|---------------|---------|
| Flex row → col tidak diterapkan di card grid | Card grids, hero buttons, two-col sections | 4 cards masih flex-row di mobile, seharusnya flex-col (stacked) |
| **Hero CTA buttons masih flex-row di mobile** | Hero section / button group | Production kanan-kiri (flex-row), Figma seharusnya atas-bawah (flex-col) full-width — selalu verify ke Figma |
| **Inner element flex direction salah di mobile** | Section dengan icon+teks atau image+teks | Desktop: icon kiri, teks kanan (flex-row) → mobile seharusnya icon centered di atas, teks di bawah (flex-col) — tapi production masih flex-row |
| **Posisi gambar salah (side atau order)** | Section 2-col dengan ilustrasi/foto | Desktop: gambar seharusnya kanan, tapi production di kiri. Mobile: gambar seharusnya di bawah teks, tapi production di atas |
| **Background color section salah** | Semua section | Section "Kapan..." seharusnya background putih, tapi production abu/biru muda |
| CTA button width salah di mobile | Hero dan CTA sections | Button auto-width, seharusnya full-width (100%) |
| **h3 font size terlalu besar di mobile** | Semua card/feature section titles | h3 = 20px di production, seharusnya 18px per Figma spec |
| **Paragraph font size terlalu besar di mobile** | Semua body/description text | p = 18px di production, seharusnya 14px per Figma spec |
| Font size tidak scaling desktop → mobile | Semua headings (h1, h2, h3) | H1 = 32px di mobile, seharusnya 24px per Figma spec |
| Wrong step badge number on mobile | How It Works / step sections | Badges show 01, 01, 02, 02 instead of 01, 02, 03, 04 |
| Wrong color token on step indicators | Numbered step circles | Red `#ef4444` instead of purple `#a855f7` |
| Hardcoded color vs design token | Section headings | `#171717` instead of `var(--h1|h2|h3, #0c2c3c)` |
| Mobile breadcrumb overflow | Hero section breadcrumb | Text cut off — "Pribadi" not visible |
| Element order differs on mobile | Image + text sections | Image below text on desktop but should be above on mobile |

---

## Notes

- **Only `figma-desktop` MCP** — `figma-remote` requires matching accounts, which Javier doesn't have set up
- Production screenshots are always taken fresh (no caching)
- If a page requires login, note it in the report header and skip — never try to bypass auth
- `full_page=True` in Playwright captures the full scrollable page including sections below the fold
- Desktop screenshots at 1440px wide; mobile at 375px wide (production) — verifikasi lebar setelah capture
- Figma mobile exports at ~390px wide (Figma frame width); this is fine for comparison
- **Always compare desktop ↔ desktop and mobile ↔ mobile. Never cross-compare viewports.**
- **Full-page production screenshots (`desktop_1440.png`, `mobile_375.png`) hanya dipakai sebagai source PIL crop.** Analisis visual selalu dari per-section crop (`prod_sN_*.png`, `prod_mN_*.png`).
- **Jangan laporkan total page height lebih tinggi dari Figma sebagai issue.** Laporan hanya berisi issues yang bisa divisualisasikan dan direproduksi secara spesifik.
- **`--get-figma-specs` (get_design_context) consistently times out on full sections.** Never call it on a section frame. Instead: call `mcp__figma-desktop__get_design_context` directly on individual LEAF TEXT NODE IDs (e.g. `<text id="22681:13844">` from metadata) — these return immediately with exact fontSize, fontFamily, fontWeight values.
- When writing bash loops that include node IDs with colons (e.g. `22684:15416`), do NOT embed them in colon-delimited strings. Pass node IDs as separate shell variables or call scripts directly to avoid shell parsing bugs.
- Figma mobile section exports may be slightly narrower than 390px (e.g. 353px, 365px) — this is normal, Figma exports bounding box of content not full frame. Still usable for comparison.
