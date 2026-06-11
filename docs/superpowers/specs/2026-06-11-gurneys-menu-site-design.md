# Gurney's Oyster Bar — Menu Site Design

## Overview

A single-page static website for Gurney's Oyster Bar, Thornham, Norfolk. Deployed to Railway. Viewed primarily on mobile via QR code. No framework, no build step — plain HTML + CSS + vanilla JS in a single `index.html`.

## Files

| File | Purpose |
|---|---|
| `index.html` | Everything: structure, styles, JS logic |
| `placeholder-logo.svg` | Oyster shell outline in slate (#2c3540) |
| `railway.toml` | Static file serve config for Railway |
| `README.md` | Setup guide: Google Sheet publishing, CONFIG instructions |

## Config

At the top of the `<script>` block in `index.html`:

```js
const CONFIG = {
  sheetUrl: '',        // published Google Sheet CSV URL
}
```

## CSV Format

The published Google Sheet contains:

1. **Optional settings rows at the top** (stop parsing when a non-keyword row is found):
   - `PDF OVERRIDE` (col A) with `ON`/`OFF` (col B) and a Google Drive file URL (col C)
   - `AUTO REFRESH` (col A) with `ON`/`OFF` (col B) and interval in seconds (col C)

2. **Menu header and rows** with columns:

| Column | Example |
|---|---|
| Section | Oysters |
| Subsection | *Optional* — Native |
| Item Name | Native Oysters × 6 |
| Price | £18 *or* Small £12 \| Large £18 |
| Allergens | molluscs, dairy |

The `Subsection` column is optional (may be empty). The `Price` cell may contain multiple variants separated by `|`, each formatted as `Label £price`.

## Rendering Logic

### Sheet vs. PDF

The site checks the `PDF OVERRIDE` row each time it fetches:

- **If `PDF OVERRIDE` is ON and has a valid Drive file URL**: embed the PDF via `<iframe src="https://drive.google.com/file/d/FILE_ID/preview">` (header remains visible, embed fills remaining viewport height)
- **If `PDF OVERRIDE` is OFF or missing**: render the menu from CSV (sections, subsections if present, items with prices and allergens)

### Menu rendering (sheet mode)

- `fetch()` the published Google Sheet CSV URL
- Parse CSV client-side (no library — implement a minimal RFC 4180 parser that handles quoted fields)
- Show a loading state while fetching; show a user-friendly error if fetch fails
- Extract settings rows (PDF OVERRIDE, AUTO REFRESH) and menu rows
- Render sections and items into the DOM

### Live auto-refresh

The page re-fetches the sheet on an interval (from the `AUTO REFRESH` row, default 60s):
- Only explicit `AUTO REFRESH OFF` disables polling
- On each poll: fetch the sheet, compare to previous CSV (re-render only if changed), and re-evaluate PDF-vs-menu mode
- Note: the published CSV has a ~5-minute CDN cache; the gviz endpoint is faster but updates less frequently

## Design

### Palette

| Token | Hex | Use |
|---|---|---|
| Background | `#f5f1ea` | Page background (linen/off-white) |
| Text | `#2c3540` | Body text, headings |
| Accent | `#6a8c7f` | Section headings, header border |
| Muted | `#7a8a95` | Allergen text |

### Typography

- **Playfair Display** (Google Fonts CDN) — restaurant name, section headings
- **Inter** (Google Fonts CDN) — menu item names, prices, allergens, UI text

### Header

Left-aligned. Logo SVG on the left, name + tagline stacked to the right.

- Restaurant name: Playfair Display, ~22px, slate
- Tagline: "Drove Orchards, Thornham, Norfolk" — Inter, small, uppercase, accent green
- Bottom border: 2px solid accent green

### Menu layout

- Max-width 640px, centred, horizontal padding 20px
- Section heading: Playfair Display, uppercase, accent green, no border
- Each item: name left / price right on one line, allergens on a second line in muted grey
- Thin divider line between items (very low opacity)
- No animations

### Mobile-first

Primary viewport is a phone held vertically. Padding and font sizes chosen for comfortable reading at arm's length.

## Error States

- **CSV fetch fails**: show a brief message ("Menu unavailable — please ask a member of staff") in place of the menu
- **PDF embed**: no special handling needed; the Drive preview handles its own errors

## Railway Deployment

`railway.toml` configures a static file server using `npx serve`:

```toml
[deploy]
startCommand = "npx serve -s . -l $PORT"
```

Railway's nixpacks environment includes Node.js, so `npx serve` is available without a `package.json`. No server-side code needed.

## Out of Scope

- No CMS, admin panel, or edit UI
- No analytics
- No SEO meta beyond a basic `<title>` and `<meta name="viewport">`
- No service worker / offline support
- No footer
