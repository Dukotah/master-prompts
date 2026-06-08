# Overseer Run Report — 2026-06-08j (Run K — 11th run this date)

**Session:** eleventh run this date
**Previous runs:** A-B in overseer-2026-06-08.md, C in …b.md, D in …c.md, E in …d.md,
F in …e.md, G in …f.md, H in …g.md, I in …h.md, J in …i.md, K this file
**Rotation:** DOY 159 mod 6 = 3 → marina (BLOCKED) → apex (soft-blocked) → **shipsafe** (index 5)
**Branch pushed:** `overseer/2026-06-08-p2c` on shipsafe · tip `43b69f2`

---

## Step 2 — Reconcile (all repos, vs. Run J / overseer-2026-06-08i.md)

| Repo | origin/main HEAD | Change since Run J |
|------|-----------------|-----------------|
| boots | `74e44106` | Unchanged |
| Websites | `826c8f3b` | Unchanged |
| Duke | `c639ca4b` | Unchanged |
| marina | `6ac5ed2` | Unchanged |
| apex-quant | `c699015a` | Unchanged |
| shipsafe | `a86dff79` | Unchanged (main); `overseer/2026-06-08-p2a` `1a4c93a` (1 ahead, pending); `overseer/2026-06-08-p2b` `a48ea9b` (1 ahead of p2a, pending); **NEW `overseer/2026-06-08-p2c` `43b69f2` pushed this run** (1 ahead of p2b / 3 ahead of main) |

**Branch inventory (shipsafe):**
- `main` `a86dff79` — owner's P1 sweep base
- `overseer/2026-06-08-p2a` `1a4c93a` — 1 ahead of main (contrast + P2.1a, pending merge)
- `overseer/2026-06-08-p2b` `a48ea9b` — 1 ahead of p2a / 2 ahead of main (P2.1b ARIA roles)
- `overseer/2026-06-08-p2c` `43b69f2` — 1 ahead of p2b / 3 ahead of main (P2.1c tab-order, this run)
- `overseer/2026-06-08-p1.3`, `p1.5`, `overseer/2026-06-08` — superseded, safe to close

---

## Step 3 — Rotation rationale

Same pattern as Run J:

- DOY 159 mod 6 = 3 → **marina**: BLOCKED. Both buildable items (wizard→storefront,
  slot-picker) require `phase-3-golive` to merge first — owner action.
- Index 4 → **apex**: soft-blocked. Two unmerged overseer branches are 40+ commits behind
  main after `feat/risk-hardening` merge. Adding more work to a diverged queue wastes rebase
  effort. Holding per OVERSEER.md brief.
- Index 5 → **shipsafe**: P2.1c (tab-order heuristic) is unblocked, self-contained, and the
  natural continuation of P2.1b from Run J.

---

## Step 4 — Built: ShipSafe P2.1c

### Branch `overseer/2026-06-08-p2c` (from `overseer/2026-06-08-p2b` `a48ea9b`)

Branched from p2b to avoid conflicts in `app.js` and `methodology.html` (both modified in
p2a and p2b). Owner merges p2a → p2b → p2c in order; each is a clean 1-commit fast-forward.

**Commit** `43b69f2` — app.js + methodology.html + index.html

### What was built

**11th accessibility check: "Tab order preserved"** (WCAG 2.4.3 Focus Order — Level A)

Added to `CHECKS.accessibility` in `app.js`:

```javascript
["Tab order preserved", (c) => {
  const elements = [...c.doc.querySelectorAll("[tabindex]")];
  if (!elements.length) return { status: "info", detail: "No elements with tabindex attributes found." };
  const abusers = elements.filter(el => {
    const val = parseInt(el.getAttribute("tabindex"), 10);
    return !isNaN(val) && val > 0;
  });
  if (!abusers.length) return { status: "pass", detail: `${elements.length} tabindex attribute(s) found — none override the natural DOM order.` };
  return { status: "warn", detail: `${abusers.length} element(s) use a positive tabindex value, which overrides the natural DOM tab order and can create a disjointed keyboard navigation experience.`, fix: 'Replace tabindex="1", tabindex="2", etc. with tabindex="0" or remove the attribute entirely. ...', law: "WCAG 2.4.3" };
}],
```

**Design notes:**
- `tabindex="0"` = normal flow (fine; included in count but not flagged)
- `tabindex="-1"` = programmatically focusable, removed from tab order (fine; not flagged)
- `tabindex="1"` or higher = OVERRIDES natural order (warn) — forces focus to those elements
  before ALL tabindex=0 and naturally-focusable elements on the page
