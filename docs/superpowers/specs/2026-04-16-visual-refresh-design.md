# reNgine Visual Refresh — Design Spec

## Goal

Replace the existing `claranet-theme.css` with a new, complete theme file (`rengine-theme.css`) that applies the provided design tokens (colors, typography, spacing, border-radius, modal widths, shadows) to the entire Django web interface. The current layout and structure remain unchanged.

## Approach

**CSS Variables Override** — A single new CSS file defines all theme tokens as CSS custom properties, bridges them to Bootstrap 5 variables, and includes targeted overrides for hardcoded styles. No templates are modified beyond swapping the `<link>` reference in `base.html`.

## Theme Tokens

### Colors (immutable — do not change)

```
--rng-primary: #DA0812
--rng-secondary: #C8D2D8
--rng-success: #6DB3A7
--rng-warning: #FAE022
--rng-danger: #DA0812
--rng-info: #5B6293
--rng-bg: #F7F8FC
--rng-card: #FFFFFF
--rng-white: #FFFFFF
--rng-black: #000000
--rng-text: #464650
--rng-text-light: #9FA2B4
--rng-text-white: #FFFFFF
--rng-border: #d9d9d9
--rng-gray-dark: #252733
--rng-gray: #464650
--rng-gray-darker: #34454E

Status:
--rng-status-ativo: #83D5F6
--rng-status-cancelado: #DA0812
--rng-status-expirado: #6B719E
--rng-status-pendente: #FAE022
--rng-status-reprovado: #464650
--rng-status-utilizado: #6DB3A7

Graphs:
--rng-graph-red: #DA0812
--rng-graph-yellow: #FAE022
--rng-graph-lightblue: #76D1F5
--rng-graph-green: #6DB3A7
--rng-graph-mediumblue: #5B6293
--rng-graph-darkblue: #233741
--rng-graph-grey: #464650

Pagination:
--rng-pagination-active: #DA0812
--rng-pagination-hover: #EEEEEE
--rng-pagination-arrow: #9FA2B4
--rng-pagination-arrow-disabled: #C8C8C8

Overlays:
--rng-shadow-modal: rgba(0, 0, 0, 0.25)
--rng-shadow-button: rgba(165, 163, 174, 0.3)
--rng-overlay: rgba(0, 0, 0, 0.25)
--rng-loading-overlay: #00000080
```

### Typography

**Fonts:**
- Body: `'Roboto', sans-serif` — self-hosted, weights 400/500/700
- Headings: `'Montserrat', sans-serif` — self-hosted, weights 500/700

**Scale:**

| Token | Value | Usage |
|-------|-------|-------|
| `--rng-font-xs` | 10px | Badges, tooltips |
| `--rng-font-sm` | 12px | Labels, captions, table cells |
| `--rng-font-md` | 14px | Body text, inputs, nav links |
| `--rng-font-lg` | 16px | Subtitles, card headers |
| `--rng-font-xl` | 18px | Section headings |
| `--rng-font-xxl` | 20px | Page titles |

**Application:**
- `body` — Roboto 400, 14px, line-height 1.5
- `h1–h6` — Montserrat 700
- Navbar, breadcrumb, labels — Roboto 500
- Tables, inputs — Roboto 400

### Spacing

| Token | Value | Usage |
|-------|-------|-------|
| `--rng-space-xs` | 4px | Icon-text gaps, badge padding |
| `--rng-space-sm` | 8px | Table cell padding, inline button gaps |
| `--rng-space-md` | 16px | Card padding, input padding, internal section margins |
| `--rng-space-lg` | 32px | Page section margins, main container padding |

### Border Radius

`--rng-radius: 6px` applied to: cards, dropdowns, modals, tooltips, popovers, buttons, inputs, selects, badges, alerts, toasts, progress bars.

### Modal Widths

| Token | Value | Bootstrap override |
|-------|-------|--------------------|
| `--rng-modal-sm` | 300px | `.modal-sm` |
| `--rng-modal-md` | 590px | `.modal-dialog` (default) |
| `--rng-modal-lg` | 980px | `.modal-lg`, `.modal-xl` |

### Shadows

- Button hover/focus: `0 2px 8px var(--rng-shadow-button)`
- Modals and dropdowns: `0 4px 24px var(--rng-shadow-modal)`
- Modal backdrop: `var(--rng-overlay)`

## Dark Mode

Selector: `[data-theme="dark"]`

Overridden variables (surfaces and text only):
```
--rng-bg: #252733
--rng-card: #2D2F3A
--rng-text: #E0E0E0
--rng-text-light: #9FA2B4
--rng-border: #3A3C4A
--rng-shadow-button: rgba(0, 0, 0, 0.4)
--rng-shadow-modal: rgba(0, 0, 0, 0.5)
--rng-overlay: rgba(0, 0, 0, 0.5)
```

Bootstrap bridge variables are re-mapped inside the dark selector.

Semantic colors (primary, success, warning, danger, info), graph colors, and status colors remain unchanged in dark mode.

## Bootstrap Bridge

Maps `--rng-*` to `--bs-*` so all Bootstrap 5 components automatically consume the theme:

```
--bs-primary → --rng-primary
--bs-secondary → --rng-secondary
--bs-success → --rng-success
--bs-warning → --rng-warning
--bs-danger → --rng-danger
--bs-info → --rng-info
--bs-body-bg → --rng-bg
--bs-body-color → --rng-text
--bs-body-font-family → --rng-font-body
--bs-body-font-size → --rng-font-md
--bs-body-line-height → 1.5
--bs-border-color → --rng-border
--bs-border-radius → --rng-radius
--bs-card-bg → --rng-card
```

## Hardcoded Override Selectors

Final section of the theme file — forces consumption of theme variables on elements with hardcoded Bootstrap styles:

- `.btn-primary`, `.bg-primary`, `.text-primary` → `var(--rng-primary)`
- `.form-control:focus`, `.form-select:focus` → border and box-shadow with primary
- `.page-item.active .page-link` → primary
- `.text-success/warning/danger/info` and `.bg-*` equivalents
- Navbar active states, sidebar active states, menu arrows
- `.form-check-input:checked`, `.dropdown-item.active`

## Files Changed

| Action | File | Description |
|--------|------|-------------|
| Create | `web/static/css/rengine-theme.css` | Complete theme file (variables + bridge + dark mode + overrides) |
| Create | `web/static/fonts/Roboto-Regular.woff2` + `.woff` | Roboto 400 |
| Create | `web/static/fonts/Roboto-Medium.woff2` + `.woff` | Roboto 500 |
| Create | `web/static/fonts/Roboto-Bold.woff2` + `.woff` | Roboto 700 |
| Create | `web/static/fonts/Montserrat-Medium.woff2` + `.woff` | Montserrat 500 |
| Create | `web/static/fonts/Montserrat-Bold.woff2` + `.woff` | Montserrat 700 |
| Edit | `web/templates/base/base.html` | Replace `claranet-theme.css` link with `rengine-theme.css` |
| Keep | `web/static/custom/claranet-theme.css` | No longer loaded, kept as backup |

## CSS Load Order (in base.html)

1. `bootstrap.min.css`
2. `app.min.css`
3. `bootstrap-dark.min.css`
4. `app-dark.min.css`
5. `icons.min.css`
6. Plugins (Snackbar, SweetAlert, DataTables, etc.)
7. `custom.css`
8. **`rengine-theme.css`** (last — highest precedence)

## What Does NOT Change

- No HTML templates modified (except the single `<link>` swap in `base.html`)
- No JavaScript files modified
- No plugins or libraries added/removed
- No layout or navigation structure changes
- No colors changed from the provided theme
