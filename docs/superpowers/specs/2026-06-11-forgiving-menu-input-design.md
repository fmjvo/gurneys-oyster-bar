# Forgiving spreadsheet → menu — design

**Date:** 2026-06-11
**File touched:** `index.html` (parser + renderer, no markup/CSS changes)

## Problem

The spreadsheet schema (`Section | Subsection | Name | Price | Allergens`) is rigid and
silent. Rows disappear with no feedback when a cell is blank or used differently than the
code expects:

- Rows with a blank **Name** are dropped entirely (the `XL` / `Tiny` case: variant typed in
  the Subsection column, Name left blank → both rows vanish).
- Rows with a blank **Price** are dropped.
- Price variants require a `|` pipe character (`Half Dozen £18 | Dozen £34`), which is
  awkward to type and undiscoverable.
- Config rows (`PDF OVERRIDE`, `AUTO REFRESH`) can, under some sheet layouts, appear as
  menu lines.

## Goals

One row = one menu line. Best-effort rendering: **nothing silently vanishes.** Natural input.

## Design

### 1. Forgiving row handling

A menu row renders if it has a usable label — i.e. **Name OR Subsection is non-empty.**
Price is optional.

- **Display label** = `Name` if present, otherwise `Subsection`.
- **Subsection becomes a heading only when there is a separate Name under it.** If Name is
  blank, the Subsection text *is* the line label and no subsection heading is emitted.
- **Blank Price** → line renders with no price.
- **Fully blank / label-less rows** → skipped (intentional spacers/accidents).

| Sheet row | Renders as |
|---|---|
| `Penises \| XL \| (blank) \| £14.80` | line "XL — £14.80", no subsection heading |
| `Oysters \| Native \| Half Dozen \| £18` | subsection "Native" → line "Half Dozen — £18" |
| `Drinks \| Beer \| Adnams Ghost Ship \| (blank)` | subsection "Beer" → line "Adnams Ghost Ship", no price |

### 2. Config can never leak into the menu

- Menu rows are taken strictly from **after the `Section` header**, matched trimmed +
  case-insensitive (a stray trailing space no longer breaks detection).
- Belt-and-braces: any row whose first cell (trimmed, uppercased) is a known config key
  (`PDF OVERRIDE`, `AUTO REFRESH`) is skipped even if it lands below the header.

### 3. Comma as variant separator

- The **Price** cell is split on **both `,` and `|`** into variants (commas are the
  natural/primary input; pipes kept for backward compatibility).
- Each variant part is parsed as optional `label price` (existing logic): the last
  whitespace-token is the price if it starts with `£` or a digit; anything before it is the
  label.
- `Glass £7, Bottle £28` → two variants. `£14.80` → one plain price.
- **Rendering is unchanged** — multiple variants use the existing stacked, right-aligned
  `.price-list`; a single variant uses the plain `.item-price`. (Google Sheets auto-quotes
  cells containing commas, so the published CSV stays well-formed.)

### 4. Explicitly NOT changing

- **No dedup.** Duplicate rows render twice — faithful rendering beats guessing intent.
- No markup or CSS changes; layout is identical to today.

## Test cases (the user's own sheet)

```
Oysters  | Native Oysters | Half Dozen        | £18    | molluscs
Oysters  | Rock Oyster    | Half Dozen        | £16    | molluscs
Oysters  | Rock Oyster    | Half Dozen        | £16    | molluscs   (renders twice — intended)
Wine     | White          | Muscadet, Glass   | £7     | sulphites
Drinks   | Beer           | Adnams Ghost Ship | £6     |
Penises  | XL             | (blank)           | £14.80 |            (was dropped → now "XL £14.80")
Penises  | Tiny           | (blank)           | £16.40 |            (was dropped → now "Tiny £16.40")
```

Expected after change: all 7 rows render; no config rows appear; Penises lines have no
subsection heading.
