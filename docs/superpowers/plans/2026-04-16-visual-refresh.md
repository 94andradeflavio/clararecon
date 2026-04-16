# Visual Refresh Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the existing theme with a complete CSS custom properties system (`rengine-theme.css`) that applies Roboto/Montserrat typography, consistent spacing, border-radius, shadows, and dark mode — without changing HTML templates or JavaScript.

**Architecture:** A single CSS file (`web/static/css/rengine-theme.css`) defines all design tokens as CSS custom properties, bridges them to Bootstrap 5 `--bs-*` variables, and includes targeted overrides for hardcoded styles. Self-hosted font files replace Cerebri Sans. The only template edit is in `base.html` (replace inline font declarations + add theme CSS link).

**Tech Stack:** CSS3 Custom Properties, Bootstrap 5, Django templates, self-hosted woff2/woff fonts

**Design Spec:** `docs/superpowers/specs/2026-04-16-visual-refresh-design.md`

---

## File Structure

| Action | File | Responsibility |
|--------|------|---------------|
| Create | `web/static/css/rengine-theme.css` | All theme tokens, Bootstrap bridge, dark mode, hardcoded overrides |
| Create | `web/static/fonts/Roboto-Regular.woff2` | Roboto 400 |
| Create | `web/static/fonts/Roboto-Regular.woff` | Roboto 400 fallback |
| Create | `web/static/fonts/Roboto-Medium.woff2` | Roboto 500 |
| Create | `web/static/fonts/Roboto-Medium.woff` | Roboto 500 fallback |
| Create | `web/static/fonts/Roboto-Bold.woff2` | Roboto 700 |
| Create | `web/static/fonts/Roboto-Bold.woff` | Roboto 700 fallback |
| Create | `web/static/fonts/Montserrat-Medium.woff2` | Montserrat 500 |
| Create | `web/static/fonts/Montserrat-Medium.woff` | Montserrat 500 fallback |
| Create | `web/static/fonts/Montserrat-Bold.woff2` | Montserrat 700 |
| Create | `web/static/fonts/Montserrat-Bold.woff` | Montserrat 700 fallback |
| Modify | `web/templates/base/base.html:24-54` | Remove inline Cerebri Sans `@font-face`, add `<link>` to `rengine-theme.css` |
| Modify | `web/static/custom/custom.css:12` | Update `var(--claranet-primary)` → `var(--rng-primary)` |
| Keep | `web/static/custom/claranet-theme.css` | No longer loaded (already not loaded), kept as backup |

---

### Task 1: Download and Install Self-Hosted Fonts

**Files:**
- Create: `web/static/fonts/Roboto-Regular.woff2`, `web/static/fonts/Roboto-Regular.woff`
- Create: `web/static/fonts/Roboto-Medium.woff2`, `web/static/fonts/Roboto-Medium.woff`
- Create: `web/static/fonts/Roboto-Bold.woff2`, `web/static/fonts/Roboto-Bold.woff`
- Create: `web/static/fonts/Montserrat-Medium.woff2`, `web/static/fonts/Montserrat-Medium.woff`
- Create: `web/static/fonts/Montserrat-Bold.woff2`, `web/static/fonts/Montserrat-Bold.woff`

- [ ] **Step 1: Download Roboto font files from Google Fonts**

Download woff2 and woff files for Roboto weights 400, 500, 700 from the google-webfonts-helper API (provides self-hosting bundles). Save to `web/static/fonts/`.

```bash
cd /home/flavioandrade/workbanch/tests/claraRecon/rengine

# Roboto Regular (400) - woff2
curl -L -o web/static/fonts/Roboto-Regular.woff2 "https://fonts.gstatic.com/s/roboto/v47/KFOMCnqEu92Fr1ME7kSn66aGLdTylUAMQXC89YmC2DPNWubEbGmT.woff2"

# Roboto Medium (500) - woff2
curl -L -o web/static/fonts/Roboto-Medium.woff2 "https://fonts.gstatic.com/s/roboto/v47/KFOMCnqEu92Fr1ME7kSn66aGLdTylUAMQXC89YmC2DPNWub2bGmT.woff2"

# Roboto Bold (700) - woff2
curl -L -o web/static/fonts/Roboto-Bold.woff2 "https://fonts.gstatic.com/s/roboto/v47/KFOMCnqEu92Fr1ME7kSn66aGLdTylUAMQXC89YmC2DPNWuYjY2mT.woff2"
```

