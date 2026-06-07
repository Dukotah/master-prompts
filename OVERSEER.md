# OVERSEER — Master Project Tracker & Coordination Brief

**Owner:** Dukotah (GitHub `Dukotah`) · **Maintainer of this file:** the overseer agent
**Purpose:** single source of truth for an overseer/coordinator agent that keeps work moving
across all projects while the owner is away. Update the per-project STATE lines whenever you
(or a delegated agent) finish a chunk of work.

> Last reconciled: 2026-06-07 (20:08 UTC). Verify git state before acting — these notes go stale fast
> because cloud agents push to `origin/main` mid-session on several repos.

**Automation:** a daily remote routine runs this brief. Routine `trig_014XPBhL62SX3vh5qei8oNPe`
("Overseer — daily project coordinator"), 06:00 PDT / 13:00 UTC daily, Sonnet, clones all 5 active
repos + master-prompts. It reconciles state, advances ONE project per run (rotating, UTC day-of-year
mod 5), pushes work to `overseer/<UTC-date>` branches (NEVER main), and pushes its updated brief +
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

| Project | Repo | Local | Tier | Deploy on push? | Live git state (2026-06-07) |
|---|---|---|---|---|---|
| **Boots / Cantrip** | `Dukotah/boots` | `~/boots` | 🟢 Active flagship | Yes (main) | `main` synced at 76a501f (pair streaks + guild boss); `qa-fixes-2026-06-07` merged; **overseer/2026-06-07** (+1 commit: analytics instrumentation, awaiting owner merge) |
| **Websites factory** | `Dukotah/Websites` | `~/websites` | 🟢 Active | Yes (main) | `main` **4 ahead / 1 behind**, clean — fetch+rebase before push |
| **Duke / Copper Bay Tech** | `Dukotah/Duke` | `~/duke` | 🟢 Active | Yes (main) | `main` synced (pushed), 4 untracked docs |
| **Marina booking SaaS** | `Dukotah/marina-booking-platform` | `~/marina-booking-platform` | 🟢 Active | Yes (main) | branch `phase-3-golive`, **24 unpushed** (stacked p1/p2/p3), clean · **overseer/2026-06-07** (+1 commit: promo admin page, awaiting owner merge) |
| **Apex Quant** | `Dukotah/apex-quant` | `~/apex-quant` | 🟢 Active | No (CI cron only) | `main` synced; `feat/research-buildout` **20 ahead** of main; `feat/risk-hardening` + `feat/status-export` each **25 ahead** — owner should review/merge or close |
| **Sonoma lead scraper** | `Dukotah/sonoma-lead-scraper` | `~/sonoma-lead-scraper` | 🟡 Supporting | No | `main` **24 BEHIND origin**, 3 dirty — local is stale |
| **Master prompts** | `Dukotah/master-prompts` | `~/master-prompts` | 🟡 Supporting | No | `main` **2 unpushed**, clean |
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
- **State:** huge surface shipped & live. `qa-fixes-2026-06-07` merged → main. Main at
  76a501f includes pair streaks (#8), guild co-op boss (#20), league segmentation (#16),
  interleaved practice (#10), double-XP weekend (#15), streak-decay soft-reset (DONE),
  FSRS spaced-rep (already in store). 139 modules / 979 lessons / 377 unit tests / 1747
  curriculum tests. **overseer/2026-06-07** adds lesson_started + streak_milestone analytics
  (awaiting owner merge). Many cloud branches in-flight: feat/courses-and-lesson-ux,
  feat/gamification-expansion, feat/systems-tracks — avoid colliding files.
- **Gotchas:** `check-curriculum.ts` runs starter stubs AND solutions with NO timeout — an
  unbounded loop in a stub or a drain-loop test HANGS forever. JS runner = Web Worker (no
  window/localStorage). Cloud agents push to `origin/main` AND open PRs — fetch+rebase before push.
- **Owner-action blockers (can't be done without secrets):** apply Supabase migrations 0005/0006/0007
  live; set Vercel env (SERVICE_ROLE, STRIPE_*, RESEND, VAPID, CRON_SECRET, GITHUB_APP_*, SENTRY_DSN).
- **Verify:** `npx tsc --noEmit` · `npm run check` · `npm test` · `npm run build`.
- **Next buildable:** goal-gradient nudge at 85% to level-up (E:L — pure UI in XPBar/dashboard,
  no secrets); variable-reward boss loot chest (E:M); comparison/alternative-to SEO pages (E:L
  content); XP double-weekend events (already shipped per main). trial-expiration urgency emails
  (blocked: needs RESEND secret). Backlog in `docs/STRATEGY-RESEARCH-2026-06.md`.

### 🟢 Websites — outreach demo-site factory (Astro)
- **What:** mass-generates per-business demo sites at `/p/<slug>` (one Vercel deploy hosts all
  demos in `sites/demo-gallery/`) for cold outreach. `npm run generate-prospects -- data/<file>.csv`.
- **State:** v2 design system live; deep anti-"AI-slop" framework (per-business fonts/palettes/
  shape, OKLab color, section-variant pool, real-photo scraping + Sharp photo-quality scorer,
  astro:assets image pipeline, vision-QA harness, outreach funnel w/ claim banner + noindex gate).
  `main` is **4 ahead / 1 behind** origin — owner has parked the deploy.
- **Gotchas:** quality must be checked by **screenshot** (headless Chrome `--headless=new
  --screenshot` vs `npm run preview`, auto-picks free port ~4322). Undefined `var(--token)` =
  silent invisible text, build stays green. A shared image component breaks parents' scoped `img`
  CSS — `SiteImage` owns fill/zoom. Build/preview from `sites/demo-gallery`. DON'T re-scrape
  curated grade-A demos to "enrich" — it regresses heroes (learned the hard way).
- **Owner-action:** set `SITE_URL`/`GALLERY_BASE_URL` on Vercel; push/deploy decision.
- **Recently built (local, UNPUSHED):** new `FaqAccordion.astro` FAQ section variant (commit
  `c143d77`, build + audit clean; visual not yet eyeballed) — 2026-06-07, awaiting batch push.
- **Next buildable:** run a fresh CSV batch through the upgraded pipeline (roster ~10); mine
  CodeStitch for MORE section variants; before/after slider; OKLab hue math.

### 🟢 Duke — Copper Bay Tech (own agency site, copperbaytech.com)
- **Stack:** Next.js 16 + Tailwind v4. Sonoma County web/IT/cyber/AI for small businesses.
  `@/config/site` + `@/config/pricing` are single sources of truth. Has a full CRM (`/crm`,
  Upstash Redis, db.ts), 30+ blog posts, large service×city page matrix, 4 free-tool lead magnets.
- **State:** `main` synced & deployed (origin/main `f161e0e`-era). Service×city matrix ~complete
  for all meaningful Sonoma County towns. Lead capture wired: contact form + all 4 tools →
  `intake.ts` → Upstash CRM, with lead-source attribution.
- **Gotchas:** ⚠️ historically the local checkout is a stale branch — **always fetch origin/main
  first**. One CRM only now (the old power-dialer was deleted/reconciled). Lint flags `Date.now()`
  in component scope — use a module-level helper.
- **Owner-action (the real growth levers — only the owner can do these):** Google Business Profile
  as a service-area business; collect real reviews + set `GOOGLE_REVIEW_URL`; founder headshot +
  LinkedIn URL; Vercel env (`CRM_ADMIN_TOKEN`, `GALLERY_BASE_URL`, `GITHUB_WEBHOOK_SECRET`,
  `OUTREACH_DOMAIN_VERIFIED_DATE`, flip on Web Analytics); verify Resend sending domain before any
  cold email.
- **Verify:** `tsc` + `eslint` + `vitest` (~132 tests) + `next build`.
- **Recently built (local, UNPUSHED):** Blog TOC + reading time (`BlogTOC.tsx` + `blog/layout.tsx`,
  commit `bcb33d6`, tsc/lint/132 tests/build all green) — 2026-06-07, awaiting batch push.
- **Next buildable (no owner dep):** ROADMAP "Next 100" leftovers — inline estimate widget,
  comparison posts, dialer shortcuts, Cmd-K, image-loading audit. (blog TOC = DONE, above)

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

### 🟢 Apex Quant — algo-trading framework (Python)
- **What:** event-driven, asset-agnostic trading framework with a 7-gate validation Gauntlet.
  Strategies emit `SignalEvent` only; `RiskManager` is the sole `OrderEvent` producer. Its own
  `CLAUDE.md/DECISIONS.md/ROADMAP.md/SESSION_PLAYBOOK.md` are the source of truth — **read first**.
- **State:** build COMPLETE (Phases 1–6). ~416 tests, CI green on `main`. The **multi-asset trend
  strategy is DEPLOYED LIVE ON PAPER** via a GitHub Actions weekday cron (commits state back each
  run → `git pull` before local work). Currently on branch `feat/research-buildout` (**4 unpushed**,
  8 dirty) chasing a 2nd edge: single-name cross-asset VALUE passes the Gauntlet but the universe is
  SURVIVORSHIP-BIASED → strong candidate, NOT deployable.
- **Workflow rule:** normally **one module per session, tested, then stop**; built files are FROZEN.
  Run `ruff format --check` locally before pushing (CI enforces it).
- **Owner-action / time-gated:** run out the 30-day paper gate (`python -m scripts.report`); only
  flip `APEX_MODE=live` after Sharpe holds.
- **Next buildable:** re-validate the value edge on point-in-time / survivorship-free constituents;
  if it holds, build the deferred multi-strategy allocation engine (trend + value).
- **Sibling:** `apex-trader` (private Next.js control surface) is a separate repo.

### 🟡 Supporting
- **sonoma-lead-scraper** (Python) — scrapes Sonoma County business leads → CSV → feeds duke CRM.
  Local `main` is **24 commits BEHIND origin** (stale) + 3 dirty. The duke CRM reads a *different*
  branch's export (`claude/lead-data-sourcing`). Sync local before working here.
- **master-prompts** — curated prompt library for Claude Code / agents. `main` **2 unpushed**, clean.
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

- **Deploy / merge decisions:** ✅ duke + websites PUSHED to main 2026-06-07 (prod deployed, both
  verified live 200). marina `phase-3-golive` (+10 vs main) + `overseer/2026-06-07` (1 commit,
  promo admin UI) both on `origin` (preview only) — awaiting your merge-to-main decision + Vercel
  admin-deploy fix. **boots `overseer/2026-06-07`** (+1 commit: lesson_started + streak_milestone
  analytics) — safe to merge any time. apex `feat/research-buildout` (20 ahead), `feat/risk-hardening`
  (25 ahead), `feat/status-export` (25 ahead) on origin — review and merge or close; risk-hardening
  and status-export have significant work that may be worth landing.
- **Boots:** apply Supabase migrations 0005/0006/0007 live; set Vercel secrets (SERVICE_ROLE,
  STRIPE_*, RESEND, VAPID, CRON_SECRET, GITHUB_APP_*, SENTRY_DSN).
- **Duke (growth levers):** GBP as service-area business; real reviews + GOOGLE_REVIEW_URL;
  founder headshot + LinkedIn; Vercel env (CRM_ADMIN_TOKEN, GALLERY_BASE_URL,
  GITHUB_WEBHOOK_SECRET, OUTREACH_DOMAIN_VERIFIED_DATE, Web Analytics); verify Resend domain.
- **Websites:** set SITE_URL / GALLERY_BASE_URL on Vercel; decide on deploy; run a fresh CSV batch.
- **Marina:** Clerk SECRET key; Stripe test keys (switched from Square — D-013); diagnose marina-admin
  Vercel deploy failure; per-tenant billing; legal/ToS. Merge `phase-3-golive` + `overseer/2026-06-07`
  onto main (trivial sidebar conflict: combine Resources/Gift Cards/Promos nav items).
- **Apex Quant:** let the 30-day paper gate run; flip to live only after Sharpe holds; decide
  whether to fund a survivorship-free dataset for the value edge.
- **Sonoma scraper:** local main is 24 behind origin — pull/sync.
