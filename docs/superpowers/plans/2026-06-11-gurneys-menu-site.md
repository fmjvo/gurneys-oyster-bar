# Gurney's Oyster Bar — Menu Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a single-page static menu site for Gurney's Oyster Bar that renders from a Google Sheet CSV or a Google Drive PDF embed, depending on a CONFIG flag.

**Architecture:** One `index.html` file contains all HTML, CSS, and JS. A `CONFIG` object at the top of the script block controls rendering mode. Sheet mode fetches and parses a published Google Sheet CSV; PDF mode embeds a Google Drive file via iframe.

**Tech Stack:** Plain HTML5, CSS custom properties, vanilla JS (ES2020), Google Fonts CDN (Playfair Display + Inter), Railway static deploy via `npx serve`.

---

## File Map

| File | What it is |
|---|---|
| `index.html` | Entire site — structure, styles, logic |
| `placeholder-logo.svg` | Oyster shell outline SVG in slate |
| `railway.toml` | Railway static serve config |
| `README.md` | Owner setup instructions |
| `.gitignore` | Ignores `.superpowers/` and OS files |

---

## Task 1: Scaffold + gitignore

**Files:**
- Create: `index.html`
- Create: `.gitignore`

- [ ] **Step 1: Create `.gitignore`**

```
.superpowers/
.DS_Store
Thumbs.db
```

- [ ] **Step 2: Create the `index.html` scaffold**

This is the full HTML skeleton — styles and script stubs filled in later tasks. Write the exact file below:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gurney's Oyster Bar</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;600&family=Inter:wght@300;400&display=swap" rel="stylesheet">
  <style>
    /* styles added in Task 2 */
  </style>
</head>
<body>
  <header>
    <img src="placeholder-logo.svg" alt="Gurney's Oyster Bar">
    <div class="header-text">
      <h1>Gurney's Oyster Bar</h1>
      <p class="tagline">Drove Orchards, Thornham, Norfolk</p>
    </div>
  </header>
  <main id="main">
    <div class="status">Loading…</div>
  </main>
  <script>
    const CONFIG = {
      mode: 'sheet',  // 'sheet' or 'pdf'
      sheetUrl: '',   // published Google Sheet CSV URL
      pdfPath: '',    // Google Drive file ID
    };

    // logic added in Tasks 4–6
  </script>
</body>
</html>
```

- [ ] **Step 3: Open `index.html` in a browser**

Verify it loads without console errors (fonts 404s are expected until styles are in place — ignore those).

- [ ] **Step 4: Commit**

```bash
git add index.html .gitignore
git commit -m "feat: scaffold index.html and gitignore"
```

---

## Task 2: Header + base styles

**Files:**
- Modify: `index.html` — replace `/* styles added in Task 2 */` with the full CSS block below

- [ ] **Step 1: Replace the empty style block with this CSS**

```css
:root {
  --bg: #f5f1ea;
  --text: #2c3540;
  --accent: #6a8c7f;
  --muted: #7a8a95;
}

*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

body {
  background: var(--bg);
  color: var(--text);
  font-family: Inter, sans-serif;
  font-weight: 400;
  -webkit-font-smoothing: antialiased;
}

header {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 20px;
  border-bottom: 2px solid var(--accent);
  max-width: 640px;
  margin: 0 auto;
}

header img {
  width: 42px;
  height: 42px;
  flex-shrink: 0;
}

.header-text h1 {
  font-family: 'Playfair Display', serif;
  font-size: 22px;
  font-weight: 600;
  letter-spacing: 0.02em;
}

.header-text .tagline {
  font-size: 10px;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--accent);
  margin-top: 4px;
}

#main {
  max-width: 640px;
  margin: 0 auto;
  padding: 0 20px;
}

.menu-section {
  padding: 20px 0 8px;
}

.section-heading {
  font-family: 'Playfair Display', serif;
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--accent);
  margin-bottom: 10px;
}

.menu-item {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  padding: 8px 0;
  border-bottom: 1px solid rgba(44, 53, 64, 0.07);
}

.item-left { flex: 1; }

.item-name {
  font-size: 14px;
  line-height: 1.4;
}