If the Google Fonts CDN URLs change or fail, use the alternative approach:
```bash
# Alternative: download from google-webfonts-helper
curl -L -o /tmp/roboto.zip "https://gwfh.mranftl.com/api/fonts/roboto?download=zip&subsets=latin&variants=regular,500,700&formats=woff,woff2"
unzip -j /tmp/roboto.zip -d web/static/fonts/ "roboto-v*-latin-regular.*" "roboto-v*-latin-500.*" "roboto-v*-latin-700.*"
# Rename to standard names
mv web/static/fonts/roboto-v*-latin-regular.woff2 web/static/fonts/Roboto-Regular.woff2
mv web/static/fonts/roboto-v*-latin-regular.woff web/static/fonts/Roboto-Regular.woff
mv web/static/fonts/roboto-v*-latin-500.woff2 web/static/fonts/Roboto-Medium.woff2
mv web/static/fonts/roboto-v*-latin-500.woff web/static/fonts/Roboto-Medium.woff
mv web/static/fonts/roboto-v*-latin-700.woff2 web/static/fonts/Roboto-Bold.woff2
mv web/static/fonts/roboto-v*-latin-700.woff web/static/fonts/Roboto-Bold.woff
```

- [ ] **Step 2: Download Montserrat font files from Google Fonts**

```bash
# Montserrat Medium (500) - woff2
curl -L -o web/static/fonts/Montserrat-Medium.woff2 "https://fonts.gstatic.com/s/montserrat/v29/JTUHjIg1_i6t8kCHKm4532VJOt5-QNFgpCuM70w-Y3tcoqK5.woff2"

# Montserrat Bold (700) - woff2
curl -L -o web/static/fonts/Montserrat-Bold.woff2 "https://fonts.gstatic.com/s/montserrat/v29/JTUHjIg1_i6t8kCHKm4532VJOt5-QNFgpCu173w-Y3tcoqK5.woff2"
```

If CDN fails, use alternative:
```bash
curl -L -o /tmp/montserrat.zip "https://gwfh.mranftl.com/api/fonts/montserrat?download=zip&subsets=latin&variants=500,700&formats=woff,woff2"
unzip -j /tmp/montserrat.zip -d web/static/fonts/ "montserrat-v*-latin-500.*" "montserrat-v*-latin-700.*"
mv web/static/fonts/montserrat-v*-latin-500.woff2 web/static/fonts/Montserrat-Medium.woff2
mv web/static/fonts/montserrat-v*-latin-500.woff web/static/fonts/Montserrat-Medium.woff
mv web/static/fonts/montserrat-v*-latin-700.woff2 web/static/fonts/Montserrat-Bold.woff2
mv web/static/fonts/montserrat-v*-latin-700.woff web/static/fonts/Montserrat-Bold.woff
```

- [ ] **Step 3: Verify font files are present and valid**

```bash
ls -la web/static/fonts/Roboto-*.woff* web/static/fonts/Montserrat-*.woff*
```

Expected: 10 font files (5 woff2 + 5 woff), each > 10KB. If any file is missing or 0 bytes, re-download.

If woff files could not be sourced (only woff2 available), that's acceptable — woff2 has >97% browser coverage. Skip woff fallbacks and adjust the `@font-face` declarations in Task 2 to only reference woff2.

- [ ] **Step 4: Commit font files**

```bash
git add web/static/fonts/Roboto-*.woff* web/static/fonts/Montserrat-*.woff*
git commit -m "assets: add self-hosted Roboto and Montserrat font files

Roboto (400/500/700) for body text, Montserrat (500/700) for headings.
Replaces Cerebri Sans as the primary font family."
```

---

### Task 2: Create rengine-theme.css — Font Declarations + Color Variables

**Files:**
- Create: `web/static/css/rengine-theme.css`

- [ ] **Step 1: Create the css directory and start the theme file with @font-face and color variables**

```bash
mkdir -p web/static/css
```

Write `web/static/css/rengine-theme.css` with the following content:

