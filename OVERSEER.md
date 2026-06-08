# OVERSEER — Master Project Tracker & Coordination Brief

**Owner:** Dukotah (GitHub `Dukotah`) · **Maintainer of this file:** the overseer agent
**Purpose:** single source of truth for an overseer/coordinator agent that keeps work moving
across all projects while the owner is away. Update the per-project STATE lines whenever you
(or a delegated agent) finish a chunk of work.

> Last reconciled: 2026-06-08 (Run C). Verify git state before acting — these notes go stale fast
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
   integrate shared files (store/nav/index/registries) by hand to avoid parallel-edit conflicts.
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

| Project | Repo | Local | Tier | Deploy on push? | Live git state (2026-06-08 Run C) |
|---|---|---|---|---|---|
| **Boots / Cantrip** | `Dukotah/boots` | `~/boots` | 🟢 Active flagship | Yes (main) | `main` at affb7296 (Skill Tree R2, career R2, /projects SSG, a11y, perf — June 7 23:42 UTC; 151 modules/1039 lessons/377 unit tests); **overseer/2026-06-07** 2fec499 now 4 commits **behind** main (owner surpassed it — may be superseded); `feat/track-boss-fights` open |
| **Websites factory** | `Dukotah/Websites` | `~/websites` | 🟢 Active | Yes (main) | `main` at 826c8f3b (June 7 23:37 UTC; 4 fresh real-scrape demos); prospect branches: `driftwood-cowgirl-boutique`, `pecota-vineyard` |
| **Duke / Copper Bay Tech** | `Dukotah/Duke` | `~/duke` | 🟢 Active | Yes (main) | `main` at 1ff75fd9 (blog editorial redesign + form timeout fixes, June 7 23:02 UTC); `feat/hardening-sprint1`, `feat/website-links-to-crm` pending review |
| **Marina booking SaaS** | `Dukotah/marina-booking-platform` | `~/marina-booking-platform` | 🟢 Active | Yes (main) | `main` at 6ac5ed2 (unchanged); `phase-3-golive` **24 ahead**; `overseer/2026-06-07` **1 ahead** (promo admin page); `feat/finish-mvp-buildable` **6 ahead** — owner merge decision needed |
| **Apex Quant** | `Dukotah/apex-quant` | `~/apex-quant` | 🟢 Active | No (CI cron only) | `main` at 3b9bd7c0 (GitHub Pages dashboard polish + ruff sort, June 7 23:52 UTC); F3.3 allocation engine ✅ DONE (in main); **overseer/2026-06-07** fa1742b (coverage uplift) + **overseer/2026-06-08** d6013a0 (F2.3 heartbeat) awaiting owner merge; `feat/research-buildout` diverged (22 ahead / 15 behind) |
| **ShipSafe** | `Dukotah/shipsafe` | `~/shipsafe` | 🟢 Active (Copper Bay Labs) | GitHub Pages (free, on main) | `main` at b2f2c707 (v1 + ROADMAP, June 7 23:54 UTC); **overseer/2026-06-08** b8b2535 (+1: P1.1+P1.2 brand identity + SVG icons) — awaiting screenshot-verify + owner merge |
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
  1971 curriculum tests (June 7 2026). Latest additions to main: Skill Tree R2 (talent toasts +
  first-visit walkthrough), career R2 (/career/resume + /career/mock-interview, Pro-gated),
  /projects/[slug] SSG detail pages, a11y contrast fixes, first-load perf (2.6MB curriculum chunk
  now only loads on /review, not all 34 routes; /learn 1.03MB→70KB). All 20 strategy-backlog items
  from `docs/STRATEGY-RESEARCH-2026-06.md` are DONE. `overseer/2026-06-07` branch (+2: analytics
  instrumentation + DailyChallenge P1.6b tests) is now 4 commits behind main — may be superseded
  by owner's own work; owner should review.
- **Gotchas:** `check-curriculum.ts` runs starter stubs AND solutions with NO timeout — an
  unbounded loop in a stub or a drain-loop test HANGS forever. JS runner = Web Worker (no
  window/localStorage). Cloud agents push to `origin/main` AND open PRs — fetch+rebase before push.
