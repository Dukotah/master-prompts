# OVERSEER — Master Project Tracker & Coordination Brief

**Owner:** Dukotah (GitHub `Dukotah`) · **Maintainer of this file:** the overseer agent
**Purpose:** single source of truth for an overseer/coordinator agent that keeps work moving
across all projects while the owner is away. Update the per-project STATE lines whenever you
(or a delegated agent) finish a chunk of work.

> Last reconciled: 2026-06-09 (Run C — 3rd run this date). Verify git state before acting — these notes go stale fast
> because cloud agents push to `origin/main` mid-session on several repos.

**Automation:** a daily remote routine runs this brief. Routine `trig_014XPBhL62SX3vh5qei8oNPe`
("Overseer — daily project coordinator"), 06:00 PDT / 13:00 UTC daily, Sonnet, clones all 5 active
repos + master-prompts. It reconciles state, advances ONE project per run (rotating, UTC day-of-year
mod 6), pushes work to `overseer/<UTC-date>` branches (NEVER main), and pushes its updated brief +
`runs/overseer-<date>.md` report to this repo's main. Manage at
https://claude.ai/code/routines/trig_014XPBhL62SX3vh5qei8oNPe (the overseer agent: this is YOU).

**Dashboards:** (1) **Hosted, live, phone-friendly** — https://dukotah.github.io/overseer-dashboard/
(repo `Dukotah/overseer-dashboard`, static `index.html` reading the GitHub API client-side, free on
GitHub Pages, auto-refresh 5min; shows GitHub-side truth + this owner-action list + latest run report).
(2) **Local** — `~/overseer-status.mjs` / `overseer.bat` (double-click) shows live LOCAL git state incl.
uncommitted/unpushed work the hosted one can't see. Use both: hosted = from anywhere; local = this machine.

---

## 0. Operating guardrails (READ FIRST — these override "just ship it")

These are hard-won house rules. Violating them costs the owner money or breaks prod.

1. **Pushing `main` = a production Vercel deploy** on `boots`, `websites`, `duke`,
   `marina-booking-platform`. The owner is on a **metered Vercel plan and conserves daily
   deploys**. DEFAULT: commit locally, do **not** push, batch pushes. Only push `main` when
   the owner has explicitly authorized a deploy this session. Pushing a **non-`main`** branch
   is safe (preview deploy only) — use that to "save without deploying."
2. **Agent budget is tight** — owner is on a **$100/mo plan**. Keep multi-agent fan-outs lean:
   prefer Sonnet/Haiku, batch files per agent, ~5–12 agents not 30–80. Disjoint file ownership;
   integrate shared files (store/nav/index/registries) yourself.
3. **Machine is RAM-constrained** (~7.35 GB, Win11 Dell laptop, has OOM-killed terminals).
   Run builds/tests serially, not in parallel. If `npm run build` flakes with OOM, `rm -rf .next`
   and retry. Don't launch many heavy local processes at once.