```css
/*
 * reNgine Theme
 * Single source of truth for all design tokens.
 * Colors are IMMUTABLE — do not change color values.
 *
 * Structure:
 *   1. @font-face declarations
 *   2. :root — all CSS custom properties (light mode)
 *   3. [data-theme="dark"] — dark mode overrides
 *   4. Bootstrap bridge — maps --rng-* to --bs-*
 *   5. Typography application
 *   6. Spacing, radius, shadows, modals
 *   7. Hardcoded override selectors
 */

/* ==========================================================================
   1. FONT DECLARATIONS
   ========================================================================== */

@font-face {
  font-family: 'Roboto';
  src: url('../fonts/Roboto-Regular.woff2') format('woff2'),
       url('../fonts/Roboto-Regular.woff') format('woff');
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: 'Roboto';
  src: url('../fonts/Roboto-Medium.woff2') format('woff2'),
       url('../fonts/Roboto-Medium.woff') format('woff');
  font-weight: 500;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: 'Roboto';
  src: url('../fonts/Roboto-Bold.woff2') format('woff2'),
       url('../fonts/Roboto-Bold.woff') format('woff');
  font-weight: 700;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: 'Montserrat';
  src: url('../fonts/Montserrat-Medium.woff2') format('woff2'),
       url('../fonts/Montserrat-Medium.woff') format('woff');
  font-weight: 500;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: 'Montserrat';
  src: url('../fonts/Montserrat-Bold.woff2') format('woff2'),
       url('../fonts/Montserrat-Bold.woff') format('woff');
  font-weight: 700;
  font-style: normal;
  font-display: swap;
}

/* ==========================================================================
   2. CSS CUSTOM PROPERTIES — LIGHT MODE (default)
   ========================================================================== */

:root {
  /* --- Colors (IMMUTABLE — do not change) --- */

  /* Semantic */
  --rng-primary: #DA0812;
  --rng-secondary: #C8D2D8;
  --rng-success: #6DB3A7;
  --rng-warning: #FAE022;
  --rng-danger: #DA0812;
  --rng-info: #5B6293;

  /* Surfaces */
  --rng-bg: #F7F8FC;
  --rng-card: #FFFFFF;
  --rng-white: #FFFFFF;
  --rng-black: #000000;

  /* Text */
  --rng-text: #464650;
  --rng-text-light: #9FA2B4;
  --rng-text-white: #FFFFFF;

  /* Grays */
  --rng-gray-dark: #252733;
  --rng-gray: #464650;
  --rng-gray-light: #9FA2B4;
  --rng-gray-darker: #34454E;

  /* Borders */
  --rng-border: #d9d9d9;

  /* Status */
  --rng-status-ativo: #83D5F6;
  --rng-status-cancelado: #DA0812;
  --rng-status-expirado: #6B719E;
  --rng-status-pendente: #FAE022;
  --rng-status-reprovado: #464650;
  --rng-status-utilizado: #6DB3A7;

  /* Graphs */
  --rng-graph-red: #DA0812;
  --rng-graph-yellow: #FAE022;
  --rng-graph-lightblue: #76D1F5;
  --rng-graph-green: #6DB3A7;
  --rng-graph-mediumblue: #5B6293;
  --rng-graph-darkblue: #233741;
  --rng-graph-grey: #464650;

  /* Pagination */
  --rng-pagination-active: #DA0812;
  --rng-pagination-hover: #EEEEEE;
  --rng-pagination-arrow: #9FA2B4;
  --rng-pagination-arrow-disabled: #C8C8C8;

  /* Shadows & Overlays */
  --rng-shadow-modal: rgba(0, 0, 0, 0.25);
  --rng-shadow-button: rgba(165, 163, 174, 0.3);
  --rng-overlay: rgba(0, 0, 0, 0.25);
  --rng-loading-overlay: #00000080;

  /* --- Typography --- */
  --rng-font-body: 'Roboto', sans-serif;
  --rng-font-heading: 'Montserrat', sans-serif;
  --rng-font-weight-regular: 400;
  --rng-font-weight-medium: 500;
  --rng-font-weight-bold: 700;
  --rng-font-xs: 10px;
  --rng-font-sm: 12px;
  --rng-font-md: 14px;
  --rng-font-lg: 16px;
  --rng-font-xl: 18px;
  --rng-font-xxl: 20px;

  /* --- Spacing --- */
  --rng-space-xs: 4px;
  --rng-space-sm: 8px;
  --rng-space-md: 16px;
  --rng-space-lg: 32px;

  /* --- Modal Widths --- */
  --rng-modal-sm: 300px;
  --rng-modal-md: 590px;
  --rng-modal-lg: 980px;

  /* --- Border Radius --- */
  --rng-radius: 6px;
}
```

