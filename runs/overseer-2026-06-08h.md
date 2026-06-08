# Overseer Run Report — 2026-06-08h (Run I)

**Session:** ninth run this date (A+B in overseer-2026-06-08.md, C in …b.md, D in …c.md,
E in …d.md, F in …e.md, G in …f.md, H in …g.md, I this file)
**Rotation:** DOY 159 mod 6 = 3 → marina (BLOCKED) → apex (soft-blocked, branch queue
further diverged after owner’s risk-hardening merge) → **shipsafe** (index 5, P2.1a buildable)
**Branch pushed:** `overseer/2026-06-08-p2a` on shipsafe · tip `1a4c93a` (2 commits)

---

## Step 2 — Reconcile (since Run H)

| Repo | origin/main HEAD | Change since Run H |
|------|-----------------|--------------------|
| boots | `74e4410` | Unchanged |
| Websites | `826c8f3` | Unchanged |
| Duke | `c639ca4` | **NEW** — owner fixed CRM outreach demo-link substitution (`{demoUrl}`/`{claimByDate}` now substituted server-side; guard blocks sending demo template with no demo built). June 8 05:31 UTC |
| marina | `6ac5ed2` | Unchanged |
| apex-quant | `c699015` | **NEW** — owner merged `feat/risk-hardening`: 5 fail-closed guardrails (stale-data, concentration caps, hard notional limits, ATR stop validation, consecutive-rejection circuit breaker). 3129 tests, 94.51% cov. June 8 05:39 UTC |
| shipsafe | `a86dff79` | Unchanged since Run G — owner’s P1 sweep. New: `overseer/2026-06-08-p2a` pushed this run |

Old overseer branches for shipsafe (`p1.3`, `p1.5`, `2026-06-08`) are superseded by the owner’s
P1 sweep — owner should close them. Apex overseer branches (`2026-06-07`, `2026-06-08`) still
unmerged, now ~40+ commits behind main after risk-hardening merge; rebase needed.

---

## Step 3 — Rotation rationale

- DOY 159 mod 6 = 3 → **marina**: BLOCKED (both buildable items await `phase-3-golive` merge).
- Index 4 → **apex**: soft-blocked. Owner’s risk-hardening merge made main diverge further from
  the two unmerged overseer branches. Pushing more apex work until the queue clears is wasteful.
- Index 5 → **shipsafe**: owner’s P1 sweep (P1.2–P1.8) left specific gaps:
  - `--muted-2:#8a8276` = 3.58:1 contrast (WCAG AA needs 4.5:1) — a tool auditing a11y failing
    its own a11y is a credibility problem.
  - No “descriptive link text” check (WCAG 2.4.4, listed in P2.1).
  - No focus management post-scan (keyboard/SR users blind to results appearing).
  - No `aria-expanded` sync on FAQ `<details>` (VoiceOver/Safari compatibility gap).

---

## Step 4 — Built: ShipSafe P2.1a

### Branch `overseer/2026-06-08-p2a` (from main `a86dff79`)

**Commit 1** `f5c7af3` — styles.css + all HTML version bumps

- `--muted-2` raised: `#8a8276` (3.58:1 – FAILS AA) → `#706860` (5.16:1 – PASSES AA).
  Affects: `.proto` (https:// prefix), `.trustline`, `.builtfor-label`, `.scanned`, `.fine`,
  `.madeby`. Hierarchy maintained: `--muted` (#665f54, 5.95:1) remains darker than `--muted-2`.
- `.nav a[aria-current="page"]` — underline style added. methodology.html + about.html already
  set the attribute; now it has a visible indicator. WCAG 2.4.12 current-page signal.
- HTML version bumps: methodology.html + about.html + 404.html were on `?v=6` while index.html
  was on `?v=7`. All four now unified at `?v=8`.

**Commit 2** `1a4c93a` — app.js + methodology.html

- **New check: “Descriptive link text”** (WCAG 2.4.4, `warn`). Checks `<a href>` elements
  (excludes `#`, `mailto:`, `tel:`, and links with `aria-label`/`aria-labelledby`) for a set of
  generic phrases: `click here`, `here`, `read more`, `more`, `learn more`, `continue`, `details`,
  `click`, `link`, `more info`. Returns `warn` (not `fail`) because static HTML context can’t
  always confirm surrounding content makes the purpose clear. `WCAG_URL["2.4.4"]` was already
  mapped — law links auto-generated. Accessibility check count: **8 → 9**.
- **Focus management**: `render()` moves keyboard focus to the report `<h2>` via
  `requestAnimationFrame` + `tabIndex=-1` + `focus({preventScroll:true})`. Error catch moves
  focus to `.notice` div. Screen-reader users now hear the result without hunting for it.
- **`aria-expanded` on FAQ `<details>`**: initialises from `d.open`; `toggle` listener keeps
  in sync. VoiceOver + Safari sometimes don’t fully expose native `<details>` AOM state.
- **methodology.html**: “These eight checks” → “These nine checks”; new `<tr>` documenting
  WCAG 2.4.4, Level AA, heuristic rationale, and why it’s a warn.

### Verification

Static content audit (all confirmed in pushed files):
- `--muted-2:#706860` in styles.css ✅
- `Descriptive link text` as 9th item in `CHECKS.accessibility` ✅
- `requestAnimationFrame` + `h2.focus` in `render()` ✅
- `aria-expanded` + `toggle` listener at end of app.js ✅
- `styles.css?v=8` in all 4 HTML files ✅
- `nine checks` + new `<tr>` in methodology.html ✅

Screenshot: NOT possible (no Chrome in cloud). Owner QA steps:
1. `?demo=1` — verify secondary text (https:// prefix, trustline, footer) is readable and
   slightly darker; not too dark.
2. Tab through methodology or about nav — active link should show underline.
3. Submit a scan — verify focus moves to “Health X/100” heading.
4. Submit unfetchable URL — verify focus moves to error notice.
5. Toggle FAQ items — verify `aria-expanded` attribute matches open/closed state.
6. Check methodology page shows “nine checks” and the new Descriptive link text row.

---

## Updated Owner-Action Items

**NEW:**
- **shipsafe `overseer/2026-06-08-p2a`** (1a4c93a): contrast + nav style + P2.1a check + focus
  management + aria-expanded. Screenshot-verify, then merge to main (GitHub Pages, free).

**CLOSE (superseded by owner’s P1 sweep):**
- `overseer/2026-06-08-p1.3` (62ab2ad): severity hierarchy + letter grade already in main. Close.
- `overseer/2026-06-08-p1.5` (e4bacc2): owner did their own P1.5 (sans --muted-2 fix, which is
  now in p2a). Close.
- `overseer/2026-06-08` (b8b2535): teal/Jakarta design rejected, 6 behind main. Close.

**Pre-existing (unchanged):** apex overseer branches, marina merge decision, boots secrets, duke
env vars — see OVERSEER.md §4.

---

## Next Buildable

| Project | Next item | Blocked? |
|---------|-----------|----------|
| boots | P1.7 loading/empty/error state guards | No |
| Websites | Fresh CSV batch / more section variants | No |
| Duke | Inline estimate widget, comparison posts | No |
| marina | wizard→storefront / slot-picker | YES — phase-3-golive merge |
| apex-quant | Hold until owner merges overseer branches | Soft-blocked |
| shipsafe | P2.1b: ARIA role validity check | No |
