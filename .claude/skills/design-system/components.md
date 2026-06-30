# Component Specs — Fast-8

Standard reusable components. Each block defines anatomy, variants, states, sizing, and which tokens apply.

Fill in token references once `tokens.md` is populated. Add new components by copying the block structure.

---

## Button

**Anatomy:** `[icon-left?]` `[label]` `[icon-right?]`
Icons are optional on either side. Label is always present.

**Variants:**

| Variant | Use case |
|---|---|
| primary | Main CTA — one per view |
| secondary | Supporting action |
| ghost | Low-emphasis action; no fill |
| destructive | Irreversible or dangerous action |
| link | Inline text action; no border or background |

**States:** default, hover, active/pressed, disabled, loading (spinner replaces icon or appends to label)

**Sizing:** S / M / L — see Button Sizing table in `tokens.md`

**Token usage:**

| Property | Token |
|---|---|
| Background | `brand.primary` (primary variant); `neutral.surface` (secondary/ghost) |
| Text | `neutral.bg` on primary; `brand.primary` on secondary/ghost |
| Border | none (primary/ghost); `neutral.border` (secondary) |
| Border radius | `radius-md` |
| Shadow | `shadow-sm` on hover (optional) |
| Disabled opacity | 40% |

---

## Input / Text Field

**Anatomy:** `[label]` `[input container]` `[helper text / error message?]`

**Variants:**

| Variant | Use case |
|---|---|
| default | Standard text entry |
| with-icon-left | Search, filter fields |
| with-icon-right | Password toggle, clear button |
| textarea | Multi-line input |

**States:** default, focus, filled, disabled, error

**Token usage:**

| Property | Token |
|---|---|
| Border (default) | `neutral.border` |
| Border (focus) | `brand.primary` |
| Border (error) | `semantic.error` |
| Background | `neutral.surface` |
| Text | `neutral.textPrimary` |
| Placeholder | `neutral.textDisabled` |
| Label | `neutral.textSecondary` |
| Helper / error text | `neutral.textSecondary` / `semantic.error` |
| Border radius | `radius-md` |

---

## Card

**Anatomy:** `[header?]` `[body]` `[footer?]`
Header and footer are optional. Body is always present.

**Variants:**

| Variant | Use case |
|---|---|
| default | Standard content container |
| outlined | Lower visual weight; border instead of shadow |
| elevated | Higher visual priority; stronger shadow |
| clickable | Entire card is interactive (hover + cursor) |

**States:** default, hover (clickable variant only), selected (when card acts as a selection)

**Token usage:**

| Property | Token |
|---|---|
| Background | `neutral.surface` |
| Border | `neutral.border` (outlined) |
| Shadow | `shadow-sm` (default); `shadow-md` (elevated) |
| Border radius | `radius-lg` |
| Title text | `neutral.textPrimary` |
| Body text | `neutral.textSecondary` |

---

## Badge / Tag

**Anatomy:** `[icon?]` `[label]`

**Variants:**

| Variant | Use case |
|---|---|
| default | Neutral label / category |
| success | Positive status |
| warning | Cautionary status |
| error | Negative status |
| info | Informational |
| outline | Low-emphasis; no fill |

**States:** default only (badges are non-interactive unless used as filters)

**Token usage:**

| Property | Token |
|---|---|
| Background | Semantic token matching variant (e.g. `semantic.success` at 10% opacity) |
| Text | Semantic token matching variant (full opacity) |
| Border radius | `radius-full` (pill) or `radius-sm` (squared) |
| Font size | Body S or Caption (from type scale) |

---

## Navigation Bar (Top)

**Anatomy:** `[logo / wordmark]` `[nav links]` `[actions (CTA / avatar)?]`

**Variants:**

| Variant | Use case |
|---|---|
| default | Standard top nav with links |
| minimal | Logo + single CTA only |

**States:** default, scrolled (background change or shadow appears), mobile (hamburger menu)

**Token usage:**

| Property | Token |
|---|---|
| Background | `neutral.bg` or `neutral.surface` |
| Border-bottom | `neutral.border` (scrolled state) |
| Shadow | `shadow-sm` (scrolled state) |
| Link text | `neutral.textSecondary` |
| Active link | `brand.primary` |
| CTA | primary Button component |
| Height | from Button Sizing M (height) + padding |

---

_Add new components by copying the block structure above. All token references must exist in `tokens.md`._