- [ ] **Step 2: Verify the file was created correctly**

```bash
head -20 web/static/css/rengine-theme.css
wc -l web/static/css/rengine-theme.css
```

Expected: file starts with the comment block, ~120 lines.

- [ ] **Step 3: Commit**

```bash
git add web/static/css/rengine-theme.css
git commit -m "feat: add rengine-theme.css with font declarations and color variables

Section 1 (@font-face for Roboto/Montserrat) and Section 2 (:root
with all design tokens — colors, typography, spacing, radius, modals,
shadows). Colors are immutable as per spec."
```

---

### Task 3: Add Dark Mode and Bootstrap Bridge to rengine-theme.css

**Files:**
- Modify: `web/static/css/rengine-theme.css` (append after existing content)

- [ ] **Step 1: Append dark mode variables**

Append the following to the end of `web/static/css/rengine-theme.css`:

```css

/* ==========================================================================
   3. DARK MODE OVERRIDES
   ========================================================================== */

[data-theme="dark"] {
  /* Surfaces */
  --rng-bg: #252733;
  --rng-card: #2D2F3A;

  /* Text */
  --rng-text: #E0E0E0;
  --rng-text-light: #9FA2B4;

  /* Borders */
  --rng-border: #3A3C4A;

  /* Shadows (deeper in dark mode) */
  --rng-shadow-button: rgba(0, 0, 0, 0.4);
  --rng-shadow-modal: rgba(0, 0, 0, 0.5);
  --rng-overlay: rgba(0, 0, 0, 0.5);
}

/* ==========================================================================
   4. BOOTSTRAP BRIDGE — maps --rng-* to --bs-*
   ========================================================================== */

:root {
  --bs-primary: var(--rng-primary);
  --bs-secondary: var(--rng-secondary);
  --bs-success: var(--rng-success);
  --bs-warning: var(--rng-warning);
  --bs-danger: var(--rng-danger);
  --bs-info: var(--rng-info);
  --bs-body-bg: var(--rng-bg);
  --bs-body-color: var(--rng-text);
  --bs-body-font-family: var(--rng-font-body);
  --bs-body-font-size: var(--rng-font-md);
  --bs-body-line-height: 1.5;
  --bs-border-color: var(--rng-border);
  --bs-border-radius: var(--rng-radius);
  --bs-border-radius-sm: var(--rng-radius);
  --bs-border-radius-lg: var(--rng-radius);
  --bs-card-bg: var(--rng-card);
  --bs-card-border-radius: var(--rng-radius);
}

/* Dark mode bridge — re-map surface/text variables */
[data-theme="dark"] {
  --bs-body-bg: var(--rng-bg);
  --bs-body-color: var(--rng-text);
  --bs-border-color: var(--rng-border);
  --bs-card-bg: var(--rng-card);
}
```

- [ ] **Step 2: Verify dark mode and bridge sections appended**

```bash
grep -n "DARK MODE" web/static/css/rengine-theme.css
grep -n "BOOTSTRAP BRIDGE" web/static/css/rengine-theme.css
grep -c "data-theme" web/static/css/rengine-theme.css
```

Expected: both section headers found, `data-theme` appears 2 times (dark mode + dark bridge).

- [ ] **Step 3: Commit**

```bash
git add web/static/css/rengine-theme.css
git commit -m "feat: add dark mode overrides and Bootstrap bridge to theme

Dark mode overrides surface/text/shadow variables only — semantic
colors stay unchanged. Bridge maps all --rng-* tokens to --bs-*
so Bootstrap components consume the theme automatically."
```

---

### Task 4: Add Typography, Spacing, and Component Overrides to rengine-theme.css

**Files:**
- Modify: `web/static/css/rengine-theme.css` (append after existing content)

- [ ] **Step 1: Append typography application rules**

Append the following to the end of `web/static/css/rengine-theme.css`:

