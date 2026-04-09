# Specification: Tema Claranet para reNgine

**Date:** 2026-03-27
**Status:** Draft
**Author:** opencode

## Overview

Substituir as cores do tema padrão do reNgine pelo tema Claranet (light e dark), modificando os arquivos CSS originais do projeto.

## Scope

- Tema Light (padrão) → Claranet Light
- Tema Dark → Claranet Dark (escuro-acinzentado)
- Fontes: Nunito → Roboto (body) + Montserrat (headings)

## Files to Modify

| File | Size |
|------|------|
| `web/static/bootstrap/bootstrap.min.css` | 193KB |
| `web/static/bootstrap/bootstrap-dark.min.css` | 193KB |
| `web/static/assets/css/app.min.css` | 123KB |
| `web/static/assets/css/app-dark.min.css` | 123KB |

## Theme Colors

### Claranet Light

| Variable | Old | New |
|----------|-----|-----|
| `--bs-primary` | #3283f6 | #DA0812 |
| `--bs-secondary` | #6c757d | #C8D2D8 |
| `--bs-success` | #44cf9c | #6DB3A7 |
| `--bs-warning` | #fcc015 | #FAE022 |
| `--bs-danger` | #f86262 | #DA0812 |
| `--bs-info` | #43bee1 | #5B6293 |
| `--bs-body-bg` | #f5f6f9 | #F7F8FC |
| `--bs-body-color` | #6c757d | #464650 |
| `--bs-gray-100` | #f3f7f9 | #F7F8FC |
| `--bs-gray-200` | #f7f7f7 | #EEEEEE |
| `--bs-gray-300` | #dee2e6 | #d9d9d9 |
| `--bs-border-color` | #dee2e6 | #d9d9d9 |

### Claranet Dark

| Variable | Value |
|----------|-------|
| `--bs-primary` | #DA0812 |
| `--bs-secondary` | #464650 |
| `--bs-success` | #6DB3A7 |
| `--bs-warning` | #FAE022 |
| `--bs-danger` | #DA0812 |
| `--bs-info` | #5B6293 |
| `--bs-body-bg` | #252733 |
| `--bs-body-color` | #9FA2B4 |
| `--bs-gray-100` | #323a46 |
| `--bs-gray-900` | #1E1E2E |

### Sidebar/Menu Colors (app.min.css)

Light:
- Active/hover: #DA0812 (was #3283f6)
- Text: #464650

Dark:
- Active/hover: #DA0812 (was #00acc1)
- Text: #9FA2B4

## Approach

1. Search and replace hex colors in all 4 CSS files
2. Update CSS variables in `:root`
3. Replace font references

## Testing

- Visual verification in browser
- Toggle dark mode to verify both themes