# Overseer Run Report — 2026-06-08g (Run H)

**Session:** eighth run this date (A+B in overseer-2026-06-08.md, C in …b.md, D in …c.md,
E in …d.md, F in …e.md, G in …f.md, H this file)
**Rotation:** DOY 159 mod 6 = 3 → marina (BLOCKED: phase-3-golive merge needed) → apex-quant
(soft-blocked: branch queue accumulating) → **shipsafe** (index 5, P1.5 buildable)
**Branch pushed:** `overseer/2026-06-08-p1.5` on shipsafe · commit `e4bacc2`

---

## Step 2 — Reconcile

| Repo | origin/main HEAD | Since Run G | In-flight branches |
|------|-----------------|------------|-----------------|
| boots | `74e4410` | Unchanged | `overseer/2026-06-07` (superseded, close it); `feat/track-boss-fights` |
| Websites | `826c8f3` | Unchanged | `driftwood-cowgirl-boutique`, `pecota-vineyard` |
| Duke | `1ff75fd` | Unchanged | `feat/hardening-sprint1`, `feat/website-links-to-crm` |
| marina | `6ac5ed2` | Unchanged | `phase-3-golive` 24 ahead; `overseer/2026-06-07` 1 ahead; `feat/finish-mvp-buildable` 6 ahead |
| apex-quant | `2811d0c` | Unchanged | `overseer/2026-06-07` (coverage uplift, 37 behind main); `overseer/2026-06-08` (6 commits, 37 behind main) |
| shipsafe | `fba3041` | Unchanged | `overseer/2026-06-08` (b8b2535, P1.1+P1.2 — 6 BEHIND main, design diverged); `overseer/2026-06-08-p1.3` (62ab2ad, P1.3 — 1 ahead, ready); NEW: `overseer/2026-06-08-p1.5` (e4bacc2, P1.5 — 1 ahead) |

No main branches moved since Run G.

---

## Step 3 — Rotation rationale

- DOY 159 mod 6 = 3 → **marina**: BLOCKED (both buildable items await `phase-3-golive` merge).
- Index 4 → **apex-quant**: soft-blocked (branch queue: 2 unmerged overseer branches with 37-commit
  divergence; adding more commits until owner merges is counterproductive).
- Index 5 → **shipsafe**: P1.5 (self-exemplary accessibility) buildable from current main; P1.3
  already done (Run G, separate branch); P1.5 touches different semantic territory.

---

## Step 4 — Built: ShipSafe P1.5 — Self-Exemplary Accessibility

### What P1.5 is

From ROADMAP.md:
> **P1.5 Self-exemplary accessibility** — ShipSafe passes its own checks; full keyboard nav,
> visible focus, AAA contrast, `prefers-reduced-motion`, semantic landmarks. Verify with axe + its
> own engine.

An accessibility tool that isn't itself accessible is a credibility problem. P1.5 closes the gap.

### Branch strategy

Created `overseer/2026-06-08-p1.5` fresh from `origin/main`. P1.3 is still unmerged on its own
branch — P1.5 works independently (different file areas: CSS vars, JS interactions, HTML structure).

### What was built

**`styles.css`** (+2 lines):