```css

/* ==========================================================================
   5. TYPOGRAPHY APPLICATION
   ========================================================================== */

body {
  font-family: var(--rng-font-body) !important;
  font-size: var(--rng-font-md) !important;
  font-weight: var(--rng-font-weight-regular);
  line-height: 1.5;
  color: var(--rng-text);
  background-color: var(--rng-bg);
}

h1, h2, h3, h4, h5, h6,
.h1, .h2, .h3, .h4, .h5, .h6 {
  font-family: var(--rng-font-heading) !important;
  font-weight: var(--rng-font-weight-bold);
  color: var(--rng-gray-dark);
}

[data-theme="dark"] h1,
[data-theme="dark"] h2,
[data-theme="dark"] h3,
[data-theme="dark"] h4,
[data-theme="dark"] h5,
[data-theme="dark"] h6,
[data-theme="dark"] .h1,
[data-theme="dark"] .h2,
[data-theme="dark"] .h3,
[data-theme="dark"] .h4,
[data-theme="dark"] .h5,
[data-theme="dark"] .h6 {
  color: var(--rng-text);
}

.page-title {
  font-size: var(--rng-font-xxl) !important;
  font-family: var(--rng-font-heading) !important;
  font-weight: var(--rng-font-weight-bold);
}

.navbar-custom,
.breadcrumb,
.breadcrumb-item,
label,
.form-label,
.nav-link {
  font-family: var(--rng-font-body) !important;
  font-weight: var(--rng-font-weight-medium);
}

table,
.table,
.form-control,
.form-select,
input,
textarea,
select {
  font-family: var(--rng-font-body) !important;
  font-weight: var(--rng-font-weight-regular);
}

.badge {
  font-size: var(--rng-font-sm) !important;
}

/* ==========================================================================
   6. SPACING, RADIUS, SHADOWS, MODALS
   ========================================================================== */

/* Border radius */
.card,
.dropdown-menu,
.modal-content,
.tooltip-inner,
.popover,
.btn,
.form-control,
.form-select,
.input-group-text,
.alert,
.toast,
.progress,
.badge {
  border-radius: var(--rng-radius) !important;
}

.btn-sm {
  border-radius: calc(var(--rng-radius) - 2px) !important;
}

.btn-lg {
  border-radius: calc(var(--rng-radius) + 2px) !important;
}

/* Card spacing */
.card {
  background-color: var(--rng-card);
  border-color: var(--rng-border);
}

/* Modal widths */
.modal-sm {
  max-width: var(--rng-modal-sm) !important;
}

.modal-dialog {
  max-width: var(--rng-modal-md);
}

.modal-lg,
.modal-xl {
  max-width: var(--rng-modal-lg) !important;
}

.modal-content {
  box-shadow: 0 4px 24px var(--rng-shadow-modal);
}

.modal-backdrop {
  background-color: var(--rng-overlay);
}

/* Dropdown shadows */
.dropdown-menu {
  box-shadow: 0 4px 24px var(--rng-shadow-modal);
  border-color: var(--rng-border);
}
```

- [ ] **Step 2: Append hardcoded override selectors**

Continue appending to `web/static/css/rengine-theme.css`:

```css

/* ==========================================================================
   7. HARDCODED OVERRIDE SELECTORS
   ========================================================================== */

/* Primary color overrides */
.navbar-light .navbar-nav .nav-link.active,
.navbar-light .navbar-nav .show > .nav-link,
#sidebar-menu > ul > li > a:active,
#sidebar-menu > ul > li > a:focus,
#sidebar-menu > ul > li > a:hover,
#sidebar-menu > ul > li > a.mm-active,
#sidebar-menu .menuitem-active > a,
#sidebar-menu .menuitem-active .active,
.nav-second-level li a:focus,
.nav-second-level li a:hover,
.nav-second-level li.active > a,
.menu-arrow,
.text-primary {
  color: var(--rng-primary) !important;
}

.bg-primary,
.btn-primary,
.form-check-input:checked,
.page-item.active .page-link,
.badge-primary,
.dropdown-item.active,
.dropdown-item:active {
  background-color: var(--rng-primary) !important;
  border-color: var(--rng-primary) !important;
}

.btn-primary {
  background-color: var(--rng-primary) !important;
  border-color: var(--rng-primary) !important;
}

.btn-primary:hover,
.btn-primary:focus {
  background-color: var(--rng-primary) !important;
  border-color: var(--rng-primary) !important;
  box-shadow: 0 2px 8px var(--rng-shadow-button) !important;
  filter: brightness(0.9);
}

.btn-outline-primary {
  color: var(--rng-primary) !important;
  border-color: var(--rng-primary) !important;
}

.btn-outline-primary:hover,
.btn-outline-primary:active {
  background-color: var(--rng-primary) !important;
  border-color: var(--rng-primary) !important;
  color: var(--rng-white) !important;
}

/* Form focus states */
.form-control:focus,
.form-select:focus {
  border-color: var(--rng-primary) !important;
  box-shadow: 0 0 0 0.2rem rgba(218, 8, 18, 0.25) !important;
}

/* Semantic color overrides */
.text-success { color: var(--rng-success) !important; }
.text-warning { color: var(--rng-warning) !important; }
.text-danger { color: var(--rng-danger) !important; }
.text-info { color: var(--rng-info) !important; }

.bg-success { background-color: var(--rng-success) !important; }
.bg-warning { background-color: var(--rng-warning) !important; }
.bg-danger { background-color: var(--rng-danger) !important; }
.bg-info { background-color: var(--rng-info) !important; }

/* Pagination */
.page-item.active .page-link {
  background-color: var(--rng-pagination-active) !important;
  border-color: var(--rng-pagination-active) !important;
}

.page-link:hover {
  background-color: var(--rng-pagination-hover);
}

.page-link {
  color: var(--rng-text);
  border-radius: var(--rng-radius) !important;
}

/* Select2 integration */
.select2-container--default .select2-selection--multiple .select2-selection__choice {
  background-color: var(--rng-primary) !important;
  border-radius: var(--rng-radius) !important;
}

/* Accordion primary color override */
.accordion-button:not(.collapsed) {
  color: var(--rng-primary);
  background-color: rgba(218, 8, 18, 0.05);
}

/* Nav tabs active */
.nav-tabs .nav-link.active,
.nav-tabs .nav-item.show .nav-link {
  border-bottom-color: var(--rng-primary);
}

/* Links */
a {
  color: var(--rng-primary);
}

a:hover {
  color: var(--rng-primary);
  filter: brightness(0.8);
}

/* DataTables integration */
table.dataTable thead th,
table.dataTable thead td {
  border-bottom-color: var(--rng-border) !important;
}

table.dataTable tbody tr {
  background-color: var(--rng-card);
}

[data-theme="dark"] table.dataTable tbody tr {
  background-color: var(--rng-card);
  color: var(--rng-text);
}

/* SweetAlert2 confirm button */
.swal2-confirm.swal2-styled {
  background-color: var(--rng-primary) !important;
  border-radius: var(--rng-radius) !important;
}
```

- [ ] **Step 3: Verify the complete theme file**

```bash
wc -l web/static/css/rengine-theme.css
grep -c "========" web/static/css/rengine-theme.css
```

Expected: ~350-400 lines, 7 section separators (sections 1-7).

- [ ] **Step 4: Commit**

```bash
git add web/static/css/rengine-theme.css
git commit -m "feat: add typography, spacing, and hardcoded overrides to theme

Sections 5-7: typography application (Roboto body, Montserrat headings),
spacing/radius/shadow/modal rules, and hardcoded override selectors for
Bootstrap components, Select2, DataTables, SweetAlert2, accordion, and
pagination."
```

---

### Task 5: Update base.html — Replace Font Declarations and Add Theme Link

**Files:**
- Modify: `web/templates/base/base.html:24-57`

- [ ] **Step 1: Replace the inline Cerebri Sans `<style>` block with the rengine-theme.css link**

In `web/templates/base/base.html`, replace lines 24-57 (the entire `<style media="screen">...</style>` block and the comment after it) with a single `<link>` tag:

**Replace this** (lines 24-57):
```html
    <style media="screen">
      @font-face {
        font-family: "Cerebri Sans,sans-serif";
        src: url("{% static 'fonts/cerebrisans-light.eot' %}");
        src: local("Cerebri-sans Light"), url("{% static 'fonts/cerebrisans-light.woff' %}") format("woff");
        font-weight: 300; }

      @font-face {
        font-family: "Cerebri Sans,sans-serif";
        src: url("{% static 'fonts/cerebrisans-regular.eot' %}");
        src: local("Cerebri-sans Regular"), url("{% static 'fonts/cerebrisans-regular.woff' %}") format("woff");
        font-weight: 400; }

      @font-face {
        font-family: "Cerebri Sans,sans-serif";
        src: url("{% static 'fonts/cerebrisans-medium.eot' %}");
        src: local("Cerebri-sans Medium"), url("{% static 'fonts/cerebrisans-medium.woff' %}") format("woff");
        font-weight: 500; }

      @font-face {
        font-family: "Cerebri Sans,sans-serif";
        src: url("{% static 'fonts/cerebrisans-semibold.eot' %}");
        src: local("Cerebri-sans Semibold"), url("{% static 'fonts/cerebrisans-semibold.woff' %}") format("woff");
        font-weight: 600; }

      @font-face {
        font-family: "Cerebri Sans,sans-serif";
        src: url("{% static 'fonts/cerebrisans-bold.eot' %}");
        src: local("Cerebri-sans Bold"), url("{% static 'fonts/cerebrisans-bold.woff' %}") format("woff");
        font-weight: 700; }
    </style>
    <!-- load any page wise jquery or stylesheet -->
```

