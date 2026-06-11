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
  mode: 'sheet',       // 'sheet' or 'pdf'
  sheetUrl: '',        // published Google Sheet CSV URL
  pdfPath: '',         // Google Drive file ID (for pdf mode)
}
```

## Two Rendering Modes

### Sheet mode (default)

- `fetch()` the published Google Sheet CSV URL
- Parse CSV client-side (no library — implement a minimal RFC 4180 parser that handles quoted fields, since the Allergens column contains commas and will be quoted by Google Sheets)
- Show a loading state while fetching; show a user-friendly error if fetch fails
- Render sections and items into the DOM

**CSV schema** (Google Sheet column order):

| Column | Example |
|---|---|
| Section | Oysters |
| Item Name | Native Oysters × 6 |
| Price | £18 |
| Allergens | molluscs, dairy |

### PDF mode

- Embed via `<iframe src="https://drive.google.com/file/d/FILE_ID/preview">`
- FILE_ID comes from `CONFIG.pdfPath`
- Header remains visible above the embed
- Embed fills remaining viewport height
- To update the menu: upload a new version of the same Drive file — no code change needed

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