.item-allergens {
  font-size: 11px;
  color: var(--muted);
  margin-top: 2px;
  letter-spacing: 0.02em;
}

.item-price {
  font-size: 14px;
  font-weight: 300;
  margin-left: 16px;
  white-space: nowrap;
}

.pdf-frame {
  width: 100%;
  height: calc(100vh - 90px);
  border: none;
  display: block;
}

.status {
  padding: 48px 0;
  text-align: center;
  font-size: 14px;
  color: var(--muted);
}
```

- [ ] **Step 2: Open `index.html` in a browser**

Check:
- Linen/off-white background
- Left-aligned header: logo placeholder (broken image icon) + "Gurney's Oyster Bar" in a serif + tagline in small green caps
- 2px green bottom border on the header
- "Loading…" status text below in muted grey

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add base styles and header layout"
```

---

## Task 3: Placeholder logo SVG

**Files:**
- Create: `placeholder-logo.svg`

- [ ] **Step 1: Create `placeholder-logo.svg`**

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 44 44" fill="none">
  <path d="M22 5C12.6 5 5 12.6 5 22c0 4.4 1.6 8.4 4.3 11.5l.7.7L22 39l12-4.8.7-.7C37.4 30.4 39 26.4 39 22 39 12.6 31.4 5 22 5z"
        stroke="#2c3540" stroke-width="1.5" fill="none"/>
  <path d="M22 7c-2.2 4.5-3.3 8.8-3.3 15s1.1 10.5 3.3 14"
        stroke="#2c3540" stroke-width="1" stroke-linecap="round" opacity="0.45"/>
  <path d="M17.5 8.5c-1.2 3.5-1.8 7-1.8 13.5s.6 10 2 13"
        stroke="#2c3540" stroke-width="1" stroke-linecap="round" opacity="0.3"/>
  <path d="M26.5 8.5c1.2 3.5 1.8 7 1.8 13.5s-.6 10-2 13"
        stroke="#2c3540" stroke-width="1" stroke-linecap="round" opacity="0.3"/>
</svg>
```

- [ ] **Step 2: Reload `index.html` in browser**

The header should now show the oyster shell SVG on the left — a simple slate outline of a shell with three curved ribs.

- [ ] **Step 3: Commit**

```bash
git add placeholder-logo.svg
git commit -m "feat: add oyster shell placeholder logo SVG"
```

---

## Task 4: RFC 4180 CSV parser

The Google Sheet CSV will quote any field containing a comma (e.g. allergens: `"molluscs, dairy"`). A plain `split(',')` breaks on that. This task writes a minimal parser that handles quoted fields, tests it with Node, then integrates it into `index.html`.

**Files:**
- Create (temporary): `csv-test.js` — deleted after verification
- Modify: `index.html` — add `parseCSV` and `escapeHtml` functions inside `<script>`

- [ ] **Step 1: Create `csv-test.js`**

```js
function parseCSV(text) {
  const rows = [];
  const lines = text.replace(/\r\n/g, '\n').replace(/\r/g, '\n').trim().split('\n');
  for (const line of lines) {
    if (!line.trim()) continue;
    const fields = [];
    let i = 0;
    while (i < line.length) {
      if (line[i] === '"') {
        let field = '';
        i++;
        while (i < line.length) {
          if (line[i] === '"' && line[i + 1] === '"') { field += '"'; i += 2; }
          else if (line[i] === '"') { i++; break; }
          else field += line[i++];
        }
        fields.push(field);
        if (line[i] === ',') i++;
      } else {
        const end = line.indexOf(',', i);
        if (end === -1) { fields.push(line.slice(i)); break; }
        fields.push(line.slice(i, end));
        i = end + 1;
      }
    }
    rows.push(fields);
  }
  return rows;
}

// Tests
const csv = 'Section,Item Name,Price,Allergens\r\nOysters,Native Oysters \xd7 6,\xa318,"molluscs, dairy"\r\nWine,Muscadet,\xa37,sulphites\r\nBeer,Adnams Ghost Ship,\xa36,';
const rows = parseCSV(csv);