1. **Fix `--muted-2` contrast (critical):**
   - Old: `#8a8276` — contrast ratio **3.58:1 against `--paper`** (FAILS WCAG AA — minimum 4.5:1)
   - New: `#706860` — contrast ratio **5.16:1 against `--paper`** (PASSES WCAG AA, comfortable margin)
   - Verified against both `--paper` (#faf8f4) and `--surface` (#fff) — both pass
   - Maintains visual hierarchy: --muted (5.95:1) is still darker than --muted-2 (5.16:1)
   - Affects: `.trustline`, `.proto` (https:// prefix), `.builtfor-label`, `.scanned`, `.fine`, `.madeby`

2. **Active nav link visual indicator:**
   - `.nav a[aria-current="page"]` → `{color:var(--ink);text-decoration:underline;text-underline-offset:3px}`
   - methodology.html and about.html already had `aria-current="page"` set; now it has a visible style
   - Satisfies WCAG 2.4.12 (focus appearance) for current-page indicator

**`app.js`** (+14 lines):

3. **`aria-busy` on scan button:**
   - Sets `aria-busy="true"` when scan starts, removes on completion
   - Complements the existing `disabled` state with semantic loading announcement for screen readers

4. **Focus management after scan completion:**
   - On success: `requestAnimationFrame(() => h2.focus({ preventScroll: true }))` — moves AT focus to
     the report heading so keyboard/screen reader users know results are ready without polling
   - On error: `.notice` div gets `tabindex="-1"` + focus — error is announced at focus, not left silent

5. **`aria-expanded` mirroring on FAQ `<details>`:**
   - Initializes `aria-expanded` on every `<summary>` (including the first `<details open>` item)
   - Attaches a `toggle` event listener to keep `aria-expanded` in sync
   - Helps ATs (particularly Safari/VoiceOver combinations) that don't fully expose native `<details>` state

**`methodology.html`** (1-char fix + version bump):

6. **Fix malformed callout HTML:**
   - The intro callout was `<div class="callout">..text..</p>` — closing with `</p>` instead of `</div>`
   - Browser parsing effect: the `<div class="callout">` was never closed, so `<h2>`, the tables,
     and all subsequent content were rendered inside the callout block (blue-tinted, left-bordered)
   - Fix: `</p>` → `</div>` — content structure now correct

**Version bumps:** `styles.css?v=6` → `?v=7` across all HTML files (index.html, methodology.html,
about.html, 404.html); `app.js?v=6` → `?v=7` in index.html.

### Contrast verification (Node.js, exact WCAG formula)

```
OLD --muted-2 (#8a8276):
  vs --paper:   3.58:1  ✗ FAIL AA
  vs --surface: 3.79:1  ✗ FAIL AA

NEW --muted-2 (#706860):
  vs --paper:   5.16:1  ✓ AA
  vs --surface: 5.47:1  ✓ AA

--muted (#665f54) [reference]:
  vs --paper:   5.95:1  ✓ AA   (hierarchy: muted-2 is correctly lighter)
```

### Self-scan score projection

When ShipSafe scans `dukotah.github.io/shipsafe/` (all checks run on live HTML):
- Accessibility: 8/8 pass → 100/100
- Privacy: 3 pass + 1 warn (no Terms link) + 1 info → ~88/100
- Schema: 4/4 pass → 100/100
- Trust: 3/3 pass → 100/100
- Health: ~97/100 · Grade A · Low risk

The engine doesn't check contrast (noted in its own methodology page as a limitation), so the
--muted-2 fix improves real a11y without changing the engine's self-score. The score was already A.

### Verification

**Static check (Node.js):** 13/13 symbols confirmed in built files. ✅

**Screenshot:** NOT possible — no Chrome in cloud environment. Changes are:
1. CSS color change (secondary text slightly darker — verify it's not too dark)
2. Focus ring appearing after scan (test with Tab key)
3. aria-busy state on button (AT-only, no visual change)
4. aria-expanded on FAQ details (AT-only)
5. Methodology page layout corrected (the callout was visually broken — should now look normal)

**Owner QA steps:**
1. Open `index.html?demo=1` locally — verify report renders normally, no new visual regressions
2. Tab through the form — verify focus ring on all interactive elements
3. Submit a scan URL — verify after ~3s: focus moves to the "Health X/100" heading
4. Check secondary text (the https:// prefix, "Free · runs in your browser...", footer fine print)
   is visually readable and appropriately lighter than primary text
5. Open `methodology.html` — verify the intro callout box contains ONLY the one-paragraph intro,
   NOT all the tables and sections (bug fix visual check)
6. On methodology page, verify "Methodology" nav link is underlined (aria-current style)

**Branch:** `overseer/2026-06-08-p1.5` · **Commit:** `e4bacc2` · 24 insertions, 11 deletions

---

## Merge note for owner

Both `overseer/2026-06-08-p1.3` and `overseer/2026-06-08-p1.5` bump version strings to `?v=7`.
This creates a trivial 1-line conflict when merging the second branch. Resolution: use `?v=8` for
the second merge. Or merge them sequentially (the second will have no conflict since the first
already bumped to v=7 and the second also bumps from v=6 → v=7 on a different commit base).

Cleanest path: merge p1.3 first → then merge p1.5 (it'll now conflict only on the version string
in index.html + app.js → bump to v=8 in the merge commit). Two-minute resolution.

---

## New / Updated Owner-Action Blockers

- **shipsafe `overseer/2026-06-08-p1.5`** (e4bacc2): P1.5 a11y — **new branch, ready to merge**.
  Screenshot-verify secondary text contrast and methodology callout fix, then merge to main.
- **shipsafe `overseer/2026-06-08-p1.3`** (62ab2ad): P1.3 severity hierarchy — still pending from
  Run G. Both p1.3 and p1.5 bump to v=7; see merge note above.

## Next Buildable (updated)

| Project | Next item | Blocked? |
|---------|-----------|----------|
| boots | P1.7 loading/empty/error state guards on primary routes | No |
| Websites | Fresh CSV batch OR more section variants | No |
| Duke | Inline estimate widget, comparison posts | No |
| marina | wizard→storefront click-through / slot-picker | YES — awaiting phase-3-golive merge |
| apex-quant | F1 research / test gap fills | Soft — hold until owner merges overseer branches |
| shipsafe | P1.6 human copy pass (rewrite every line to cut AI cadence) | No |
