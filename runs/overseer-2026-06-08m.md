# Overseer Run M — 2026-06-08

**Run sequence:** 13th run this date (a=1, b=2, … m=13)  
**UTC timestamp:** 2026-06-08 ~14:xx UTC  
**Model:** claude-sonnet-4-6  
**Rotation index:** day-of-year 159 mod 6 = 3 → marina (blocked) → 4 = apex (blocked) → 5 = shipsafe ✓

---

## Step 2 — Reconcile

All six `main` branches confirmed unchanged from Run L. No new owner merges or pushes detected.

| Repo | main HEAD | Notable branches |
|---|---|---|
| boots | `74e44106` | `overseer/2026-06-07` (superseded), `feat/track-boss-fights` |
| websites | `826c8f3b` | `driftwood-cowgirl-boutique`, `pecota-vineyard` |
| duke | `c639ca4b` | `feat/hardening-sprint1`, `feat/website-links-to-crm` |
| marina | `6ac5ed2` | `phase-3-golive` (+24), `overseer/2026-06-07` (+1), `feat/finish-mvp-buildable` (+6) |
| apex-quant | `c699015a` | `overseer/2026-06-07` (41 behind main), `overseer/2026-06-08` (41 behind main) |
| shipsafe | `a86dff79` | `overseer/2026-06-08-p2a` (+1), `-p2b` (+1 of p2a), `-p2c` (+1 of p2b), `-p2d` (+1 of p2c) |

---

## Step 3 — Target selection

- Day-of-year 2026-06-08: 159 mod 6 = **3** → marina-booking-platform
- Marina: both next buildable items blocked on `phase-3-golive` merge (owner action). Skip.
- Index **4** → apex-quant: "hold until owner merges/rebases overseer branches." Skip.
- Index **5** → **shipsafe** ✓

---

## Step 4 — Build: ShipSafe P2.1e — Language of parts (WCAG 3.1.2 Level AA)

### What was built

Added the **13th accessibility check** to ShipSafe: "Language of parts" (WCAG 3.1.2, Level AA).

**Purpose:** WCAG 3.1.2 is the companion to 3.1.1. Where 3.1.1 requires a language declaration on `<html>`, 3.1.2 requires that any passage or phrase in a different language carry its own `lang` attribute so screen readers can switch pronunciation engines at that boundary. A French quotation read with English phonology is incomprehensible.