- **Owner-action blockers (can't be done without secrets):** apply Supabase migrations 0005/0006/0007
  live; set Vercel env (SERVICE_ROLE, STRIPE_*, RESEND, VAPID, CRON_SECRET, GITHUB_APP_*, SENTRY_DSN).
- **Verify:** `npx tsc --noEmit` · `npm run check` · `npm test` · `npm run build`.
- **Next buildable:** P1.7 — verify loading/empty/error states on primary routes (dashboard, lesson,
  learn, leaderboard, account, teams) — find routes that don't handle these states and add guards.
  P1.2 (bundle analyzer + first-load investigation) may already be done per latest commits — verify TASKS.md.
  trial-expiration urgency emails (blocked: needs RESEND secret).

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
- **State:** `main` at 1ff75fd9 — blog editorial redesign (featured card + category filter + grid +
  shared ArticleHeader with contrast-fixed byline/date), form timeout fixes (withTimeout helper,
  15s client AbortSignal, /contact 308 redirect), Resend email-event ingestion + drip cron, CI
  pipeline + Playwright E2E smoke suite all on main. Service×city matrix complete. Lead capture
  fully wired. In-flight: `feat/hardening-sprint1`, `feat/website-links-to-crm`.
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
- **What:** event-driven, asset-agnostic trading framework with a 7-gate validation Gauntlet.
  Strategies emit `SignalEvent` only; `RiskManager` is the sole `OrderEvent` producer. Its own
  `CLAUDE.md/DECISIONS.md/ROADMAP.md/SESSION_PLAYBOOK.md` are the source of truth — **read first**.
- **State:** build COMPLETE (Phases 1–6 + F1 + F2 + F3). F3.3 allocation engine ✅ DONE.
  `Bar.__post_init__` invariant ✅ DONE. Coverage uplift ✅ DONE (`overseer/2026-06-07`: backtester
  62→100%, config 79→100%, base_strategy 78→96%). F2.3 heartbeat ✅ DONE. Gate-3 walk-forward
  efficiency ✅ CLOSED (34a32bb: `_MIN_IS_SHARPE` guard + ROADMAP checked off, 3074 tests).
  **Multi-asset trend strategy LIVE ON PAPER** via GitHub Actions. Owner pushed 3 web app commits
  (feature-tour + layperson redesigns). Main now at `7a9e4c76`.
  **overseer/2026-06-07** (coverage) + **overseer/2026-06-08** (F2.3 + walk-forward) awaiting merge.
- **Workflow rule:** normally **one module per session, tested, then stop**; built files are FROZEN.
  Run `ruff format --check` locally before pushing (CI enforces it).
- **Owner-action / time-gated:** run out the 30-day paper gate (`python -m scripts.report`); only
  flip `APEX_MODE=live` after Sharpe holds. Decide on paid delisted-data source to unlock W8/F3.3
  live sleeve. Merge `overseer/2026-06-07` + `overseer/2026-06-08` when convenient.
- **Next buildable:** README quickstart for a cold-start operator. OR: any remaining open
  items in apex-quant ROADMAP.md (Gate-3 efficiency + Makefile already closed).
- **Sibling:** `apex-trader` (private Next.js control surface) is a separate repo.

### 🟢 ShipSafe — Copper Bay Labs product #1 (from the forge factory)
- **What:** standalone static web app (vanilla HTML/CSS/JS, GitHub Pages, $0). Paste a URL → ~20
  ADA/WCAG + privacy + schema checks → plain-English "demand-letter risk" report. Live at
  `dukotah.github.io/shipsafe/`. Deliberately kept STANDALONE (not folded into copperbaytech.com) so
  it grows its own brand/audience + a self-serve Pro tier; the agency benefits later via a one-way
  lead bridge.
- **The mandate:** make it NOT look AI-built — credibility is the product. Its own roadmap
  (`~/shipsafe/ROADMAP.md`) is the source of truth: Phase 1 = de-slop. Work phases top-down.
- **State:** v1 launched June 7. ROADMAP and de-slop plan written. **P1.1 + P1.2 DONE on
  `overseer/2026-06-08`** (commit b8b2535): brand identity (maritime navy #0d4f6e, Plus Jakarta
  Sans via Bunny Fonts, two-tone ShipSafe wordmark) + full SVG icon set (4 Lucide-style icons
  replacing all emoji). Self-exemplary: privacy + contact checks now pass on ShipSafe's own scan.
  **Screenshot verification NOT done by agent** (cloud env lacks headless Chrome) — owner must
  verify visually before merging to main.
- **Verify method:** SCREENSHOT every UI change in headless Chrome (never trust build-success),
  AND the tool must keep passing its own accessibility engine.
- **Deploy:** GitHub Pages from `main` (FREE — no Vercel quota). Push work to `overseer/<date>`
  branches; merging to main to deploy is low-risk, owner's call.
- **Next buildable:** P1.3 report redesign (severity hierarchy, letter grade A–F, calm-but-serious
  visual tone; make the report the thing people screenshot). **After** owner merges P1.1+P1.2.
  Also remaining in P1: P1.5 (self-exemplary a11y — partially addressed; still needs ToS + a11y
  statement), P1.6 (human copy pass), P1.7 (trust depth: /methodology + /about + limitations).

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
  - **shipsafe `overseer/2026-06-08`** (b8b2535): brand identity + SVG icons — **screenshot-verify** before merging (ROADMAP requirement). GitHub Pages deploy is free, low-risk.
  - **marina `phase-3-golive`** (24 ahead vs main) + **`overseer/2026-06-07`** (1 commit, promo admin UI) — awaiting merge-to-main decision + Vercel admin-deploy fix.
  - **boots `overseer/2026-06-07`** (2fec499, 4 commits BEHIND current main): owner's own work has surpassed this branch. Review whether analytics instrumentation + DailyChallenge tests are still needed or already covered.
  - **apex `overseer/2026-06-07`** (fa1742b, coverage uplift) + **`overseer/2026-06-08`** (d6013a0, F2.3 heartbeat): safe to merge any time.
  - **apex `feat/research-buildout`** (22 ahead / 15 behind): diverged; rebase/squash-merge decision needed.
  - **apex `feat/risk-hardening`** + **`feat/status-export`** (each 1 ahead / 40 behind): almost certainly obsolete — close these branches.
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
- **ShipSafe:** P1.9 real domain (`shipsafe.app` or `copperbaylabs.com` subdomain); custom-domain
  GitHub Pages deploy. After domain: P4.2 launch (Product Hunt / Show HN) — only after Phase 1 clears.
- **Sonoma scraper:** local main is 24 behind origin — pull/sync.
