# Gurney's Oyster Bar — Menu Site

Static menu website for Gurney's Oyster Bar. Deployed on Railway. Viewed on mobile via QR code.

## Google Sheet structure

The sheet has two settings rows at the top, a blank spacer row, then the menu rows:

| Col A | Col B | Col C |
|---|---|---|
| `PDF OVERRIDE` | `ON` / `OFF` | Full Google Drive share URL of the PDF |
| `AUTO REFRESH` | `ON` / `OFF` | Refresh interval in seconds (blank = 60) |
| *(leave this row blank)* | | |
| Section | Subsection | Item Name | Price | Allergens |
| Oysters | | Native Oysters × 6 | £18 | molluscs |
| Wine | White | Muscadet, Glass | £7 | sulphites |
| Wine | White | Chablis, Glass | `175ml £7 \| 250ml £9 \| Bottle £28` | sulphites |

> The header row with column names (Section, Subsection, …) is row 4. Rows 1–3 are always the two settings rows and the blank spacer.

### Menu columns

- **Section** — groups items under a heading (e.g. Oysters, Wine, Beer, Puddings)
- **Subsection** — optional middle grouping within a section (e.g. White / Red / Rosé under Wine). Leave blank and the item sits directly under the Section heading.
- **Item Name** — displayed as-is
- **Price** — one price or multiple variants separated by `|`, e.g. `175ml £7 | 250ml £9 | Bottle £28` or `Single £3.50 | Dozen £18`. A plain `£8` still works fine.
- **Allergens** — comma-separated list, shown in small text below the item name. Leave blank if none.

---

## Day-to-day tasks

### Adding or editing menu items

Edit the sheet directly. Changes appear on the live site within a few minutes (see Auto Refresh below).

### Switching to a PDF menu (e.g. seasonal special)

Use the **PDF OVERRIDE** row (row 1 of the sheet):

1. Export your menu as a PDF (e.g. from Canva).
2. Upload it to Google Drive. Set sharing to **Anyone with the link → Viewer**.
3. Copy the full share URL from Drive (it looks like `https://drive.google.com/file/d/…/view`).
4. In the sheet, set **column B of row 1** to `ON` and paste the full URL into **column C of row 1**.
5. The site immediately shows the PDF instead of the live menu.

To go back to the live menu, set column B of row 1 back to `OFF`.

**To update a PDF without changing the URL:** right-click the file in Drive → **Manage versions → Upload new version**. The site updates automatically.

**Adding an ON/OFF dropdown to column B (one-time setup):**

1. Click cell B1.
2. Go to **Data → Data validation → Add rule**.
3. Under Criteria choose **Dropdown** and enter the two options: `ON` and `OFF`.
4. Save. Repeat for B2 (the AUTO REFRESH row).

---

### Auto Refresh

The **AUTO REFRESH** row (row 2 of the sheet) controls whether the site automatically re-fetches the menu in the background:

- **ON** — the site polls for updates on the interval set in column C (in seconds). Leave column C blank to use the default of 60 seconds. This means edits you make to the sheet appear on customers' phones without them needing to reload the page.
- **OFF** — no polling; customers see the version loaded when they opened the page.

**Tip on update speed:** Google's published CSV URL (`…/pub?output=csv`) is cached by Google for roughly 5 minutes. If you use the alternative `gviz` endpoint (`…/gviz/tq?tqx=out:csv&sheet=Sheet1`) as the `sheetUrl` in the code, updates typically appear within seconds rather than minutes.

---

## First-time setup (technical)

### Publishing the sheet

1. Create a Google Sheet following the structure above.
2. Publish it: **File → Share → Publish to web → Sheet 1 → Comma-separated values (.csv) → Publish**.
3. Copy the URL.

### Updating the code

Paste the URL into `index.html`. The only setting you need to change is `sheetUrl`:

```js
const CONFIG = {
  sheetUrl: 'PASTE_URL_HERE',
};
```

> Note: the old `mode` and `pdfPath` fields have been removed. PDF mode is now controlled entirely from the sheet via the PDF OVERRIDE row.

Commit and push — Railway redeploys automatically.

---

## Local preview

Open `index.html` directly in a browser. Note: the Google Sheet fetch will fail due to CORS in some browsers when opened as a `file://` URL — use a local server instead:

```bash
npx serve .
```

Then visit `http://localhost:3000`.

## Deployment

Connected to Railway. Push to `main` to deploy.
