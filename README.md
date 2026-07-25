# Admin Converter Tool

Converts the **Cash is Clear Maximizer** master spreadsheet (Excel/CSV) into the
`data.json` file that the [Maximizer app](https://cash-is-clear.github.io/maximizer/)
reads. Runs entirely in the browser — nothing is uploaded to a server.

**Live tool:** https://cash-is-clear.github.io/admin_converter/

> ⚠️ Use the site **root** (`index.html`). Do **not** use `/admin.html` — it is an
> old page with a hardcoded header row that silently drops the first 4 strategies
> (parses 240 instead of 244). The root page auto-detects the header and is the
> correct one.

## How to use

1. Open https://cash-is-clear.github.io/admin_converter/ (hard-refresh with
   **Cmd/Ctrl+Shift+R** if you recently changed the tool).
2. Drag the `.xlsx` (or `.csv`) onto the drop zone, or click **Browse Files**.
3. Check the status line and preview:
   - It should say **"Parsed N strategies … All columns matched."**
   - The **Column Detection** checklist shows every expected column as found (✓).
   - The **Sample Data** table shows the first few rows — confirm they look right.
4. Click **Download data.json**.
5. Push that file to the Maximizer — see
   [the Maximizer README](https://github.com/Cash-Is-Clear/maximizer#updating-the-data)
   (edit the Gist; no code change needed).

## What it produces

```json
{
  "columns": ["#", "Strategy", "Key Department", ...],
  "strategies": [
    { "#": 1, "Strategy": "Establish and use business credit", ... },
    ...
  ]
}
```

- `columns` preserves the spreadsheet's **left-to-right column order**.
- `strategies` preserves the spreadsheet's **top-to-bottom row order** — to change
  the order shown in the app, re-sort the spreadsheet and re-convert.

## How it reads the spreadsheet

- **Header auto-detection:** scans the first 15 rows and picks the row that best
  matches the expected column names (case- and spacing-insensitive). No fixed row
  number is assumed.
- **Expected columns** are defined in [`app.js`](app.js) (`EXPECTED_COLUMNS`).
  `Strategy`, `Key Department`, `Difficulty`, `Cash Position`, and `Risk` are
  **required**; the rest are optional (missing ones just leave that filter empty).
- **Extra columns** not in the expected list are passed through unchanged.
- **Blank rows** (fewer than 2 non-empty cells) are skipped and reported.

## Files

| File | Purpose |
|------|---------|
| `index.html` | The converter page (loads `app.js`). **This is the one to use.** |
| `app.js` | Parsing logic: header detection, column mapping, JSON output. |
| `styles.css` | Styling for `index.html`. |
| `admin.html` | ⚠️ Deprecated standalone page with buggy inline script. Do not use. |

## Deployment

Pushing to `main` triggers `.github/workflows/static.yml`, which deploys the whole
repo to GitHub Pages. No build step.