4. **`duke` local checkout has historically been a STALE feature branch** 200+ commits behind
   `origin/main`. ALWAYS `git fetch origin main` and diff before editing/pushing any repo here.
   (It's on `main` and synced right now, but re-verify every session.)
5. **Verify quality the right way per project:** `websites` = SCREENSHOT pages (headless Chrome),
   never trust build-success (undefined CSS tokens render invisibly but build green). `boots` =
   `npm run check` (curriculum) + `npm test` + `npm run build`. `marina`/`duke`/`apex` = typecheck
   + test suite + build.
6. **Integrity rules the owner cares about:** never fabricate data to look good — no fake GitHub
   contribution commits (boots), no fabricated reviews/testimonials/ratings without a "sample/
   representative" label (duke), no fake photos passed as a business's own (websites), no
   survivorship-biased backtest claimed as deployable (apex-quant).
7. **Owner working style:** keep momentum, minimal check-ins. Pick sensible defaults on reversible
   calls and proceed; only stop to ask on irreversible/outward-facing actions (deploys, sending
   cold email, publishing).
8. **Commit-message gotcha on this Windows box:** `git commit -F -` heredoc fails with
   `EUNKNOWN uv_spawn`. Write the message to a temp file (`$LOCALAPPDATA/Temp`) and `git commit -F <file>`.

---

## 1. Project portfolio at a glance

| Project | Repo | Local | Tier | Deploy on push? | Live git state (2026-06-08 Run P) |
|---|---|---|---|---|---|
| **Boots / Cantrip** | `Dukotah/boots` | `~/boots` | 🟢 Active flagship | Yes (main) | `main` at `4ae73a3c` (June 8 21:11 UTC — feat/catalog-pricing-dashboard merged PR #23); `feat/track-boss-fights` open; `overseer/2026-06-07` superseded — close |
| **Websites factory** | `Dukotah/Websites` | `~/websites` | 🟢 Active | Yes (main) | `main` at `826c8f3b` (June 7 23:37 UTC; 4 fresh real-scrape demos); prospect branches: `driftwood-cowgirl-boutique`, `pecota-vineyard` |
| **Duke / Copper Bay Tech** | `Dukotah/Duke` | `~/duke` | 🟢 Active | Yes (main) | `main` at `7c3a0412` (June 8 21:21 UTC — PR #34 website-signal block + PR #33 deep-enriched lead data); `feat/hardening-sprint1`, `feat/website-links-to-crm` pending review |
| **Marina booking SaaS** | `Dukotah/marina-booking-platform` | `~/marina-booking-platform` | 🟢 Active | Yes (main) | `main` at `6ac5ed2` (unchanged); `phase-3-golive` **24 ahead**; `overseer/2026-06-07` **1 ahead** (promo admin page); `feat/finish-mvp-buildable` **6 ahead** — owner merge decision needed |
| **Apex Quant** | `Dukotah/apex-quant` | `~/apex-quant` | 🟢 Active | No (CI cron only) | `main` at `f752197f` (June 8 21:28 UTC — cron state update); `overseer/2026-06-07` **1 ahead** (needs rebase ~46 behind); `overseer/2026-06-08` **5 ahead** (needs rebase); `feat/risk-hardening` 1 ahead (should be merged — verify); `feat/research-buildout` 0 ahead (diverged/absorbed); `feat/status-export` 1 ahead (obsolete) |
| **ShipSafe** | `Dukotah/shipsafe` | `~/shipsafe` | 🟢 Active (Copper Bay Labs) | GitHub Pages (free, on main) | `main` at `a86dff79` (June 8 05:31 UTC); chain p2a→p2h pending owner merge; **overseer/2026-06-09** `f90a158` (4 commits: P2.1i autocomplete + P2.1j iframe title + P2.1k viewport zoom + **P2.1l color contrast** — Run C); old p1.3/p1.5/2026-06-08 branches superseded — close |
| **Sonoma lead scraper** | `Dukotah/sonoma-lead-scraper` | `~/sonoma-lead-scraper` | 🟡 Supporting | No | `main` stale — 24 behind origin, 3 dirty |
| **Master prompts** | `Dukotah/master-prompts` | `~/master-prompts` | 🟡 Supporting | No | `main` — updated this run |
| **Apex Trader** | `Dukotah/apex-trader` (private) | — | 🟡 Supporting | Yes | Next.js control surface for apex-quant |
| **Tour booking research** | `Dukotah/tour-booking-platform-research` | `~/tour-booking-platform-research` | ⚪ Reference | No | Static research, done |
| Misc / dormant | `JobHunt`, `SwiftJob`, `crewcost`, `romanartisanmill`, `LakeSonoma`, `Marketing`, `FAITHFULDOGPAWS`, `Silo-app-*`, `iPhone-Claude-build` | various | ⚪ Dormant/experiments | — | Not actively coordinated; surface only if owner asks |

**Cross-project wiring that exists (don't break it):**
`sonoma-lead-scraper` CSV export → `duke` CRM (`/api/crm/leads` fetches the raw-GitHub CSV, 1hr
cache + webhook) → `websites` builds a demo site → `npm run push-to-crm` attaches the demo
`previewUrl` back onto the matched lead (token-gated `/api/crm/admin/preview-url`, matched by
normalized business name). `marina` seed client is the owner's own Lake Sonoma Marina.

---

## 2. Per-project briefs

### 🟢 Boots / Cantrip — gamified coding academy
- **Stack:** Next.js 14 App Router, TS, Tailwind, Zustand+localStorage, Supabase (auth/profiles),
  Stripe, Anthropic tutor. Monaco; lessons run client-side (JS Web Worker / Pyodide / sql.js).
- **Naming:** product is "Cantrip", repo/mascot is "Boots".
- **Where things live:** engine `src/store/useGameStore.ts`; curriculum data `src/lib/curriculum/`
  (register every new module in FOUR places: `index.ts`, `scripts/check-curriculum.ts` hand-list,
  `tracks.ts`, `paths.ts`); operating spine at root `VISION/ROADMAP/TASKS/PROGRESS/DECISIONS.md`.
- **State:** huge surface shipped & live. 151 modules / 1039 lessons / **377 unit tests** /
  1971 curriculum tests (June 7 2026). Owner's SEO push June 8: 60 blog posts total, FAQPage +
  ItemList JSON-LD schema on posts/paths, /projects/[slug] noindex bug fixed, sitemap expanded.
  PRs #21 (career-pack-polish), #22 (first-lesson-loop unblock), #23 (catalog-pricing-dashboard audit)
  all merged June 8 21:11 UTC. `overseer/2026-06-07` branch superseded by owner's own work; close.
- **Gotchas:** `check-curriculum.ts` runs starter stubs AND solutions with NO timeout — an
  unbounded loop in a stub or a drain-loop test HANGS forever. JS runner = Web Worker (no
  window/localStorage). Cloud agents push to `origin/main` AND open PRs — fetch+rebase before push.
- **Owner-action blockers (can't be done without secrets):** apply Supabase migrations 0005/0006/0007
  live; set Vercel env (SERVICE_ROLE, STRIPE_*, RESEND, VAPID, CRON_SECRET, GITHUB_APP_*, SENTRY_DSN).
- **Verify:** `npx tsc --noEmit` · `npm run check` · `npm test` · `npm run build`.
- **Next buildable:** P1.7 — verify loading/empty/error states on primary routes (dashboard, lesson,
  learn, leaderboard, account, teams) — find routes that don't handle these states and add guards.

### 🟢 Websites — outreach demo-site factory (Astro)
- **What:** mass-generates per-business demo sites at `/p/<slug>` (one Vercel deploy hosts all
  demos in `sites/demo-gallery/`) for cold outreach. `npm run generate-prospects -- data/<file>.csv`.
- **State:** v2 design system live; deep anti-"AI-slop" framework (per-business fonts/palettes/
  shape, OKLab color, section-variant pool, real-photo scraping + Sharp photo-quality scorer,
  astro:assets image pipeline, vision-QA harness, outreach funnel w/ claim banner + noindex gate).
  Latest: 4 fresh real-scrape demos live on main (Golden Gear Automotive, Joon Hair, Petaluma Pie
  Company, Brewsters Beer Garden — screenshot-verified, audit clean). Mobile header drawer +
  conversion-zone footer shipped. Brand design tokens (--brand-tint/--surface-3), grain/noise
  texture overlay, font preload, schema compliance, CRO (post-testimonials CTA inject), layout
  bugfixes all on main. Prospect branches `driftwood-cowgirl-boutique` + `pecota-vineyard` still open.
- **Gotchas:** quality must be checked by **screenshot** (headless Chrome `--headless=new
  --screenshot` vs `npm run preview`, auto-picks free port ~4322). Undefined `var(--token)` =
  silent invisible text, build stays green. A shared image component breaks parents' scoped `img`
  CSS — `SiteImage` owns fill/zoom. Build/preview from `sites/demo-gallery`. DON'T re-scrape
  curated grade-A demos to "enrich" — it regresses heroes (learned the hard way).
- **Owner-action:** set `SITE_URL`/`GALLERY_BASE_URL` on Vercel; push/deploy decision.
- **Next buildable:** run a fresh CSV batch through the upgraded pipeline (roster ~10); mine
  CodeStitch for MORE section variants; before/after slider; OKLab hue math.

### 🟢 Duke — Copper Bay Tech (own agency site, copperbaytech.com)
- **Stack:** Next.js 16 + Tailwind v4. Sonoma County web/IT/cyber/AI for small businesses.
  `@/config/site` + `@/config/pricing` are single sources of truth. Has a full CRM (`/crm`,
  Upstash Redis, db.ts), 30+ blog posts, large service×city page matrix, 4 free-tool lead magnets.
- **State:** `main` at `7c3a0412` (June 8 21:21 UTC) — CRM now shows deep-enriched lead data:
  grade pills, deliverability badges, decision-maker block, phone-type chips, website quality
  chips (site_quality / digital_presence / builder / slow-load flag), score breakdowns (score_why).
  `{demoUrl}`/`{claimByDate}` substituted server-side in outreach emails. In-flight:
  `feat/hardening-sprint1`, `feat/website-links-to-crm`.
- **Gotchas:** ⚠️ historically the local checkout is a stale branch — **always fetch origin/main
  first**. One CRM only now. Lint flags `Date.now()` in component scope — use a module-level helper.
- **Owner-action (the real growth levers — only the owner can do these):** Google Business Profile
  as a service-area business; collect real reviews + set `GOOGLE_REVIEW_URL`; founder headshot +
  LinkedIn URL; Vercel env (`CRM_ADMIN_TOKEN`, `GALLERY_BASE_URL`, `GITHUB_WEBHOOK_SECRET`,
  `OUTREACH_DOMAIN_VERIFIED_DATE`, flip on Web Analytics); verify Resend sending domain before any
  cold email; set `UPSTASH_REDIS_REST_URL` + `UPSTASH_REDIS_REST_TOKEN` + `RESEND_API_KEY` in
  Vercel prod (root cause of the form hang the timeout fix works around).
- **Verify:** `tsc` + `eslint` + `vitest` (~132 tests) + `next build`.
- **Next buildable (no owner dep):** ROADMAP "Next 100" — inline estimate widget,
  comparison posts, dialer shortcuts, Cmd-K palette, image-loading audit.

### 🟢 Marina booking platform — multi-tenant SaaS (beat Singenuity)
- **Stack:** Turborepo/pnpm + Next 14 (web+admin) + Node/Hono API + Postgres/Prisma + Square + Clerk.
  Multi-tenant from day one: operator_id FK + Postgres RLS + app-layer scoping. Seed tenant = Lake
  Sonoma Marina (19 activities). **Repo = shared brain:** AGENTS.md + docs/{CONTEXT,ARCHITECTURE,
  DECISIONS,ROADMAP}.md + root VISION/ROADMAP/TASKS/PROGRESS — keep these updated.
- **State:** Phases 0–3 done. Branch `phase-3-golive` (stacks phase-1 cockpit + phase-2 self-serve
  front door + phase-3 money-robustness) is **24 commits ahead, unpushed** (Vercel quota). DB is
  **live on Neon** (US-West). A stranger can self-provision a tenant. Isolation suite 8/8 live,
  typecheck 9/9, builds green. **Branch `overseer/2026-06-07`** adds promo code admin page
  (commit `658e478`, tsc clean, core 69/69 — safe to squash-merge onto main).
- **Gotchas:** Neon `neondb_owner` has BYPASSRLS — tenant queries MUST use a NOBYPASSRLS `app_user`
  role (`APP_DATABASE_URL`). Payment processor is **Stripe** (D-013, switched off Square). Load env
  via `tsx --env-file=../../.env` (bash `export` mangles Neon URLs). No pnpm/Docker on this box —
  `corepack enable`, hosted Neon. `feat/finish-mvp-buildable` branch adds customer auth, 3DS/SCA,
  and reschedule UI — overlaps with `phase-3-golive`; owner should reconcile before merging.
- **Owner-action:** Clerk SECRET key (to flip REQUIRE_CLERK_AUTH=true); Stripe test keys; diagnose
  marina-admin Vercel deploy failure; per-tenant billing; legal/ToS; Neon restore drill; merge
  phase-3-golive + overseer/2026-06-07 (trivial sidebar conflict — combine Resources/Gift Cards/Promos).
- **Next buildable:** wizard→storefront click-through (OnboardingWizard.tsx is in phase-3-golive
  diff — do after that branch merges); web account slot-picker UI for 2.1 reschedule (also in that diff).
  ⚠️ Both items are BLOCKED on `phase-3-golive` merge (owner action). Skip to apex/shipsafe if marina is first in rotation.

### 🟢 Apex Quant — algo-trading framework (Python)
- **What:** event-driven, asset-agnostic trading framework with a 9-gate validation Gauntlet.
  Strategies emit `SignalEvent` only; `RiskManager` is the sole `OrderEvent` producer. Its own
  `CLAUDE.md/DECISIONS.md/ROADMAP.md/SESSION_PLAYBOOK.md` are the source of truth — **read first**.
- **State:** build COMPLETE (Phases 1–6 + F1 + F2 + F3). Realignment commit (`cf12b22a`, June 8
  21:27 UTC) landed on main: coverage floor raised 70→90 (measured 94.53%), architecture fitness
  tests enforce golden rules 1–2, watchdog workflow (>26h dead-man's-switch), preflight self-heals
  `data/state/` dir. 3155 tests passing, 94.53% coverage. Multi-asset trend strategy LIVE ON PAPER.
  `feat/risk-hardening` MERGED to main (c699015a): 5 fail-closed guardrails. `overseer/2026-06-07`
  (fa1742b) + `overseer/2026-06-08` (b202e1d) now ~46+ behind main after realignment — both need
  rebase before landing.
- **Workflow rule:** normally **one module per session, tested, then stop**; built files are FROZEN.
  Run `ruff format --check` locally before pushing (CI enforces it).
- **Owner-action / time-gated:** run out the 30-day paper gate (`python -m scripts.report`); only
  flip `APEX_MODE=live` after Sharpe holds. Decide on paid delisted-data source to unlock F3.3
  live sleeve. Rebase + merge `overseer/2026-06-07` + `overseer/2026-06-08` when convenient.
  Close stale branches: `feat/risk-hardening` (merged), `feat/status-export` (~1 ahead, obsolete),
  review `feat/research-buildout` (22 ahead / ~55+ behind, heavily diverged).
- **Next buildable:** hold until owner merges/rebases the two overseer branches. Branch queue too
  long to add more work safely. Once cleared: probe Gate-3 walk-forward efficiency metric anomaly
  (reported anomalous values like 66/397 in IMPROVEMENTS) or targeted coverage fills.

### 🟢 ShipSafe — Copper Bay Labs product #1 (from the forge factory)
- **What:** standalone static web app (vanilla HTML/CSS/JS, GitHub Pages, $0). Paste a URL → ~20
  ADA/WCAG + privacy + schema checks → plain-English "demand-letter risk" report. Live at
  `dukotah.github.io/shipsafe/`. Deliberately kept STANDALONE (not folded into copperbaytech.com) so
  it grows its own brand/audience + a self-serve Pro tier; the agency benefits later via a one-way
  lead bridge.
- **The mandate:** make it NOT look AI-built — credibility is the product. Its own roadmap
  (`~/shipsafe/ROADMAP.md`) is the source of truth: Phase 1 = de-slop. Work phases top-down.
- **State:** Owner's comprehensive P1 sweep landed on main (a86dff79, June 8 05:31): P1.2 custom SVG
  icons (inline ICON set, no emoji), P1.3 severity hierarchy (STATUS_ORDER sort + letter grade),
  P1.4 shareable result (copy button + URL deep-link), P1.5 a11y (skip link, focus-visible,
  reduced-motion, semantic landmarks), P1.6 human copy pass, P1.7 trust depth (methodology + about
  deepened), P1.8 polish (skeleton loading, microinteractions). Brand: copper + Fraunces/Hanken.
  NOTE: owner's P1.5 did NOT fix `--muted-2:#8a8276` (3.58:1, WCAG AA fail).
  **Pending-merge chain (p2a→p2h, 8 branches, each 1 commit, merge in order):**
  - **p2a** (1a4c93a): (1) `--muted-2` #8a8276→#706860 contrast fix; `.nav a[aria-current]`
    underline; all HTML on unified `?v=8`. (2) 9th a11y check "Descriptive link text" (WCAG 2.4.4,
    `warn`); focus management post-scan; `aria-expanded` FAQ sync; methodology updated.
  - **p2b** (a48ea9b): 10th a11y check — "ARIA roles are valid" (WCAG 4.1.2, `fail`).
  - **p2c** (43b69f2): 11th a11y check — "Tab order preserved" (WCAG 2.4.3, `warn`).
  - **p2d** (a563c41): 12th a11y check — "Video captions" (WCAG 1.2.2, `fail`).
  - **p2e** (9a36eb1): 13th a11y check — "Language of parts" (WCAG 3.1.2, `fail`).
  - **p2f** (1448a1c): 14th a11y check — "Skip link resolves" (WCAG 2.4.1, `fail`).
  - **p2g** (357e7fe): 15th a11y check — "Duplicate IDs" (WCAG 4.1.1, `fail`).
  - **p2h** (93df640, Run P): 16th a11y check — "ARIA required attributes" (WCAG 4.1.2, `fail`).
    Checks 12 roles against their WAI-ARIA 1.2 required state/property attributes. app.js v=15.
  - **overseer/2026-06-09** (7c39412 + 3f92ef0 + 5a65686 + f90a158, Runs A + B + C): four commits on one branch:
    - **P2.1i** (7c39412): 17th a11y check — "Autocomplete on personal-data fields" (WCAG 1.3.5, `warn`). app.js v=16.
    - **P2.1j** (3f92ef0): 18th a11y check — "Frames have accessible names" (WCAG 4.1.2, `fail`). Checks `<iframe>` elements for `title` attribute; demo gets an untitled Google Maps iframe → shows fail. app.js v=17.
    - **P2.1k** (5a65686): 19th a11y check — "Viewport zoom restrictions" (WCAG 1.4.4, `warn`). Parses `<meta name="viewport">` for `user-scalable=no` or `maximum-scale≤1`; demo updated with `user-scalable=no` viewport → shows warn. app.js v=18.
    - **P2.1l** (f90a158): 20th a11y check — "Color contrast (inline styles)" (WCAG 1.4.3, `warn`). Heuristic: parses inline `color`/`background-color` style attributes, computes WCAG relative-luminance ratio, flags elements below 4.5:1 (normal text) or 3:1 (large text). Supports hex, rgb/rgba, 30+ named colors. Demo updated with `<p style="color:#aaa;background-color:#fff">` → shows warn (2.3:1). methodology.html updated: nineteen→twenty checks, new table row, updated limitation note. app.js v=19.
  Old branches superseded — all safe to close:
  - `overseer/2026-06-08-p1.3` (62ab2ad): severity hierarchy done by owner's sweep.
  - `overseer/2026-06-08-p1.5` (e4bacc2): owner did P1.5 without --muted-2 fix (now in p2a).
  - `overseer/2026-06-08` (b8b2535): teal/Jakarta design rejected, 6 behind main.
- **Verify method:** SCREENSHOT every UI change in headless Chrome (never trust build-success),
  AND the tool must keep passing its own accessibility engine.
- **Deploy:** GitHub Pages from `main` (FREE — no Vercel quota). Push work to `overseer/<date>`
  branches; merging to main is low-risk, owner's call.
- **Next buildable:** P2.1m — Touch target size (WCAG 2.5.5 Level AAA, `warn`). Check for
  interactive elements (links, buttons, inputs) that appear small based on inline `width`/`height`
  style attributes. Threshold: less than 44×44px inline-declared size is flagged as a potential
  touch-target issue (heuristic — CSS-sized elements not detectable from source). Status `warn`
  (Level AAA; inline styles only). Branch from overseer/2026-06-09 or new date branch.

### 🟡 Supporting
- **sonoma-lead-scraper** (Python) — scrapes Sonoma County business leads → CSV → feeds duke CRM.
  Local `main` is **24 commits BEHIND origin** (stale) + 3 dirty. The duke CRM reads a *different*
  branch's export (`claude/lead-data-sourcing`). Sync local before working here.
- **master-prompts** — curated prompt library for Claude Code / agents. `main` updated each run.
  Low-risk, no deploy.
- **apex-trader** (private) — dashboards/auth/strategy-risk visibility over apex-quant.

---

## 3. Overseer agent — operating protocol

**Mission:** keep each 🟢 Active project advancing on its next buildable item while the owner is
away, without burning deploys/budget or breaking the guardrails in §0.

**Each cycle:**
1. **Reconcile reality.** For every active repo: `git fetch`, recompute ahead/behind + dirty,
   and update the table in §1 + the project's STATE line. Cloud agents move `origin/main`
   independently — never assume the notes are current.
2. **Pick the next unit of work** from each project's "Next buildable" list. Prefer items with
   **no owner dependency** (don't pick anything blocked on a secret/credential/decision — instead
   log it under that project's Owner-action and move on).
3. **Delegate, don't do it all inline.** Spawn ONE focused agent per project/task (lean per §0.2),
   give it the project's source-of-truth docs + the relevant gotchas, and have it report back:
   what it built, verification result, commit hash, push status.
4. **Respect the deploy gate.** Default = commit locally, do NOT push `main`. If work is worth
   saving without deploying, push a non-`main` branch. Maintain a running "ready to deploy" list
   per project so the owner can authorize a batch push on return.
5. **Verify before claiming done** — use each project's verify command (§2). Report failures with
   the actual output; never mark something green that didn't pass.
6. **Log owner-action blockers** in one consolidated list (below) so the owner has a single
   to-do list when they return.

**Hand-off / parallelism rules:** disjoint file ownership across delegated agents; integrate
shared files (stores, navs, registries, sitemaps) yourself. Re-run the project's checks after any
merge — agent-written content is often CI-untested.

---

## 4. Consolidated OWNER ACTION LIST (things no agent can do)

Keep this current — it's the owner's return-from-away checklist.

- **Deploy / merge decisions:**
  - **shipsafe `overseer/2026-06-08-p2a`** (1a4c93a): contrast fix (#8a8276→#706860) + aria-current
    nav underline + unified `?v=8` + P2.1a link-purpose check + focus management + aria-expanded FAQ.
    Screenshot-verify `?demo=1`, then merge to main **first** (p2b is based on p2a).
  - **shipsafe `overseer/2026-06-08-p2b`** (a48ea9b, 1 commit): P2.1b ARIA roles validity
    (10th a11y check, WCAG 4.1.2 Level A). Merge p2a first; p2b then fast-forwards cleanly.
  - **shipsafe `overseer/2026-06-08-p2c`** (43b69f2, 1 commit): P2.1c tab-order heuristic
    (11th a11y check, WCAG 2.4.3 Level A). Merge p2a → p2b → p2c in order.
  - **shipsafe `overseer/2026-06-08-p2d`** (a563c41, 1 commit): P2.1d video captions
    (12th a11y check, WCAG 1.2.2 Level A). Merge p2a → p2b → p2c → p2d in order.
  - **shipsafe `overseer/2026-06-08-p2e`** (9a36eb1, 1 commit): P2.1e language-of-parts
    (13th a11y check, WCAG 3.1.2 Level AA). Merge after p2d.
  - **shipsafe `overseer/2026-06-08-p2f`** (1448a1c, 1 commit): P2.1f skip link resolves
    (14th a11y check, WCAG 2.4.1 Level A). Merge p2a → ... → p2e → p2f in order.
  - **shipsafe `overseer/2026-06-08-p2g`** (357e7fe, 1 commit): P2.1g duplicate IDs
    (15th a11y check, WCAG 4.1.1 Level A). Merge p2a → ... → p2f → p2g in order.
  - **shipsafe `overseer/2026-06-08-p2h`** (93df640, 1 commit): P2.1h ARIA required attributes
    (16th a11y check, WCAG 4.1.2 Level A, `fail`). Checks 12 WAI-ARIA roles (checkbox, combobox,
    heading, menuitemcheckbox, menuitemradio, meter, option, radio, scrollbar, slider, spinbutton,
    switch) against their mandatory state/property attributes. Merge after p2g. app.js v=15.
    Owner QA: paste a URL with `<div role="slider">` (no aria-valuenow) → expect fail; a page
    using only semantic HTML → expect info. Demo `?demo=1` returns info (no such roles in sample).
  - **shipsafe `overseer/2026-06-09`** (4 commits — merge after p2h): P2.1i + P2.1j + P2.1k + P2.1l.
    - **P2.1i** (7c39412): Autocomplete on personal-data fields (17th check, WCAG 1.3.5 AA, `warn`). Demo has email input w/ no autocomplete → shows warn.
    - **P2.1j** (3f92ef0): Frames have accessible names (18th check, WCAG 4.1.2 Level A, `fail`). Checks `<iframe>` for `title`; demo includes untitled Google Maps iframe → shows fail. ShipSafe own pages have no `<iframe>` elements → info (no self-fail). app.js v=17.
    - **P2.1k** (5a65686): Viewport zoom restrictions (19th check, WCAG 1.4.4 Level AA, `warn`). Parses viewport meta for `user-scalable=no` or `maximum-scale≤1`. Demo updated with `user-scalable=no` → shows warn. app.js v=18.
    - **P2.1l** (f90a158): Color contrast on inline styles (20th check, WCAG 1.4.3 Level AA, `warn`). Computes WCAG relative-luminance ratio for inline color vs background-color (assumes white if no bg declared). Demo has `<p style="color:#aaa;background-color:#fff">` → shows warn (2.3:1, needs 4.5:1). methodology.html: nineteen→twenty, new table row, updated limitation note. app.js v=19.
    Owner QA: (P2.1j) paste a URL with an untitled `<iframe>` → expect fail. (P2.1k) A site with `user-scalable=no` viewport → expect warn. (P2.1l) A page with inline `style="color:#aaa;background-color:#fff"` on a text element → expect warn (2.3:1). A page with `style="color:#333;background-color:#fff"` → expect pass (12.6:1). `?demo=1` now shows all four checks as expected.
  - **CLOSE shipsafe `overseer/2026-06-08-p1.3`** (62ab2ad): superseded.
  - **CLOSE shipsafe `overseer/2026-06-08-p1.5`** (e4bacc2): superseded.
  - **CLOSE shipsafe `overseer/2026-06-08`** (b8b2535): rejected design, 6 behind main.
  - **marina `phase-3-golive`** (24 ahead vs main) + **`overseer/2026-06-07`** (1 commit, promo
    admin UI) — awaiting merge-to-main decision + Vercel admin-deploy fix.
  - **boots `overseer/2026-06-07`** (superseded by owner's own work) — close it.
  - **apex `overseer/2026-06-07`** (fa1742b, coverage uplift): now ~46 commits behind main after
    realignment commit — needs rebase before it can merge.
  - **apex `overseer/2026-06-08`** (tip `b202e1d`, F2.3 + walk-forward + docs): also ~46 behind —
    needs rebase before merging.
  - **CLOSE apex `feat/risk-hardening`**: already merged to main.
  - **apex `feat/research-buildout`** (22 ahead / ~55+ behind after realignment): heavily
    diverged; rebase/squash-merge or close decision needed.
  - **apex `feat/status-export`** (~1 ahead / ~55+ behind): almost certainly obsolete — close.
- **Duke (verify deploy):** PRs #33 + #34 merged today — confirm Vercel picked up the deep-enriched
  CRM fields (grade pills, deliverability badges, website quality chips, score_why breakdown).
- **Boots:** apply Supabase migrations 0005/0006/0007 live; set Vercel secrets (SERVICE_ROLE,
  STRIPE_*, RESEND, VAPID, CRON_SECRET, GITHUB_APP_*, SENTRY_DSN).
- **Duke (growth levers):** GBP as service-area business; real reviews + GOOGLE_REVIEW_URL;
  founder headshot + LinkedIn; Vercel env (CRM_ADMIN_TOKEN, GALLERY_BASE_URL,
  GITHUB_WEBHOOK_SECRET, OUTREACH_DOMAIN_VERIFIED_DATE, Web Analytics); **set UPSTASH_REDIS_REST_URL +
  TOKEN + RESEND_API_KEY in Vercel prod** (root cause of form hangs — the withTimeout fix degrades
  gracefully but the real fix is the env vars); verify Resend domain.
- **Websites:** set SITE_URL / GALLERY_BASE_URL on Vercel; decide on deploy; run a fresh CSV batch.
- **Marina:** Clerk SECRET key; Stripe test keys (switched from Square — D-013); diagnose marina-admin
  Vercel deploy failure; per-tenant billing; legal/ToS. Merge `phase-3-golive` + `overseer/2026-06-07`
  onto main (trivial sidebar conflict: combine Resources/Gift Cards/Promos nav items).
- **Apex Quant:** let the 30-day paper gate run; flip to live only after Sharpe holds; decide
  whether to fund a survivorship-free dataset for the value edge (needed for F3.3 live sleeve).
  Rebase + merge `overseer/2026-06-07` + `overseer/2026-06-08` (both now ~46 behind, need rebase).
  Close stale branches: `feat/risk-hardening` (merged), `feat/status-export`, review `feat/research-buildout`.
- **ShipSafe:** P1.9 real domain (`shipsafe.app` or `copperbaylabs.com` subdomain); custom-domain
  GitHub Pages deploy. After domain: P4.2 launch (Product Hunt / Show HN) — only after Phase 1 clears.
- **Sonoma scraper:** local main is 24 behind origin — pull/sync.
