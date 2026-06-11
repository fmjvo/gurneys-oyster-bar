# Gurney's Oyster Bar — Menu Site

Static menu website for Gurney's Oyster Bar. Deployed on Railway. Viewed on mobile via QR code.

## Updating the menu

### Option A — Google Sheet (default)

1. Create a Google Sheet with these exact column headers in row 1:

   | Section | Item Name | Price | Allergens |
   |---|---|---|---|
   | Oysters | Native Oysters × 6 | £18 | molluscs |
   | Wine | Muscadet, Glass | £7 | sulphites |

   - **Section** — groups items under a heading (e.g. Oysters, Wine, Beer, Puddings)
   - **Item Name** — displayed as-is
   - **Price** — displayed as-is (include £ symbol)
   - **Allergens** — comma-separated list, shown in small text below the item name. Leave blank if none.

2. Publish the sheet: **File → Share → Publish to web → Sheet 1 → Comma-separated values (.csv) → Publish**

3. Copy the URL and paste it into `index.html`:

   ```js
   const CONFIG = {
     mode: 'sheet',
     sheetUrl: 'PASTE_URL_HERE',
     pdfPath: '',
   };
   ```

4. Commit and push — Railway redeploys automatically.

### Option B — PDF (Canva export)

1. Export your Canva menu as a PDF.
2. Upload it to Google Drive. Set sharing to **Anyone with the link → Viewer**.
3. Copy the file ID from the Drive URL:
   `https://drive.google.com/file/d/FILE_ID_IS_HERE/view`
4. Update `index.html`:

   ```js
   const CONFIG = {
     mode: 'pdf',
     sheetUrl: '',
     pdfPath: 'FILE_ID_IS_HERE',
   };
   ```

5. To update the PDF later: right-click the file in Drive → **Manage versions → Upload new version**. The site updates automatically — no code change needed.

6. Commit and push.

## Local preview

Open `index.html` directly in a browser. Note: the Google Sheet fetch will fail due to CORS in some browsers when opened as a `file://` URL — use a local server instead:

```bash
npx serve .
```

Then visit `http://localhost:3000`.

## Deployment

Connected to Railway. Push to `main` to deploy.
