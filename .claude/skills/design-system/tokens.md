# Design Tokens — Fast-8

Token values are decided by Javier. Fill in the **Value** column; then mirror it in `tailwind.config.js`.

---

## Color — Brand

Primary, secondary, and accent colors. Javier decides the full set.

| Token name | Tailwind key | Value |
|---|---|---|
| brand-primary | `brand.primary` | [PLACEHOLDER] |
| brand-secondary | `brand.secondary` | [PLACEHOLDER] |
| brand-accent | `brand.accent` | [PLACEHOLDER] |

---

## Color — Semantic

Communicates status. One hex per meaning — avoid ambiguity.

| Token name | Tailwind key | Value |
|---|---|---|
| semantic-success | `semantic.success` | [PLACEHOLDER] |
| semantic-warning | `semantic.warning` | [PLACEHOLDER] |
| semantic-error | `semantic.error` | [PLACEHOLDER] |
| semantic-info | `semantic.info` | [PLACEHOLDER] |

---

## Color — Neutral

Surface, background, borders, and text hierarchy. Usually greyscale or near-neutral.

| Token name | Tailwind key | Value |
|---|---|---|
| neutral-bg | `neutral.bg` | [PLACEHOLDER] |
| neutral-surface | `neutral.surface` | [PLACEHOLDER] |
| neutral-border | `neutral.border` | [PLACEHOLDER] |
| neutral-text-primary | `neutral.textPrimary` | [PLACEHOLDER] |
| neutral-text-secondary | `neutral.textSecondary` | [PLACEHOLDER] |
| neutral-text-disabled | `neutral.textDisabled` | [PLACEHOLDER] |

---

## Spacing Scale

Base unit: **8px**. All steps must be multiples of 4px or 8px.

| Step | px value | Tailwind key |
|---|---|---|
| spacing-1 | 4px | `1` |
| spacing-2 | 8px | `2` |
| spacing-3 | 12px | `3` |
| spacing-4 | 16px | `4` |
| spacing-6 | 24px | `6` |
| spacing-8 | 32px | `8` |
| spacing-12 | 48px | `12` |
| spacing-16 | 64px | `16` |
| spacing-24 | 96px | `24` |
| spacing-32 | 128px | `32` |

_Tailwind keys match the step name; spacing-2 → `p-2`, `m-2`, etc._

---

## Typography — Type Scale

One row per text role. Font family is Javier's choice — fill in when decided.

| Role | Font family | Weight | Size (px) | Line height | Letter spacing |
|---|---|---|---|---|---|
| Display | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| H1 | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| H2 | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| H3 | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| H4 | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| Body L | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| Body M | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| Body S | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| Caption | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| Label | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |

---

## Button Sizing

Three sizes. Padding is written as `horizontal / vertical`.

| Size | Height (px) | Padding H (px) | Padding V (px) | Font size (px) | Icon size (px) |
|---|---|---|---|---|---|
| S | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| M | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |
| L | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] | [PLACEHOLDER] |

---

## Border Radius

| Token name | px value | Tailwind key |
|---|---|---|
| radius-none | 0px | `rounded-none` |
| radius-sm | [PLACEHOLDER] | `rounded-sm` |
| radius-md | [PLACEHOLDER] | `rounded-md` |
| radius-lg | [PLACEHOLDER] | `rounded-lg` |
| radius-full | 9999px | `rounded-full` |

---

## Shadows

| Token name | CSS value | Tailwind key |
|---|---|---|
| shadow-none | none | `shadow-none` |
| shadow-sm | [PLACEHOLDER] | `shadow-sm` |
| shadow-md | [PLACEHOLDER] | `shadow-md` |
| shadow-lg | [PLACEHOLDER] | `shadow-lg` |

---

## Breakpoints

Match or override Tailwind defaults. Min-width values.

| Name | Min-width (px) | Tailwind key |
|---|---|---|
| sm | [PLACEHOLDER] | `sm` |
| md | [PLACEHOLDER] | `md` |
| lg | [PLACEHOLDER] | `lg` |
| xl | [PLACEHOLDER] | `xl` |