**With this:**
```html
    <link href="{% static 'css/rengine-theme.css' %}" rel="stylesheet" type="text/css" />
    <!-- load any page wise jquery or stylesheet -->
```

- [ ] **Step 2: Verify the edit was applied correctly**

```bash
grep -n "rengine-theme" web/templates/base/base.html
grep -c "Cerebri" web/templates/base/base.html
```

Expected: `rengine-theme` found on one line, `Cerebri` count is 0.

- [ ] **Step 3: Commit**

```bash
git add web/templates/base/base.html
git commit -m "feat: load rengine-theme.css, remove inline Cerebri Sans declarations

Replace the inline <style> block with Cerebri Sans @font-face
with a <link> to the new rengine-theme.css. Font declarations
for Roboto/Montserrat now live in the theme file."
```

---

### Task 6: Update custom.css Variable Reference

**Files:**
- Modify: `web/static/custom/custom.css:12`

- [ ] **Step 1: Replace `--claranet-primary` with `--rng-primary` in custom.css**

In `web/static/custom/custom.css`, line 12, replace:
```css
  color: var(--claranet-primary);
```

With:
```css
  color: var(--rng-primary);
```

- [ ] **Step 2: Verify the change**

```bash
grep -n "claranet" web/static/custom/custom.css
grep -n "rng-primary" web/static/custom/custom.css
```

Expected: no `claranet` references found, `rng-primary` found on line 12.

- [ ] **Step 3: Commit**

```bash
git add web/static/custom/custom.css
git commit -m "fix: update CSS variable reference from claranet to rng namespace

Replace --claranet-primary with --rng-primary in custom.css to
match the new theme variable naming convention."
```

---

### Task 7: Visual Verification

No files modified in this task — this is a manual verification step.

- [ ] **Step 1: Start the dev environment**

```bash
make up
```

Wait for all services to start. Open the browser and navigate to the reNgine dashboard.

- [ ] **Step 2: Verify light mode**

Check the following in the browser:
1. **Typography** — body text uses Roboto, headings use Montserrat (inspect via DevTools → Computed → font-family)
2. **Colors** — primary red (#DA0812) appears on active nav links, buttons, badges
3. **Background** — page background is #F7F8FC, cards are #FFFFFF
4. **Border radius** — cards, buttons, modals, badges all have 6px radius
5. **Spacing** — cards have 16px padding, page sections have 32px margins
6. **Modals** — open any modal, verify default width is ~590px
7. **Forms** — click into an input, verify red focus ring appears
8. **DataTables** — check that table borders match theme, pagination uses red active state

- [ ] **Step 3: Verify dark mode**

Toggle dark mode via the theme switch:
1. **Background** — page background changes to #252733, cards to #2D2F3A
2. **Text** — body text is #E0E0E0, secondary text is #9FA2B4
3. **Borders** — borders change to #3A3C4A
4. **Semantic colors** — primary red, success green, warning yellow remain the same
5. **Modals** — backdrop is darker (rgba(0,0,0,0.5))

- [ ] **Step 4: Check browser DevTools console for errors**

Open DevTools → Console. Verify no 404 errors for font files or CSS files.

Expected: No errors related to `rengine-theme.css`, `Roboto-*.woff2`, or `Montserrat-*.woff2`.

- [ ] **Step 5: If issues found, fix and commit**

If any visual issues are found, fix them in `web/static/css/rengine-theme.css` and commit:
```bash
git add web/static/css/rengine-theme.css
git commit -m "fix: address visual issues found during verification"
```