- Returns `warn` (not `fail`) because: (a) context matters — an unusual but intentional
  layout could use positive tabindex legitimately; (b) visual/DOM order divergence cannot be
  confirmed from static source HTML alone. WCAG 2.4.3 Language: "…if the navigation sequences
  affect meaning or operation…" — we can detect the attribute but not always the impact.
- Multi-value tabindex is not possible (it's a single integer attribute) — no multi-value logic needed.
- WCAG_URL map gains `"2.4.3": "focus-order"` for the linked law tag.

**ShipSafe self-check:**
- ShipSafe's own static HTML (`index.html`, `methodology.html`, `about.html`) has NO `tabindex`
  attributes in source. JavaScript sets `tabIndex = -1` dynamically for focus management, but
  the DOMParser runs on static source only — those don't appear. Result: `info` ("No elements
  with tabindex attributes found.") — correct, not a false warn.

**Other file changes:**
- `methodology.html`: "These ten checks" → "These eleven checks"; new `<tr>` with full
  explanation of WCAG 2.4.3, why positive tabindex breaks focus order, and why warn not fail.
- `index.html`: `app.js?v=9` → `app.js?v=10` (cache-bust for the new check).

---

## Step 5 — Verification

Cloud environment: no Chrome/Node available. Static audit of committed files:

| Check | Result |
|-------|--------|
| `Tab order preserved` check present in `CHECKS.accessibility` | ✅ Confirmed |
| `tabindex > 0` logic correct (parseInt, NaN guard, val > 0) | ✅ Confirmed |
| `tabindex=0` and `tabindex=-1` correctly excluded from warn | ✅ Logic confirmed |
| `WCAG_URL["2.4.3"]` = `"focus-order"` added for law tag link | ✅ Confirmed |
| `methodology.html` says "eleven checks" | ✅ Confirmed |
| New `<tr>` for Tab order in accessibility table | ✅ Confirmed |
| `index.html` has `app.js?v=10` | ✅ Confirmed |
| ShipSafe's own static HTML: no `tabindex` attrs → returns `info` | ✅ Manual grep confirmed |

Screenshot: NOT possible (no Chrome in cloud). Owner QA steps:
1. `?demo=1` — Tab order row should show `info` ("No elements with tabindex attributes found.").
2. Paste a URL whose HTML contains `tabindex="2"` → expect `warn` + fix text.
3. Paste a URL with only `tabindex="0"` or `tabindex="-1"` → expect `pass`.
4. Paste ShipSafe's own URL → Tab order row shows `info` (correct — no tabindex in static source).
5. Check methodology page says "eleven checks" and shows the new Tab order table row.

---

## Owner Action Items (complete list as of Run K)

**NEW this run:**
- **shipsafe `overseer/2026-06-08-p2c`** (43b69f2): P2.1c tab-order heuristic, 11th a11y check.
  Merge order: p2a → p2b → p2c (each is 1 fast-forward commit). GitHub Pages, free to merge.
  Owner QA: see steps above. No conflicts — each branch touches only app.js/methodology.html/index.html.

**Pre-existing (unchanged):**
- **shipsafe `overseer/2026-06-08-p2a`** (1a4c93a): contrast fix + P2.1a. **Merge FIRST.**
- **shipsafe `overseer/2026-06-08-p2b`** (a48ea9b): P2.1b ARIA roles. Merge p2a first.
- **CLOSE** shipsafe `overseer/2026-06-08-p1.3`, `p1.5`, `overseer/2026-06-08` — superseded.
- **marina `phase-3-golive`** (24 ahead) + `overseer/2026-06-07` (promo admin) — merge decision.
- **boots `overseer/2026-06-07`** — close (superseded by owner's own work).
- **apex `overseer/2026-06-07`** + `overseer/2026-06-08`** — rebase needed before merging.
- **CLOSE apex `feat/risk-hardening`** (already merged to main).
- **apex `feat/research-buildout`** / `feat/status-export`** — rebase/close decision.
- All secrets and env vars (boots Supabase, duke Upstash/Resend, marina Clerk/Stripe) — unchanged.
- ShipSafe P1.9 real domain — unchanged.

---

## Next Buildable (by project)

| Project | Next item | Blocked? |
|---------|-----------|----------|
| boots | P1.7 loading/empty/error state guards on primary routes | No |
| Websites | Fresh CSV batch / more section variants | No |
| Duke | Inline estimate widget, comparison posts | No |
| marina | wizard→storefront / slot-picker | YES — phase-3-golive merge |
| apex-quant | Hold until owner merges overseer branches | Soft-blocked |
| shipsafe | P2.1d — video captions flag (WCAG 1.2.2 Level A) | No |
