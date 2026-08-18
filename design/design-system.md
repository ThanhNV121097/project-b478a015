# Design System — Hello Word website

> Source of truth: the approved `index.html` (preview: http://localhost:8080/design/b478a015-f722-4b82-82c2-f37d1820af57).
> Every value below is extracted from it. Changing a value here without
> changing the approved design is a defect.

Last updated: 2026-05-27

## 1. Foundations

### 1.1 Color

Semantic tokens. Name by job, never by hue.

| Token | Value | Used for |
|---|---|---|
| `--color-bg` | `#ffffff` | Page background |
| `--color-ink` | `#111111` | Body text, primary emphasis, focus ring |
| `--color-muted` | `#6b6b6b` | Secondary text, captions, status dot |
| `--color-line` | `#e5e5e5` | Default border, divider |

Tokens the template reserves but this design does not use, and therefore does
not define: `--color-surface`, `--color-surface-raised`, `--color-primary`,
`--color-primary-text`, `--color-success`, `--color-warning`, `--color-danger`.
Do not invent values for them — there is no card, no filled action button, and
no success/warning/danger color in the approved mockup. The error state reuses
`--color-ink` text and `--color-muted` dot.

#### Contrast audit

Every text-on-background pair actually used. Body text ≥ 4.5:1, large text (≥ 18.66px bold or ≥ 24px) ≥ 3:1, UI borders ≥ 3:1.

| Foreground | Background | Ratio | Passes |
|---|---|---|---|
| `--color-ink` `#111111` | `--color-bg` `#ffffff` | `18.9:1` | AA |
| `--color-muted` `#6b6b6b` | `--color-bg` `#ffffff` | `5.3:1` | AA |
| `--color-bg` `#ffffff` | `--color-ink` `#111111` (pressed button) | `18.9:1` | AA |
| `--color-line` `#e5e5e5` | `--color-bg` `#ffffff` (default button border) | `1.3:1` | Non-text, decorative — fails 3:1 UI boundary |

The `--color-line` border is decorative only: button state is carried by
`aria-pressed` and label text, not by border color. Hover swaps the border to
`--color-ink`, which restores a visible boundary.

### 1.2 Spacing

Base unit: `4px`. Every margin, padding, and gap in the product uses one of these.

| Token | Value |
|---|---|
| `--space-2` | `8px` |
| `--space-3` | `12px` |
| `--space-4` | `16px` |
| `--space-6` | `24px` |
| `--space-8` | `32px` |

`--space-1` (`4px`) is reserved but unused in the current design.

### 1.3 Typography

Font family (single family for body and headings, loaded as the system stack —
no webfont):

- Body & headings: `system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial, sans-serif`
- Mono: not used.

| Token | Size | Line height | Weight | Used for |
|---|---|---|---|---|
| `--text-xs` | `12px` (`0.75rem`) | default | `400` | Hint caption |
| `--text-sm` | `14px` (`0.875rem`) | default | `400` | Status label, button label |
| `--text-display` | `clamp(40px, 8vw, 80px)` | `1.05` | `700` | h1 — the "Hello Word" heading |

The display heading uses `letter-spacing: -0.02em`. There is no base 16px body
run in the visible copy — the only heading is the display level, and all body
copy is `--text-xs` or `--text-sm`. Heading levels are used in order (a single
h1, nothing skipped).

### 1.4 Radius, border, shadow, motion

| Token | Value | Used for |
|---|---|---|
| `--radius-sm` | `8px` (`0.5rem`) | Button |
| `--radius-full` | `50%` | Status dot |
| `--border-width` | `1px` | Button border |
| `--shadow-*` | none | No shadow is used anywhere in the design |
| `--duration-fast` | `120ms` | Hover/focus border and background |
| `--easing` | `ease` | All transitions |

No shadow, and no `--radius-md` / `--radius-lg`. The status dot is the only
pill-shaped element.

### 1.5 Layout and breakpoints

No media queries are used. The design is responsive without breakpoints:

- `body` is a full-viewport grid with `place-items: center` (`min-height: 100vh`).
- `main` is `width: 100%`, `max-width: 40rem` (`640px`), centered text.
- The heading scales fluidly via `clamp()` rather than a breakpoint ramp.
- The demo controls wrap via `flex-wrap`.

There is therefore no named breakpoint set, no column grid, and no gutter
system.

Z-index scale (only these values are allowed):

| Layer | Value |
|---|---|
| Base | `0` |

No element in the design sets a `z-index`; everything sits on the base layer.

## 2. Components

One subsection per reusable component. Every component lists **all** states.

### 2.1 Display screen

**Purpose** — the single screen of the product: show the database-fetched
greeting, plus a live status line saying where it came from. Used on the only
page; not a reusable card, do not add surrounding chrome.

**Anatomy** — `[h1 greeting] [status indicator] [demo controls] [hint]`, all
centered.

**Variants** — none; the screen is fixed and centered.

**Sizes** — `main` is `max-width: 640px`, `padding: 32px`, text-align center.

**States** — the greeting text and status label change together per fetch state.

| State | Visual change | Tokens |
|---|---|---|
| Loading | Heading shows `…`, label "Loading from database" | `--color-ink`, `--color-muted` |
| Loaded | Heading shows "Hello Word", label "Loaded from database" | `--color-ink`, `--color-muted` |
| Empty | Heading shows `—`, label "No text found in database" | `--color-ink`, `--color-muted` |
| Error | Heading shows "Error", label "Could not reach the database" | `--color-ink`, `--color-muted` |

**Accessibility** — the heading carries `aria-live="polite"` so state changes
are announced; the status line carries `role="status"`. The greeting is a
single `h1`; the status line is a `p`, not a heading.

### 2.2 Status indicator

**Purpose** — a dot plus a one-line label that reports the fetch state.
Not a standalone component: it is the screen's status line.

**Anatomy** — `[dot] [label]`, inline-flex, `8px` gap, `14px` text.

**Variants** — one variant; color does not change with state.

**Sizes** — dot `8×8px` (`50%` radius), label `--text-sm`.

**States**

| State | Visual change | Tokens |
|---|---|---|
| Loading | Label "Loading from database", dot `--color-muted` | `--color-muted` |
| Loaded | Label "Loaded from database", dot `--color-muted` | `--color-muted` |
| Empty | Label "No text found in database", dot `--color-muted` | `--color-muted` |
| Error | Label "Could not reach the database", dot `--color-muted` | `--color-muted` |

The dot is `aria-hidden="true"` (decorative — state is carried by the label
text, which is the accessible name).

**Accessibility** — `role="status"` on the container announces label changes to
screen readers. The dot is decorative.

### 2.3 Button

**Purpose** — the demo controls that preview the four fetch states. In the
shipped product these are removed; they exist only to make the four states
reviewable in the mockup.

**Anatomy** — `[label]` only; no icon.

**Variants**

| Variant | Tokens | When to use |
|---|---|---|
| Default (toggle) | `--color-bg` bg, `--color-ink` text, `1px --color-line` border, `--radius-sm` | Selecting a preview state |
| Pressed (`aria-pressed="true"`) | `--color-ink` bg, `--color-bg` text, `--color-ink` border | The currently active state |

**Sizes** — one size: `padding: 8px 16px`, text `--text-sm`, height ~`35px`.

**States**

| State | Visual change | Tokens |
|---|---|---|
| Default | White bg, dark text, light border | `--color-bg`, `--color-ink`, `--color-line` |
| Hover | Border darkens to ink | `--color-ink` border |
| Focus (keyboard) | Visible `2px` outline, `2px` offset, `:focus-visible` only | `--color-ink` (`--color-focus` = `--color-ink`) |
| Active / pressed | Inverted: dark bg, white text, dark border | `--color-ink` bg, `--color-bg` text |
| Disabled | Not designed | — |
| Loading | Not applicable — demo control only | — |
| Error | Not applicable — demo control only | — |
| Empty | Not applicable — demo control only | — |

**Accessibility** — native `<button type="button">`. Pressed state uses
`aria-pressed` (a real attribute toggle, not a class), so it is announced. Focus
is `:focus-visible` with a visible outline — never removed without replacement.
Hit target is below the 44×44px minimum (button height ~35px); recorded in
Known deviations.

## 3. Content and formatting

- **Voice and tone** — plain, literal, one short sentence. The product shows a
  single greeting and says exactly where it came from; no marketing voice.
- **Dates, numbers, currency** — none appear in this design.
- **Capitalization** — sentence case for labels and buttons ("Loaded from
  database", "Loading"). The greeting "Hello Word" is the stakeholder's own
  wording and is reproduced verbatim, including its spelling.
- **Empty / error wording** — empty state is "No text found in database" (tells
  what is missing); error state is "Could not reach the database" (tells the
  cause). Neither is a blank area.

## 4. Known deviations

| Where | Deviation | Why it stands | Follow-up |
|---|---|---|---|
| Button hit target | Button height ~35px, below the 44×44px touch minimum | Demo controls are a mockup-only affordance, not part of the shipped product | Confirm size if these ever ship as real controls |
| Decorative border contrast | `--color-line` on `--color-bg` is ~1.3:1, below the 3:1 UI-boundary guideline | Border is decorative; state is carried by text and `aria-pressed` | If a form input uses this border, darken it |
| No 16px base body run | All body copy is 12–14px; the only large text is the display heading | Single-purpose screen with one heading | Add `--text-base` if copy grows |

## 5. Change log

| Date | Change | Design PR |
|---|---|---|
| 2026-05-27 | Initial design system extracted from approved mockup | — |
