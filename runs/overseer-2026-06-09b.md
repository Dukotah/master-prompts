# Overseer Run B — 2026-06-09 (2nd run this date)

**UTC timestamp:** 2026-06-09 (Run B)
**Target repo:** shipsafe
**Item built:** P2.1k — Viewport zoom restrictions (19th accessibility check, WCAG 1.4.4 Level AA)
**Branch pushed:** `overseer/2026-06-09` at `5a65686` (3 commits total: P2.1i + P2.1j + P2.1k)

---

## Step 2 — Reconcile

All repos compared to Run A state (2026-06-09 earlier today):

| Repo | origin/main HEAD | Change since Run A |
|---|---|---|
| **boots** | `4ae73a3` (June 8 21:11 UTC) | Unchanged |
| **Websites** | `826c8f3` (June 7 23:37 UTC) | Unchanged |
| **Duke** | `7c3a041` (June 8 21:21 UTC) | Unchanged |
| **Marina** | `6ac5ed2` (June 5 03:18 UTC) | Unchanged |
| **Apex** | `f752197` (June 8 21:28 UTC) | Unchanged |
| **ShipSafe** | `a86dff7` (June 8 05:31 UTC) | Unchanged on main; `overseer/2026-06-09` advanced to `5a65686` this run (P2.1k) |

**ShipSafe branch queue (pending owner merge — in order):**
- `overseer/2026-06-08-p2a` (1a4c93a) — 2 ahead of main; base of merge chain
- `overseer/2026-06-08-p2b` (a48ea9b)
- `overseer/2026-06-08-p2c` (43b69f2)
- `overseer/2026-06-08-p2d` (a563c41)
- `overseer/2026-06-08-p2e` (9a36eb1)
- `overseer/2026-06-08-p2f` (1448a1c)
- `overseer/2026-06-08-p2g` (357e7fe)
- `overseer/2026-06-08-p2h` (93df640)
- `overseer/2026-06-09` (5a65686) — 12 ahead of main (P2.1i + P2.1j + P2.1k)

---

## Step 3 — Rotation

UTC day-of-year 2026-06-09 = 160. 160 mod 6 = 4 → index 4 = apex-quant.
Apex is BLOCKED (branch queue too long, needs owner rebase). Skip to index 5 = **shipsafe**.

Run A already built P2.1j. This run advances to the next item: **P2.1k**.

---

## Step 4 — What was built

**P2.1k — Viewport zoom restrictions** (WCAG 1.4.4 Resize Text, Level AA, `warn`)

### The check

WCAG 1.4.4 requires that text be resizable up to 200% without loss of content or functionality.
The most common source-detectable way a page blocks this is the `<meta name="viewport">` tag:
- `user-scalable=no` — disables pinch-to-zoom on mobile
- `maximum-scale=1` (or ≤1) — caps zoom at 100%, blocking any enlargement

Both are direct WCAG 1.4.4 failures. iOS Safari has ignored `user-scalable=no` since iOS 10,
which is why the check returns `warn` rather than `fail` — the restriction may not bite on modern
iPhones. Android and many older WebViews still honour it. WCAG makes no platform exception.

### Check logic (app.js lines 325–344)

1. Find `meta[name="viewport"]`
2. If none → `info` ("No viewport meta tag found — zoom restrictions cannot be assessed.")
   — The companion "Mobile viewport" check already handles this case.
3. Parse `content` attribute: split by comma, trim each directive
4. Detect `user-scalable=no` (regex, case-insensitive)
5. Detect `maximum-scale=<N>` where N ≤ 1 (parseFloat comparison)
6. If neither → `pass` ("Viewport allows pinch-to-zoom…")
7. If either → `warn` listing the offending directive(s), with fix text and `WCAG 1.4.4` law tag

Added `"1.4.4": "resize-text"` to the `WCAG_URL` map so the law tag renders as a clickable
link to the WCAG 2.2 Understanding document.

### Demo update

The existing demo sample (`?demo=1`) had no viewport meta, causing "Mobile viewport" to show `warn`.
Added `<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">` to
the sample:
- **Mobile viewport**: warn → pass (viewport meta now present) ✓
- **Viewport zoom restrictions**: info → warn (user-scalable=no detected) ✓ demonstrates the new check

### ShipSafe self-scan (no self-fail)

All three ShipSafe pages use `content="width=device-width, initial-scale=1"`:
- `index.html`: pass ✓
- `methodology.html`: pass ✓
- `about.html`: pass ✓

### Files changed

| File | Change |
|---|---|
| `app.js` | Added `"1.4.4": "resize-text"` to WCAG_URL map; added 19th a11y check (lines 325–344); added viewport meta to demo sample; version stays unmarked in JS (cache-bust is in index.html) |
| `index.html` | `app.js?v=17` → `app.js?v=18` |
| `methodology.html` | "eighteen" → "nineteen" in section intro; new table row for Viewport zoom restrictions; new limitations bullet |

---

## Verification

**Static analysis only** — headless Chrome not available in cloud environment.

Verified by code inspection:
- ✓ 19 accessibility checks confirmed by enumerating `CHECKS.accessibility` array
- ✓ `WCAG_URL["1.4.4"] = "resize-text"` → link resolves to correct WCAG 2.2 Understanding page
- ✓ Check logic handles all four branches: `info` (no meta), `pass` (no restriction), `warn` (user-scalable=no), `warn` (maximum-scale≤1)
- ✓ Regex `user-scalable\s*=\s*no` matches common variants (with/without spaces)
- ✓ `parseFloat` correctly extracts `1`, `1.0`, `1.5`, etc. from `maximum-scale=N`
- ✓ ShipSafe own viewport `width=device-width, initial-scale=1` → neither restriction → `pass`
- ✓ methodology.html table row added after "Frames have accessible names" row
- ✓ Limitations section updated with honest caveat (CSS/JS zoom blocking, iOS Safari behaviour)

⚠️ Cannot screenshot: headless Chrome not available. Owner must verify visually:
- `?demo=1` → Viewport zoom restrictions shows `warn` (user-scalable=no)
- `?demo=1` → Mobile viewport now shows `pass` (viewport meta present)
- Scan a URL with `user-scalable=no` in viewport → expect warn
- Scan a URL with `maximum-scale=1` in viewport → expect warn
- Scan a URL with `content="width=device-width, initial-scale=1"` → expect pass

---

## Step 5 — Owner action updates

No new blockers discovered. Existing blockers unchanged.

Updated `overseer/2026-06-09` merge note in §4 to document the 3rd commit (P2.1k).
Updated ShipSafe "Next buildable" to suggest P2.1l (color contrast heuristic) or
P2.1m (touch target size).

---

## Status at end of run B

- ShipSafe accessibility suite: **19 checks** (P2.1k on `overseer/2026-06-09` at `5a65686`)
- Pending merge chain: p2a → p2b → p2c → p2d → p2e → p2f → p2g → p2h → overseer/2026-06-09 (12 commits)
- All six active projects:
  - boots ✅ unchanged (main at 4ae73a3)
  - Websites ✅ unchanged (main at 826c8f3)
  - Duke ✅ unchanged (main at 7c3a041)
  - Marina 🔲 blocked on owner merge (main at 6ac5ed2; phase-3-golive 24 ahead)
  - Apex 🔲 blocked on owner rebase (main at f752197; paper gate running ~13% through 30 days)
  - ShipSafe ✅ 19-check a11y suite; `overseer/2026-06-09` at `5a65686`