console.assert(rows.length === 4, `Expected 4 rows, got ${rows.length}`);
console.assert(rows[0][0] === 'Section', `Header col 0: got "${rows[0][0]}"`);
console.assert(rows[1][0] === 'Oysters', `Section: got "${rows[1][0]}"`);
console.assert(rows[1][1] === 'Native Oysters \xd7 6', `Item name: got "${rows[1][1]}"`);
console.assert(rows[1][3] === 'molluscs, dairy', `Quoted allergens: got "${rows[1][3]}"`);
console.assert(rows[2][3] === 'sulphites', `Unquoted allergens: got "${rows[2][3]}"`);
console.assert(rows[3][3] === '', `Empty allergens: got "${rows[3][3]}"`);

console.log('All CSV parser tests passed ✓');
```

- [ ] **Step 2: Run the test**

```bash
node csv-test.js
```

Expected output:
```
All CSV parser tests passed ✓
```

If any assertion fires, fix the `parseCSV` function in the test file until all pass, then copy the fixed version to `index.html` in the next step.

- [ ] **Step 3: Add `parseCSV` and `escapeHtml` to `index.html`**

Replace `// logic added in Tasks 4–6` inside the `<script>` block with:

```js
function escapeHtml(str) {
  return String(str)
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;');
}

function parseCSV(text) {
  const rows = [];
  const lines = text.replace(/\r\n/g, '\n').replace(/\r/g, '\n').trim().split('\n');
  for (const line of lines) {
    if (!line.trim()) continue;
    const fields = [];
    let i = 0;
    while (i < line.length) {
      if (line[i] === '"') {
        let field = '';
        i++;
        while (i < line.length) {
          if (line[i] === '"' && line[i + 1] === '"') { field += '"'; i += 2; }
          else if (line[i] === '"') { i++; break; }
          else field += line[i++];
        }
        fields.push(field);
        if (line[i] === ',') i++;
      } else {
        const end = line.indexOf(',', i);
        if (end === -1) { fields.push(line.slice(i)); break; }
        fields.push(line.slice(i, end));
        i = end + 1;
      }
    }
    rows.push(fields);
  }
  return rows;
}

// renderMenu, showPDF, init added in Tasks 5–6
```

- [ ] **Step 4: Delete `csv-test.js`**

```bash
rm csv-test.js
```

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add RFC 4180 CSV parser and escapeHtml"
```

---

## Task 5: Sheet mode — render menu

**Files:**
- Modify: `index.html` — add `renderMenu` and `init` functions

- [ ] **Step 1: Replace `// renderMenu, showPDF, init added in Tasks 5–6` with this**

```js
function renderMenu(rows) {
  const main = document.getElementById('main');
  const data = rows.slice(1).filter(r => r.length >= 3 && r[0].trim());
  if (!data.length) {
    main.innerHTML = '<div class="status">Menu unavailable — please ask a member of staff.</div>';
    return;
  }
  const sections = {};
  const order = [];
  for (const row of data) {
    const [section, name, price, allergens = ''] = row;
    if (!sections[section]) { sections[section] = []; order.push(section); }
    sections[section].push({ name, price, allergens });
  }
  main.innerHTML = order.map(section => `
    <div class="menu-section">
      <div class="section-heading">${escapeHtml(section)}</div>
      ${sections[section].map(item => `
        <div class="menu-item">
          <div class="item-left">
            <div class="item-name">${escapeHtml(item.name)}</div>
            ${item.allergens.trim() ? `<div class="item-allergens">${escapeHtml(item.allergens)}</div>` : ''}
          </div>
          <div class="item-price">${escapeHtml(item.price)}</div>
        </div>
      `).join('')}
    </div>
  `).join('');
}

function showPDF(fileId) {
  document.getElementById('main').innerHTML =
    `<iframe class="pdf-frame" src="https://drive.google.com/file/d/${escapeHtml(fileId)}/preview" title="Menu PDF"></iframe>`;
}

async function init() {
  const main = document.getElementById('main');
  if (CONFIG.mode === 'pdf' && CONFIG.pdfPath) {
    showPDF(CONFIG.pdfPath);
    return;
  }
  if (!CONFIG.sheetUrl) {
    main.innerHTML = '<div class="status">Menu unavailable — please ask a member of staff.</div>';
    return;
  }
  try {
    const res = await fetch(CONFIG.sheetUrl);
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const text = await res.text();
    renderMenu(parseCSV(text));
  } catch {
    main.innerHTML = '<div class="status">Menu unavailable — please ask a member of staff.</div>';
  }
}

init();
```