**Logic:** Find all elements with `lang` attributes excluding `<html>`. For each:
- Empty `lang=""` (after trim) → `fail`
- Value that doesn't match BCP 47 structural pattern `/^[a-zA-Z]{2,8}(-[a-zA-Z0-9]{2,8})*$/` → `fail`
- All valid → `pass` with count
- None found → `info` (page may be monolingual, or has unmarked foreign content we can't detect)

**Why `fail` not `warn`:** An invalid or empty `lang` attribute is worse than no attribute — it promises a language context to the screen reader but delivers none. The fix is mechanical (remove or correct the value), so the high severity is appropriate.

**`?demo=1` behavior:** Sample HTML has `<html>` with no `lang` attribute and no inline `lang` attributes → returns `info` (correct — monolingual page, language-of-page check separately handles the missing `<html lang>`).

**ShipSafe's own pages:** Both `index.html` and `methodology.html` have only `<html lang="en">`, no inline `lang` attributes → returns `info` (correct — ShipSafe is an English-only page). Self-audit passes.

**Files changed:**

1. **`app.js`** (bumped to `v=12`):
   - Added `"3.1.2": "language-of-parts"` to `WCAG_URL` map (enables linked law tag → Understanding doc)
   - Added 13th `["Language of parts", ...]` check after "Video captions" in `CHECKS.accessibility`
   - BCP47 regex: `/^[a-zA-Z]{2,8}(-[a-zA-Z0-9]{2,8})*$/` — catches empty, numeric-only, and structurally invalid tags; passes `en`, `fr`, `zh-TW`, `pt-BR`, `en-US`

2. **`index.html`**: `app.js?v=11` → `v=12` (cache bust)

3. **`methodology.html`**:
   - "These twelve checks" → "These thirteen checks"
   - New table row: Language of parts / WCAG 3.1.2 / Level AA / full rationale
   - New limitations bullet: "Unmarked foreign-language content cannot be detected" — honest scope statement

### Branch and commit

- **Branch:** `overseer/2026-06-08-p2e` (branched from `overseer/2026-06-08-p2d` tip `a563c41`)
- **Commit:** `9a36eb1`
- 1 commit ahead of p2d / 5 commits ahead of main
- Pushed to `origin/overseer/2026-06-08-p2e` ✓

### Verification

**Logic test (node -e):** Ran BCP47 regex assertions against 9 cases — all passed:
- `""` → fail ✓, `"  "` (whitespace) → fail ✓, `"1"` (digit-only) → fail ✓
- `"en"` → pass ✓, `"fr"` → pass ✓, `"zh-TW"` → pass ✓, `"pt-BR"` → pass ✓, `"en-US"` → pass ✓

**Cannot screenshot** — no headless Chrome in this cloud environment. Static analysis confirms:
- `querySelectorAll("[lang]")` filters `<html>` correctly via tagName comparison. ✓
- Empty-lang count logic (detail message variant) is correct. ✓
- `WCAG_URL["3.1.2"]` = `"language-of-parts"` → law tag links to Understanding 3.1.2. ✓
- methodology.html count updated: "twelve" → "thirteen". ✓
- ShipSafe's own HTML: only `<html lang="en">`, no inline lang attrs → `info` result → passes own audit. ✓

**Owner QA to confirm before merging:**
1. Visit `?demo=1` — language-of-parts row should show info badge (no inline lang in sample).
2. Paste a URL with `<span lang="">` → expect fail + fix guidance + WCAG 3.1.2 law link.
3. Paste a URL with `<span lang="fr">Merci</span>` → expect pass with count.
4. Paste a URL with mixed valid/invalid (e.g. `<span lang="fr">` + `<div lang="">`) → expect fail.
5. Verify methodology.html shows "thirteen checks" in the accessibility opening paragraph.

---

## Step 6 — Summary

### All-project reconcile (vs Run L)

- **boots:** `main` unchanged at `74e44106`. No new owner work detected.
- **websites:** `main` unchanged at `826c8f3b`. Prospect branches still open.
- **duke:** `main` unchanged at `c639ca4b`. `feat/hardening-sprint1` + `feat/website-links-to-crm` still pending.
- **marina:** `main` unchanged at `6ac5ed2`. All pending branches unchanged. Awaiting owner merge decisions.
- **apex-quant:** `main` unchanged at `c699015a`. `overseer/2026-06-07` + `overseer/2026-06-08` still 41 commits behind main. No owner rebase action.
- **shipsafe:** `main` unchanged at `a86dff79`. p2a/p2b/p2c/p2d still pending owner merge. New branch `overseer/2026-06-08-p2e` pushed this run.

### This run's delivery

| Item | Status |
|---|---|
| Target | ShipSafe (index 5 after skipping marina + apex) |
| Built | P2.1e: 13th accessibility check — Language of parts (WCAG 3.1.2 Level AA) |
| Branch | `overseer/2026-06-08-p2e` |
| Commit | `9a36eb1` |
| Logic verified | Yes — node BCP47 regex test, 9 assertions all passed |
| Screenshotted | No — cloud env has no headless Chrome; static logic review confirms correctness |
| OVERSEER.md | Updated (Run L → Run M, ShipSafe state + owner-action list) |
| Run report | `runs/overseer-2026-06-08m.md` created |

### New owner-action blockers discovered

None new. The shipsafe merge chain (p2a → p2b → p2c → p2d → p2e) now has 5 commits. Each is a 1-commit clean branch; they merge in order. Note: p2d was the last Run L delivery and is also pending — owner should merge the whole chain in one session.

### Next buildable for shipsafe (p2f)

P2.1f — skip-link destination check (WCAG 2.4.1 Level A): verify that the `href` in `<a class="skip">` resolves to an element that exists in the DOM. ShipSafe has `<a class="skip" href="#main">` — if `#main` doesn't exist in the target page's source, keyboard users clicking "Skip to content" go nowhere. Check: find `<a class="skip">`, extract the fragment, look for matching `id` in the DOM.