- [ ] **Step 2: Test sheet mode with a real published CSV URL**

To test this without a live sheet, temporarily set `CONFIG.sheetUrl` to this public test CSV (a Google Sheets export with the correct column schema):

```js
sheetUrl: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vSamplePublishedSheetId/pub?output=csv',
```

If you don't have a test sheet available, create one: make a Google Sheet with columns `Section | Item Name | Price | Allergens`, add a few rows, publish via **File → Share → Publish to web → Comma-separated values**, and paste the CSV URL into CONFIG.

Expected result: the menu renders with section headings in green and item rows with price on the right.

- [ ] **Step 3: Reset `CONFIG.sheetUrl` to empty string before committing**

```js
sheetUrl: '',
```

Verify the page shows "Menu unavailable — please ask a member of staff."

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add sheet mode CSV fetch and menu render"
```

---

## Task 6: PDF mode + error state verification

**Files:**
- Modify: `index.html` — manual test of PDF mode (no code changes needed if Task 5 was written correctly)

- [ ] **Step 1: Test PDF mode**

Temporarily set CONFIG to:

```js
const CONFIG = {
  mode: 'pdf',
  sheetUrl: '',
  pdfPath: '1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgVE2upms',  // public Google Sheet used as test (any valid Drive file ID works)
};
```

Reload the page. Expected:
- Header still visible
- Below the header: a full-height iframe loading the Google Drive preview
- No JS errors in console

- [ ] **Step 2: Test error state**

Set CONFIG to:

```js
const CONFIG = { mode: 'sheet', sheetUrl: 'https://example.com/not-a-csv', pdfPath: '' };
```

Reload. Expected: "Menu unavailable — please ask a member of staff." (the fetch will fail or return non-CSV content — either way the catch block fires).

- [ ] **Step 3: Reset CONFIG to defaults**

```js
const CONFIG = {
  mode: 'sheet',
  sheetUrl: '',
  pdfPath: '',
};
```

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: verify pdf mode and error state"
```

---

## Task 7: Railway deployment config

**Files:**
- Create: `railway.toml`

- [ ] **Step 1: Create `railway.toml`**

```toml
[deploy]
startCommand = "npx serve -s . -l $PORT"
```

This tells Railway to use `npx serve` (available via Railway's Node.js environment) to serve the directory as a static site on the port Railway assigns.

- [ ] **Step 2: Commit**

```bash
git add railway.toml
git commit -m "feat: add Railway static serve config"
```

---

## Task 8: README

**Files:**
- Create: `README.md`

- [ ] **Step 1: Create `README.md`**

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README with menu update instructions"
```

---

## Task 9: Final check + mobile verification

- [ ] **Step 1: Run locally with `npx serve`**

```bash
npx serve .
```

Visit `http://localhost:3000`.

- [ ] **Step 2: Open Chrome DevTools → Toggle device toolbar → iPhone 12 Pro (390px)**

Check:
- Header fits without wrapping (logo + name on one line)
- Section headings readable
- Item name left / price right on one line for typical item lengths
- Allergens text legible but subdued
- No horizontal scrollbar

- [ ] **Step 3: Check with a populated CONFIG**

Temporarily set a real `sheetUrl` (or the test CSV from Task 5) and verify the full rendered menu looks correct on mobile.

- [ ] **Step 4: Check `.gitignore` covers `.superpowers/`**

```bash
git status
```

The `.superpowers/` directory should not appear as untracked. If it does, verify `.gitignore` was committed in Task 1.

- [ ] **Step 5: Final commit (if any polish applied)**

```bash
git add -A
git commit -m "chore: final polish and mobile verification"
```
```
