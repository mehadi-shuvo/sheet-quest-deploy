# SheetQuest — 45-Day Build Plan & Codex Operating Manual

> **Single source of truth for development, QA, and progress.** This document is written for a solo developer using Codex. Codex must **read it before starting any task and update it after every task attempt**, including partial progress, blockers, failed tests, and changes in scope.
>
> **Product:** Career-focused, browser-based Excel/spreadsheet learning SaaS for students, job seekers, and office professionals. **AI tutor is explicitly out of scope.**
>
> **Deadline:** 45 calendar days from the date development officially begins. Record that date in the Project Control table. Do not claim the entire 96-lesson vision can be built in 45 days. Ship a reliable, paid-ready MVP or a controlled beta if payment-provider onboarding is blocked.

## 0. Project control — update on every Codex run

| Field                | Current value                                                                                                                            |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Project              | SheetQuest                                                                                                                               |
| Status               | `COMPLETE — Pre-M03`                                                                                                                     |
| Start date           | `2026-11-04`                                                                                                                             |
| Target date          | `2026-12-18`                                                                                                                             |
| Current milestone    | `Pre-M03 — Database & Platform Foundation (complete)` |
| Current task         | `None — M03 requires explicit authorization` |
| Last verified result | `Pre-M03 exit gate passed: schema/migrations, seeded content, learner persistence, recovery drill, and focused CI-equivalent tests are verified.` |
| Last update          | `2026-11-17 — PRE-M03-T10 completed; M03 was not started.` |
| Next action          | `Do not start M03 without explicit authorization.` |
| Known blockers       | `No M02 blocker. Live Compose gateway smoke was not run because no local services were running. Submission rate limiting remains an M05 security requirement.` |

**Status vocabulary:** `PLANNED`, `TODO`, `IN_PROGRESS`, `PARTIAL`, `BLOCKED`, `DONE`, `DEFERRED`. A task is `DONE` **only after** its acceptance criteria and required checks pass. A file existing is not proof a feature works.

### Codex mandatory execution contract

Codex, follow these instructions on **every** task, even if the human only says “continue”:

1. **Read this entire `deploy/README.md`** and relevant source files before changing code. Inspect Git status; never overwrite unrelated changes.
2. Find the **first incomplete, unblocked task** in the milestone tables. Prefer finishing `PARTIAL` or `IN_PROGRESS` tasks before starting new tasks. If the user explicitly requests a different task, do that task and update dependencies/status honestly.
3. Before coding, state a brief plan: files affected, intended behavior, acceptance criteria, and commands/tests to run.
4. Implement the **smallest working vertical slice**. Keep frontend and backend contracts aligned. Add migrations and tests for changes that need them.
5. Run applicable checks. Record **exact commands and outcomes** in the Execution Log; do not invent successful test results. If Docker, network, credentials, or a payment provider are unavailable, mark verification `NOT RUN` and explain why.
6. **Immediately update this master README at the end of every task attempt**, even when only partially completed, blocked, or unsuccessful. Update: Project Control, milestone task status, Execution Log, Decision Log (if architecture changed), and Next Action. Record files changed and remaining work.
7. If a task spans multiple Codex sessions, preserve its `PARTIAL` state with a precise resume checklist. Never silently mark it `DONE`.
8. Never claim deployment, security, payment readiness, or production scalability without evidence. Do not commit secrets, `.env`, tokens, or real customer data.
9. If changing this README, **preserve its task IDs, existing history, and previously recorded evidence**. Append to logs instead of deleting past entries. You may refine tasks, but explain scope changes in the Decision Log.
10. Finish each response with: **Completed / Partial / Blocked / Tests / README updated / Next task**. If the README could not be written, explicitly say so and provide the intended patch.

**Non-negotiable rule:** Code + tests + documentation/status update = one task. No task is finished without all three, unless the task is explicitly documentation-only.

### Copy-paste prompt for Codex (first run or any new session)

```text
You are the implementation engineer for SheetQuest. Read `deploy/README.md` in full and inspect the existing repository/repositories before acting. Follow the "Codex mandatory execution contract" exactly. Continue from the first incomplete unblocked task in the roadmap; do not restart or rewrite completed work. Work on one task or tightly related vertical slice at a time. Run appropriate tests, record real evidence, and UPDATE `deploy/README.md` at the end of this run even if work is partial, blocked, or failed. Never mark DONE without satisfying acceptance criteria. Report Completed / Partial / Blocked / Tests / README updated / Next task. Do not add AI features, monorepo tooling, microservices, Kubernetes, or unnecessary infrastructure. Preserve user code and data.
```

### When multiple repositories are used

This is **not a monorepo**. The current downloaded starter contains three sibling directories, but they are independent projects, not a workspace:

```text
sheetquest-starter/                 # local parent folder only
├── sheetquest-web/                 # own package.json, ideally own Git repo
├── sheetquest-api/                 # own package.json, ideally own Git repo
└── deploy/                         # Docker Compose, private deployment repo, and canonical README.md
```

- `sheetquest-web`: Next.js, TypeScript, UI, browser workbook, frontend tests.
- `sheetquest-api`: NestJS, Prisma, PostgreSQL, auth, curriculum, attempts, progress, billing, API tests.
- `deploy`: Docker Compose, configuration examples, operational scripts, deployment instructions.
- **Canonical roadmap:** this file is `deploy/README.md`, tracked in the private deployment repository. Update only this copy. The web/API READMEs direct contributors here. If Codex opens only one repository, make this roadmap accessible before starting work.
- API is the contract authority. Maintain OpenAPI docs and generated frontend API types when practical; do not manually fork incompatible DTO definitions.
- Coordinated feature changes may require separate commits in web and API; log both commit hashes if available.

---

## 1. MVP definition and strict scope

### User-facing MVP (target by day 45)

- Account registration/login/logout and protected learner routes.
- Goal-based onboarding: student, job seeker, office professional; level and daily goal.
- Courses → modules → lessons navigation and a simple dashboard.
- Browser spreadsheet exercises: cell editing, supported formulas, starter data, reset, submission, contextual **rule-based** feedback.
- Save/resume workbook state; track attempts, completion, mastery, and XP.
- **Five free lessons**, **15–20 total lessons** if engine quality allows; at least one mini project.
- Paywall and subscription entitlements. Real checkout **only if an eligible, legally operable provider is approved**; otherwise a clearly labeled controlled beta without pretending payments work.
- Minimal admin content workflow (versioned JSON/seed content is acceptable at first; full visual CMS is not required).
- Responsive learner navigation, error states, basic analytics, security, automated tests, deploy instructions.

### Explicitly out of scope for the 45-day MVP

AI tutor, AI-generated content, community forum, full 96-lesson catalog, all three large capstones, full Excel parity, Power Query execution, VBA/macros, advanced PivotTables, mobile app, multiplayer editing, organizations/teams, microservices, Kubernetes, complex distributed queues. Certificates and sophisticated gamification are post-MVP unless ahead of schedule.

### Quality bar

- A learner can sign in, complete an exercise, receive correct feedback, reload, and see preserved progress.
- An equivalent valid formula should not be rejected solely because its text differs from a reference formula.
- For formula-learning tasks, hardcoding a correct result must **not** count as demonstrating the required formula skill.
- No private answer keys or hidden assessment data in public browser bundles.
- Auth, ownership, paid access, and admin authorization enforced **server-side**.
- Migrations are version-controlled; repeatable deployment; no accidental destructive DB reset.
- Key flows covered by automated tests; errors logged without secrets.

---

## 2. Baseline: existing starter (do not rebuild blindly)

The previously supplied Docker starter contains:

- `sheetquest-web`: Next.js app, standalone Docker build, `/api/health`, `/backend/*` gateway.
- `sheetquest-api`: NestJS API, `/health/ready`, Prisma 6.x, initial `User` model and migration.
- `deploy/compose.yaml`: PostgreSQL 17, one-off `migrate`, API, web, private network and localhost-bound ports.

**Historical issue:** original Prisma schema put `generator` and `datasource` blocks on single lines and failed `P1012`. A corrected archive was provided. **Do not assume the user's current checkout already contains the fix.** Inspect `sheetquest-api/prisma/schema.prisma` and confirm correct multi-line blocks.

### Baseline runbook (from parent directory)

```bash
cd deploy
cp -n .env.example .env
# Set a strong POSTGRES_PASSWORD; keep .env untracked.
docker compose config --quiet
docker compose build api migrate web
docker compose up -d
docker compose ps
docker compose logs migrate --tail=80
docker compose logs api --tail=80
curl -fsS http://localhost:3000/api/health
curl -fsS http://localhost:4000/health/ready
curl -fsS http://localhost:3000/backend/health/ready
```

Expected: web/API builds pass; migration exits successfully; DB/API/web services run; health checks return 2xx. **If not, capture exact error and set `M00-T01` to `PARTIAL` or `BLOCKED`.** Do not run `docker compose down -v` on a DB with valuable data.

### M00 operational guide

#### Architecture and request flow

```text
Browser ── HTTPS / reverse proxy ──> Next.js web (port 3000)
                                      └─ /backend/* ──> NestJS API (port 4000, private Docker network)
                                                           └─ Prisma ──> PostgreSQL 17 (private Docker network)
```

- The web app exposes `/api/health` itself. Browser API calls use the same-origin `/backend/*` gateway; it forwards selected request headers to the API and never exposes the Docker-internal API hostname to the browser.
- The API exposes unversioned infrastructure probes at `/health/live` and `/health/ready`; all other API routes use `/v1`.
- The one-off `migrate` service runs `prisma migrate deploy` after PostgreSQL is healthy. The API starts only after that service completes successfully.
- PostgreSQL has no public Compose port. API and web ports bind to `127.0.0.1`, so production requires a TLS reverse proxy on the same host. Do not expose PostgreSQL publicly.

#### Environment-variable contract

| Location | Variable | Required | Purpose / safe local value |
| --- | --- | --- | --- |
| `deploy/.env` | `POSTGRES_USER` | Yes | Database role; use `sheetquest` locally. |
| `deploy/.env` | `POSTGRES_PASSWORD` | Yes | Database password. Generate a long random value; never commit this file. |
| `deploy/.env` | `POSTGRES_DB` | Yes | Database name; use `sheetquest` locally. |
| `deploy/.env` | `API_PORT` | No | Host loopback port for API; defaults to `4000`. |
| `deploy/.env` | `WEB_PORT` | No | Host loopback port for web; defaults to `3000`. |
| API runtime | `DATABASE_URL` | Yes | Compose constructs it from the PostgreSQL values using hostname `db`; a host-run API needs a reachable PostgreSQL URL instead. |
| API runtime | `NODE_ENV` | No | `development`, `test`, or `production`; Compose sets `production`. |
| API runtime | `PORT` | No | API listening port; Compose sets `4000`. |
| Web runtime | `API_INTERNAL_URL` | Yes | Server-side API URL; Compose sets `http://api:4000`. Never prefix it with `NEXT_PUBLIC_`. |

Create local deployment settings once, then edit only the untracked copy:

```bash
cd deploy
cp -n .env.example .env
# Replace POSTGRES_PASSWORD in .env with a strong local-only secret.
```

The app-specific `.env.example` files document direct-process development. They must contain placeholders only. A direct API process cannot reach Compose's private `db` hostname; point `DATABASE_URL` to a separately reachable local PostgreSQL instance, or run the API through Compose.

#### Local workflow

From `deploy/`, use the Compose stack for the normal integrated environment:

```bash
docker compose config --quiet
docker compose build api migrate web
docker compose up -d
docker compose ps
docker compose logs migrate --tail=80
curl -fsS http://127.0.0.1:3000/api/health
curl -fsS http://127.0.0.1:4000/health/ready
curl -fsS http://127.0.0.1:3000/backend/health/ready
```

For application-only work, install exactly from the lockfile and run the checks in each repository:

```bash
cd sheetquest-api
npm ci --no-audit --no-fund
npm run prisma:generate
npm run lint && npm run typecheck && npm run build

cd ../sheetquest-web
npm ci --no-audit --no-fund
npm run lint && npm run typecheck && npm run build
```

Use `docker compose down` only to stop local services. It preserves the named PostgreSQL volume. Do not add `-v` unless intentionally discarding local data after a backup.

#### Development Compose workflow

`compose.yaml` remains the production-shaped configuration. Use the separate `compose.dev.yaml` for integrated development; it keeps the same service names, private network, localhost-only API/web ports, gateway routing, and `postgres_data` named volume. Do not run the production and development files at the same time: they intentionally share the same Compose project, ports, and database volume.

From `deploy/`, first build the development targets, start PostgreSQL, run migrations explicitly, then start the watch services:

```bash
docker compose -f compose.dev.yaml config --quiet
docker compose -f compose.dev.yaml build
docker compose -f compose.dev.yaml up -d db
docker compose -f compose.dev.yaml --profile migrations run --rm migrate
docker compose -f compose.dev.yaml up -d api web
docker compose -f compose.dev.yaml ps
```

For later starts, use `docker compose -f compose.dev.yaml up -d`; normal `.ts` and `.tsx` edits are bind-mounted, so Next development mode applies Fast Refresh and Nest's `--watch` recompiles/restarts the API without rebuilding an image. The API/web `node_modules` volumes protect container dependencies from the host mounts, and `.next` remains in the `web_next` container volume. Linux bind mounts use normal file notifications; polling is not configured.

Run migrations only when intentionally applying reviewed migrations:

```bash
docker compose -f compose.dev.yaml --profile migrations run --rm migrate
```

Stop the development stack with `docker compose -f compose.dev.yaml down`; it preserves `postgres_data`. Rebuild only for dependency or lockfile changes, Dockerfile/base-image changes, or relevant native-module changes. After stopping the stack for a dependency change, also remove only the affected development dependency volume before rebuilding, for example `docker volume rm sheetquest_web_node_modules` or `docker volume rm sheetquest_api_node_modules`; do not remove `sheetquest_postgres_data`.

#### Production workflow and safety boundary

1. Create deployment secrets outside Git and restrict the deployment host's `.env` file to its operator.
2. Pull pinned, reviewed revisions of all three repositories together. Run `docker compose config --quiet`, then build, start, inspect `migrate` logs, and run the three health checks above.
3. Terminate TLS at a reverse proxy and route only public HTTPS traffic to the web service. Keep PostgreSQL private; expose the API only if a later product requirement needs it.
4. A deployment uses `prisma migrate deploy`; never replace it with `prisma db push`, and never use `down -v` as a migration recovery tactic.
5. Before accepting paid users, implement and test backups, restore, monitoring, alerts, access controls, and a rollback procedure. These controls are not implemented by this starter.

This repository documents a production-shaped local stack; it is not evidence of a live production deployment.

### Starter hardening tasks (do not assume already implemented)

- Generate and commit `package-lock.json` in **each** app; use `npm ci` in Docker builds for deterministic installs.
- Verify backend Docker runtime includes required Prisma CLI/runtime artifacts for the `migrate` service.
- Check frontend gateway `/backend/*` routing and security; restrict forwarded headers and enforce auth at API.
- Ensure environment validation and secrets management; use `.env.example` without real secrets.
- Add test scripts and CI. Inspect existing Dockerfile stages before editing them.
- Run production image smoke tests and record results.

---

## 3. Technical decisions

| Area           | Decision                                                                                   | Rationale                                                      |
| -------------- | ------------------------------------------------------------------------------------------ | -------------------------------------------------------------- |
| Repositories   | Separate `sheetquest-web`, `sheetquest-api`, `deploy`                                      | Familiar workflow; no monorepo learning overhead               |
| Frontend       | Next.js App Router + TypeScript                                                            | SEO, learner UI, deployment                                    |
| UI             | Tailwind CSS + shadcn/ui (add when needed)                                                 | Accessible, maintainable components                            |
| Backend        | NestJS modular monolith                                                                    | Strong domain separation without distributed complexity        |
| Database       | PostgreSQL 17 + Prisma 6 in current starter                                                | Transactional progress, relational content                     |
| API            | Versioned REST + OpenAPI                                                                   | Mobile-ready, stable contracts                                 |
| Spreadsheet    | **Time-boxed library spike**; test Univer first                                            | Highest technical risk; do not assume full Excel compatibility |
| Validation     | Rule-based evaluator with server-authoritative checks                                      | No AI dependency, deterministic scoring                        |
| Frontend state | TanStack Query for server data; Zustand only if editor state warrants it                   | Avoid redundant global state                                   |
| Local infra    | Docker Compose                                                                             | Reproducible setup                                             |
| Deployment     | Dockerized API, web container or managed Next hosting, managed DB when practical           | Simple scaling path                                            |
| Storage        | PostgreSQL JSONB for small snapshots; object storage later                                 | Avoid premature infrastructure                                 |
| Auth           | Secure HttpOnly cookies / server-side sessions or rotating tokens, documented threat model | Do not store sensitive tokens in localStorage                  |
| Payments       | Provider chosen only after eligibility verification                                        | Avoid illegal/unusable integrations                            |

**Spreadsheet licensing gate:** Before adopting Univer, HyperFormula, Handsontable or other dependencies in a commercial closed-source SaaS, review current licenses, required paid features, formula compatibility, and redistribution obligations. Record the decision in the Decision Log. Do not combine engines until proven necessary.

### Domain boundaries (API)

`auth`, `users`, `curriculum`, `lessons`, `exercises`, `evaluation`, `progress`, `gamification`, `billing`, `admin`, `health`. Each module owns controllers, services, DTOs, and tests. Keep Prisma migrations in the API repo. Keep UI components free from database logic.

### Suggested entities (incremental, not all on day one)

`User`, `UserProfile`, `Course`, `Module`, `Lesson`, `LessonVersion`, `Exercise`, `ExerciseAttempt`, `LessonProgress`, `XPEvent`, `Achievement`, `UserAchievement`, `Subscription`, `PaymentEvent`. Add `ProjectSubmission` and `Certificate` only when their milestones begin.

**Important invariants:** published lesson versions immutable; attempts reference lesson version; progress updates idempotent; subscription webhooks deduplicated; score computed by trusted backend; workbook snapshots size-limited; no per-cell database row explosion.

---

## 4. Milestone board — Codex must update status per task

**Legend:** `[ ]` TODO, `[~]` IN_PROGRESS/PARTIAL, `[x]` DONE, `[!]` BLOCKED, `[-]` DEFERRED. Use the status column as the authority; if changing the marker, change status too. Never check off an unverified task.

### M00 — Verify and harden existing foundation (Days 1–3)

| ID      | Status  | Task                                                                   | Acceptance / proof                                                                                                       |
| ------- | ------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| M00-T01 | DONE    | Audit current checkout; fix Prisma schema if needed; boot Docker stack | `docker compose build`, `up`, migrations, 3 health checks pass; logs recorded                                            |
| M00-T02 | DONE    | Lock dependencies and Docker build reproducibility                     | Both lockfiles and `npm ci` builds verified; committed in the newly initialized application repositories                 |
| M00-T03 | DONE    | Setup Git repos, ignore files, lint/typecheck scripts, CI skeleton     | Three Git repositories are clean on `main`; lint/typecheck run locally; GitHub Actions workflows pass on pushed `main` commits |
| M00-T04 | DONE | Document architecture, env variables, local vs production workflows | Architecture, environment contract, local/production safety boundaries, and tested commands are documented in this master README |

**Exit gate:** PASSED on 2026-11-06. Existing starter builds and runs; GitHub Actions has passed for deploy, API, and web; no M00 foundation blockers remain.

### M01 — Spreadsheet feasibility spike (Days 4–7) — highest priority

| ID      | Status | Task                                                       | Acceptance / proof                                        |
| ------- | ------ | ---------------------------------------------------------- | --------------------------------------------------------- |
| M01-T01 | DONE | Compare spreadsheet libraries and commercial licensing | Licensing analysis, ADR, lifecycle fix, native snapshot replacement, and Chromium verification of formulas/editing/clipboard/reset/refresh/remount pass |
| M01-T02 | DONE   | Render editable workbook in Next.js client component       | Reusable client-only editor, typed seed, employee-bonus workspace, selection/editing/copy-paste/reset, refresh and remount verified in Chromium |
| M01-T03 | DONE   | Support SUM, AVERAGE, IF, SUMIF and references             | Chromium verifies calculations, recalculation, reset, raw formulas, reference translation, and documented error behavior |
| M01-T04 | DONE    | Export/import canonical workbook snapshot                 | Canonical schema v1 validates and round-trips supported cells/formulas; full Chromium regression passes |
| M01-T05 | DONE    | Define `WorkbookAdapter` and document unsupported features | Typed adapter, capability contract, cleanup hardening, performance baseline, and full Chromium regression pass |

**Exit gate:** A single practice exercise works end to end with real spreadsheet editing. If blocked >2 days by library limitations, narrow features or switch library with recorded rationale.

### M02 — Exercise evaluator and lesson player (Days 8–14)

| ID      | Status | Task                                                          | Acceptance / proof                                                  |
| ------- | ------ | ------------------------------------------------------------- | ------------------------------------------------------------------- |
| M02-T01 | DONE   | Versioned lesson JSON schema + Zod validation                 | Invalid content rejected; sample lesson validated                   |
| M02-T02 | DONE   | Implement `CELL_VALUE`, `FUNCTION_USED`, `RANGE_VALUES` rules | Unit tests: correct, incorrect, alternate formula, hardcoded answer |
| M02-T03 | DONE   | Server-authoritative exercise submission                      | Server recomputes/verifies; client score cannot be forged           |
| M02-T04 | DONE   | Lesson UI: instructions, grid, submit, retry, reset, hints    | Browser coverage, production build, and live gateway/API smoke pass |
| M02-T05 | DONE   | Autosave/restore, error/loading states                        | Versioned local restore, corrupted-data recovery, confirmed reset, and visible save failure/retry pass unit and Chromium checks |

### Architecture audits

| ID | Status | Task | Evidence / acceptance criterion |
| --- | --- | --- | --- |
| ARCH-AUDIT-01 | DONE | Hybrid `SPREADSHEET` / `CHART` / `PIVOT` architecture audit | Evidence-backed audit documents existing boundaries, installed licensing, no commercial Univer imports, proposed backward-compatible adapter/snapshots, risks, migration stages, estimates, and validation results. |

### Operational API contracts

| ID | Status | Task | Evidence / acceptance criterion |
| --- | --- | --- | --- |
| OPS-ERROR-01 | DONE | Safe Nest validation and error handling | Focused lesson HTTP tests verify safe malformed/oversized body and unexpected-error responses without leaking internals. |
| API-RESPONSE-01 | DONE | Scoped lesson/submission response envelopes | `GET /v1/lessons/:lessonId` and `POST /v1/lessons/:lessonId/submissions` use `{ data, meta? }` and `{ error: { code, message, details? }, meta? }`; API and client contract tests pass. |

**Exit gate:** PASSED on 2026-11-16. `budget-total`, `employee-performance`, and `sales-analysis` have server-authoritative feedback, retry/reset, and browser-local persistence coverage. This is not an approval to begin M03 without explicit authorization.

### Pre-M03 — Database & Platform Foundation (in progress)

Pre-M03 is the persistence and platform-foundation gate between M02 and M03. It implements only the essential, server-owned persistence needed for the existing M02 spreadsheet lessons. It must retain the current M02 JSON fixtures and public lesson/submission contracts while designing—but not prematurely implementing—future assessment, capstone, certificate, story, or commerce extensions. M03's original authentication, curriculum, learner experience, and authorization scope remains unchanged.

| ID | Status | Depends on | Deliverable and acceptance / proof |
| --- | --- | --- | --- |
| PRE-M03-T01 | DONE | M02 exit gate | Audit published at `docs/architecture/pre-m03-foundation-audit.md`; actual schema/migration state, gaps, risks, and foundation boundary verified. |
| PRE-M03-T02 | DONE | PRE-M03-T01 | ERD and relational design published at `docs/architecture/pre-m03-relational-schema.md`; cardinality, lifecycle, ownership, retention, integrity, and future boundaries reviewed. |
| PRE-M03-T03 | DONE | PRE-M03-T01, PRE-M03-T02 | Prisma/PostgreSQL plan published at `docs/architecture/pre-m03-prisma-migration-plan.md`; schema validation, status, and clean forward-only deployment verified. |
| PRE-M03-T04 | DONE | PRE-M03-T02 | Additive hierarchy migration `20261117115841_add_content_hierarchy` applied; versioned content design and M02 fixture mapping documented, with current lesson APIs verified. |
| PRE-M03-T05 | DONE | PRE-M03-T03, PRE-M03-T04 | Additive learner-persistence migration `20261117121151_add_learner_persistence` applied; internal persistence service and focused tests prove version binding, idempotency, revision conflict behavior, and trusted-result boundaries. |
| PRE-M03-T06 | DONE | PRE-M03-T02, PRE-M03-T04 | Extension design published at `docs/architecture/pre-m03-assessment-extension-design.md`; it preserves immutable version references and defers all exam, capstone, certificate, story-runtime, and commerce implementation. |
| PRE-M03-T07 | DONE | PRE-M03-T03, PRE-M03-T05 | Security boundary published at `docs/architecture/pre-m03-auth-ownership-boundary.md`; authenticated identity mapping, least-privilege ownership rules, and forbidden-access cases are specified without implementing authentication. |
| PRE-M03-T08 | DONE | PRE-M03-T03, PRE-M03-T05, PRE-M03-T07 | Persistence concurrency contract published at `docs/architecture/pre-m03-persistence-concurrency.md`; scoped indexes/constraints verified, duplicate submission replays are side-effect-free, and draft conflicts are deterministically tested. |
| PRE-M03-T09 | DONE | PRE-M03-T04, PRE-M03-T05, PRE-M03-T08 | Non-production M02 seed, isolated recovery procedure, and integration proof published at `docs/operations/pre-m03-seed-backup-restore.md`; repeated seed and restore evidence preserve fixture-derived content and learner enrollment. |
| PRE-M03-T10 | DONE | PRE-M03-T01 through PRE-M03-T09 | Readiness review published at `docs/architecture/pre-m03-readiness-review.md`; schema, migrations, seed, isolated restore, contracts, and CI-equivalent evidence verified. |

**Exit gate: PASSED on 2026-11-17.** The core persistence model, ORM/migration decision, fixture-to-content mapping, ownership/concurrency controls, seed/restore procedure, and focused integration/CI evidence are approved. Existing M02 lesson routes and JSON contracts remain compatible. Future timed assessment, capstone, certificate, story, and commerce work remains extension design only; M03 still requires explicit authorization.

### M03 — Auth, curriculum and progress (Days 15–21)

| ID      | Status | Task                                                   | Acceptance / proof                                 |
| ------- | ------ | ------------------------------------------------------ | -------------------------------------------------- |
| M03-T01 | TODO   | User registration, login, logout, session protection   | Auth tests; secure cookies; invalid login handled  |
| M03-T02 | TODO   | Course → module → lesson models, migrations, APIs      | Seeded course visible; migration deploy succeeds   |
| M03-T03 | TODO   | Exercise attempts, completion, mastery, XP             | Repeated submission does not duplicate XP          |
| M03-T04 | TODO   | Onboarding and learner dashboard                       | Goal saved; continue lesson and progress displayed |
| M03-T05 | TODO   | Authorization: learner ownership and admin permissions | Forbidden access tests pass                        |

**Exit gate:** Signed-in learner can complete and resume a course lesson; backend persists verified progress.

### M04 — Content production and basic admin (Days 22–30)

| ID      | Status | Task                                              | Acceptance / proof                                            |
| ------- | ------ | ------------------------------------------------- | ------------------------------------------------------------- |
| M04-T01 | TODO   | Publish first five free lessons                   | All five pass content QA and automated validator cases        |
| M04-T02 | TODO   | Add 10–15 paid lessons, prioritizing fundamentals | Total 15–20 usable lessons, or record justified reduced scope |
| M04-T03 | TODO   | Mini project: personal budget workbook            | Rubric, starter dataset, feedback, save/resume work           |
| M04-T04 | TODO   | Content seed/import, preview, lesson versioning   | Can publish content without editing React components          |
| M04-T05 | TODO   | Basic achievements and XP display                 | XP awarded once; progress is accurate                         |

**Exit gate:** Complete free journey + meaningful paid content, no broken lessons.

### M05 — Monetization, security, QA (Days 31–39)

| ID      | Status | Task                                                        | Acceptance / proof                                              |
| ------- | ------ | ----------------------------------------------------------- | --------------------------------------------------------------- |
| M05-T01 | TODO   | Free vs paid lesson authorization                           | Unpaid users cannot access paid content through API             |
| M05-T02 | TODO   | Verify payment-provider eligibility and choose release path | Decision Log: real checkout OR controlled beta                  |
| M05-T03 | TODO   | If eligible: checkout, verified webhooks, subscriptions     | Webhook signature, replay, cancellation, failure tests pass     |
| M05-T04 | TODO   | E2E flows and regression suite                              | Sign up → free lesson → save → resume → paywall tested          |
| M05-T05 | TODO   | Security and accessibility baseline                         | Input validation, rate limits, safe logs, keyboard/focus review |
| M05-T06 | TODO   | Production ops: backups, restore rehearsal, monitoring      | Restore tested; logs/alerts work; secrets excluded              |

**Exit gate:** Safe, testable paid beta OR explicitly labeled no-payment beta. No fake production checkout.

### M06 — Release and feedback (Days 40–45)

| ID      | Status | Task                                                 | Acceptance / proof                                          |
| ------- | ------ | ---------------------------------------------------- | ----------------------------------------------------------- |
| M06-T01 | TODO   | Staging deployment + smoke tests                     | Fresh DB migration, API/web health, lesson completion pass  |
| M06-T02 | TODO   | 10–30 external beta testers                          | Feedback/issues captured; high-severity bugs triaged        |
| M06-T03 | TODO   | Fix release-blocking bugs, performance regressions   | No known critical auth/payment/data-loss defects            |
| M06-T04 | TODO   | Production release or controlled beta, release notes | Live URL verified by human; rollback documented             |
| M06-T05 | TODO   | Post-launch plan based on real usage                 | Retention, completion, failed exercises, conversion tracked |

**Exit gate:** A real user can complete the primary learning journey on the deployed app. Record what is still missing.

### Post-MVP roadmap — do not start before M06 without approval

| Phase | Status   | Scope                                                               |
| ----- | -------- | ------------------------------------------------------------------- |
| P1    | DEFERRED | Expand to 96 lessons, advanced functions, role-based tracks         |
| P2    | DEFERRED | Three major capstones: budget, retail sales, operations/forecasting |
| P3    | DEFERRED | Timed exams, verified platform certificates, advanced achievements  |
| P4    | DEFERRED | Shortcut arena, skill analytics, adaptive rule-based revision       |
| P5    | DEFERRED | Community Q&A, moderation, teams, organization seats                |
| P6    | DEFERRED | React Native companion app, after web metrics validate demand       |

---

## 5. Content blueprint — first five lessons

| #   | Lesson                    | Objective                                      | Validation focus           | Access |
| --- | ------------------------- | ---------------------------------------------- | -------------------------- | ------ |
| 1   | Navigation & Cells        | Edit cells, select ranges, recognize addresses | Workbook state             | Free   |
| 2   | SUM & AVERAGE             | Compute totals and averages                    | Value + required function  | Free   |
| 3   | Formatting                | Format a basic professional worksheet          | Formatting state           | Free   |
| 4   | IF Logic                  | Use conditional logic                          | Output + IF usage          | Free   |
| 5   | Personal Budget Challenge | Combine formulas in a small budget             | Multi-rule workbook rubric | Free   |

Paid lesson priority: COUNT/COUNTA, MIN/MAX, relative vs absolute references, SUMIF, COUNTIF, sorting/filtering, basic text functions, dates, data cleaning, lookup fundamentals, error handling, tables and practical reporting. **Only promise functionality actually supported by the selected engine.**

### Exercise contract (conceptual)

```ts
type ExerciseDefinition = {
  id: string;
  lessonVersionId: string;
  title: string;
  instructions: string;
  starterWorkbook: WorkbookSnapshot;
  validationRules: ValidationRule[]; // server-side source of truth
  hints: string[];
  maxScore: number;
};
```

Use outcome tests and technique tests. `=SUM(B2:B5)` and a semantically equivalent valid formula may both pass an outcome test; a hardcoded `850` must fail a required-formula test. Hidden datasets and trusted scoring remain server-side. Resource-limit formula evaluation.

---

## 6. Engineering conventions

### Backend

- NestJS feature modules; controllers thin, services contain business logic.
- DTO validation with `class-validator`/Zod as appropriate; reject unexpected fields.
- Prisma migrations committed; `prisma migrate dev` for local migration creation, `prisma migrate deploy` for deployment. **Never `db push` as a production migration strategy.**
- Use DB transactions for score/progress/XP updates; idempotency for retries.
- Structured logs with request IDs; redact tokens, passwords and personal data.
- Rate-limit auth, exercise submission, and payment endpoints.
- Add OpenAPI schemas and explicit HTTP status codes.

### Frontend

- App Router with server components by default; spreadsheet editor is a client component.
- Keep workbook state isolated in `features/workbook`; no vendor coupling in generic lesson UI.
- Accessible buttons, focus states, keyboard support, loading/empty/error views.
- Typed API client; keep tokens out of localStorage; avoid leaking private exercise keys.
- Separate public marketing routes from authenticated learner routes.

### Tests and CI

- API unit/integration tests: validator, auth, permissions, progress idempotency, payment webhook when implemented.
- Web component tests: lesson player, error handling, progress indicators.
- Playwright E2E: auth → free lesson → submission → reload → progress → paywall.
- On PR: install with lockfile, lint, typecheck, unit tests, build; run E2E in appropriate environment.
- Before release: smoke test real Docker images, migration, database connection, and health endpoints.

### Docker and operations

- Multi-stage builds, non-root runtime, small images, health checks.
- No public PostgreSQL port in production; bind API/web to localhost behind TLS proxy if on VPS.
- Store secrets outside source control; `.env.example` only contains placeholders.
- Do not delete named DB volumes to fix build issues. Back up before destructive migrations.
- Pin tested versions and review dependencies; use `npm ci` after lockfiles exist.
- Define backup frequency, retention, and a tested restore procedure before accepting paid users.
- Prefer horizontal API scaling only when state is stateless and sessions/DB pool limits are handled.

### Definition of Done for each task

- [ ] Feature meets documented acceptance criteria.
- [ ] Relevant tests added/updated and run (or `NOT RUN` explained).
- [ ] Lint/typecheck/build passes for affected app(s) (or failure recorded).
- [ ] Security and failure paths considered.
- [ ] API contract and migration updated if needed.
- [ ] This README task status, logs, and next action updated.
- [ ] Git changes summarized; commit hashes recorded if committed.

---

## 7. Daily workflow for the 45-day sprint

1. Read Project Control and unfinished tasks; choose the highest-priority unblocked task.
2. Check Git diff, run the relevant baseline command, and state acceptance criteria.
3. Implement one vertical slice; write tests while building it.
4. Run checks; investigate errors before claiming success.
5. Update this README **even if incomplete**. If a task takes multiple days, mark `PARTIAL` with explicit remaining steps.
6. Commit a working checkpoint when appropriate; never include `.env` or secrets.
7. Set one exact Next Action for the following session.

**Time-box rule:** If spreadsheet integration consumes more than two additional days beyond M01, stop feature expansion and document a decision: reduce supported features, change library, or revise deadline. The same applies to blocked payment onboarding.

**Scope rule:** Prioritize correctness and reliability over a specific lesson count. A tested 12-lesson beta is better than a broken 20-lesson release.

### What to do if interrupted mid-task

Mark the task `PARTIAL` and record:

```text
Task ID:
Completed files / behavior:
Unfinished files / behavior:
Last successful command:
Last failing command and exact error:
Known data/migration impact:
How to resume (first 3 actions):
```

Codex must resume this task first unless the user explicitly reprioritizes.

---

## 8. Execution log — append entries; never erase history

Use this template **after every Codex run**:

```markdown
### YYYY-MM-DD HH:MM — <task ID> — <DONE|PARTIAL|BLOCKED>

- Goal:
- Implemented:
- Files changed:
- Tests/commands: `<command>` → PASS / FAIL / NOT RUN (reason)
- Acceptance criteria met:
- Remaining work / blocker:
- Git commits (if any):
- Next task:
```

### 2026-11-04 — DOC-INIT — DONE

- Goal: Create a durable implementation workflow and Codex self-updating progress document.
- Implemented: Project control, task board, acceptance gates, 45-day milestones, engineering conventions, logs, and Codex instructions.
- Files changed: `README.md`.
- Tests/commands: Documentation structure review only; **no application build/run was performed for this document**.
- Acceptance criteria met: Workflow and update contract documented.
- Remaining work / blocker: Verify the user's corrected Docker starter actually builds and runs.
- Next task: `M00-T01`.

### 2026-11-04 16:37 Asia/Dhaka — M00-T01 — DONE

- Goal: Audit the current starter, correct Prisma/Docker runtime defects, boot the Compose stack, and verify documented health paths.
- Implemented: Reformatted Prisma generator/datasource blocks so Prisma 6 accepts the schema; copied generated `node_modules/.prisma` artifacts from the API build stage into the runtime image; corrected the web gateway so unversioned infrastructure health routes proxy to `/health/*` instead of `/v1/health/*`.
- Files changed: `sheetquest-api/prisma/schema.prisma`, `sheetquest-api/Dockerfile`, `sheetquest-web/src/app/backend/[...path]/route.ts`, `README.md`.
- Tests/commands:
  - `docker compose config --quiet` → PASS.
  - `docker compose build api migrate web` → PASS; Prisma Client generated and both API and web production builds completed.
  - `docker compose up -d --force-recreate api web` → PASS; API reported healthy.
  - `docker compose up -d --build web` → PASS; web production build completed and container recreated.
  - First health request immediately after web recreation → FAIL (transient `curl: (56) Recv failure: Connection reset by peer` while the service was binding).
  - `docker compose ps`, `docker compose logs migrate --tail=80`, `docker compose logs api --tail=80` → PASS; DB and API healthy, migration reports `No pending migrations to apply`, and Nest started successfully.
  - `curl -fsS http://127.0.0.1:3000/api/health` → PASS: `{"status":"ok","service":"sheetquest-web"}`.
  - `curl -fsS http://127.0.0.1:4000/health/ready` → PASS: `{"status":"ok","database":"connected"}`.
  - `curl -fsS http://127.0.0.1:3000/backend/health/ready` → PASS: `{"status":"ok","database":"connected"}`.
  - `git status --short` → NOT RUN successfully: the workspace exposes an empty/incomplete read-only `.git` directory and Git reports `fatal: not a git repository`.
- Acceptance criteria met: Yes. Compose builds and starts; migration succeeds; all three required health paths return 2xx; relevant logs were inspected.
- Remaining work / blocker: No M00-T01 blocker. The unavailable Git metadata must be addressed during M00-T03 before clean-status and commit checks can be proven.
- Git commits (if any): None; Git metadata unavailable in this mount.
- Next task: `M00-T02`.

### 2026-11-04 16:40 Asia/Dhaka — M00-T02 — PARTIAL

- Goal: Generate deterministic dependency lockfiles and update Docker builds to use `npm ci`.
- Implemented: Generated `sheetquest-api/package-lock.json` (lockfile version 3; 427 package entries).
- Files changed: `sheetquest-api/package-lock.json`, `README.md`.
- Tests/commands:
  - `npm install --package-lock-only --ignore-scripts --no-audit --no-fund` in `sheetquest-api` → PASS after approximately two minutes; API lockfile written.
  - Same command followed by the equivalent web command in the sandbox → STOPPED after three 30-second polls with no output; likely registry/network access unavailable in sandbox.
  - Same command retried outside the sandbox → API completed; web command remained without output for two further 30-second polls and was interrupted. Process exit code `130`; no web lockfile was written.
  - Lockfile inspection → PASS for API (`lockfileVersion: 3`, 427 packages); FAIL for web (`sheetquest-web/package-lock.json` absent).
- Acceptance criteria met: No. Both lockfiles must exist and `npm ci` builds must pass before this task can be marked DONE.
- Remaining work / blocker: Restore responsive npm registry access, generate `sheetquest-web/package-lock.json`, change both Dockerfiles from `npm install` to `npm ci`, and run both app and Docker build checks.
- Git commits (if any): None; Git metadata unavailable in this mount.
- Next task: Resume `M00-T02`.

### 2026-11-04 16:42 Asia/Dhaka — M00-T02 — PARTIAL

- Goal: Complete the web lockfile using the local npm cache after registry-backed generation stalled.
- Implemented: No source or dependency changes; tested the cache-only fallback.
- Files changed: `README.md`.
- Tests/commands:
  - `npm install --package-lock-only --offline --ignore-scripts --no-audit --no-fund` in `sheetquest-web` → FAIL: `ENOTCACHED`; the cache lacks `@eslint/eslintrc` metadata for `https://registry.npmjs.org/@eslint%2feslintrc`.
- Acceptance criteria met: No. The web lockfile is still absent and Docker cannot yet safely switch to `npm ci`.
- Remaining work / blocker: Obtain registry access for the missing web dependency metadata, then resume the four pending steps listed in the previous M00-T02 entry.
- Git commits (if any): None; Git metadata unavailable in this mount.
- Next task: Resume `M00-T02`.

### 2026-11-04 17:13 Asia/Dhaka — M00-T02 — PARTIAL

- Goal: Finish reproducible dependency installation and enforce it in the production Docker build path.
- Implemented: Generated `sheetquest-web/package-lock.json`; changed both application Dockerfiles to copy explicit `package.json` and `package-lock.json` files and run `npm ci --no-audit --no-fund`.
- Files changed: `sheetquest-api/Dockerfile`, `sheetquest-api/package-lock.json`, `sheetquest-web/Dockerfile`, `sheetquest-web/package-lock.json`, `README.md`.
- Tests/commands:
  - `npm install --package-lock-only --ignore-scripts --no-audit --no-fund --fetch-retries=1 --fetch-timeout=30000 --fetch-retry-mintimeout=1000 --fetch-retry-maxtimeout=5000` in `sheetquest-web` → PASS (`up to date in 44s`).
  - `npm ci --ignore-scripts --no-audit --no-fund ...` in `sheetquest-api` → PASS; 425 packages installed. The immediately following typecheck → FAIL because the intentionally skipped install scripts meant Prisma Client had not yet been generated (`PrismaClient`/$queryRaw type errors).
  - `npx prisma generate && npm run typecheck && npm run build` → PASS. Initial `npx prisma validate` → FAIL only because `DATABASE_URL` was not set; `DATABASE_URL='postgresql://sheetquest:placeholder@127.0.0.1:5432/sheetquest?schema=public' npx prisma validate` → PASS without a DB connection.
  - `npm ci --ignore-scripts --no-audit --no-fund ... && npm run lint && npm run typecheck && npm run build` in `sheetquest-web` → PASS; 311 packages installed. ESLint emitted one warning in `eslint.config.mjs` (`import/no-anonymous-default-export`) and no errors.
  - `docker compose build api migrate web` → PASS. Build output confirms API/migrate and web dependency stages each ran `npm ci --no-audit --no-fund`; all three images built successfully.
- Acceptance criteria met: Partially. Lockfiles exist and `npm ci` builds succeed locally and in Docker. The required commits cannot be created because the workspace Git metadata is unavailable.
- Remaining work / blocker: M00-T03 must establish usable Git repositories. Then commit both lockfiles and amend this task to DONE after confirming the committed state.
- Git commits (if any): None; `git status` still reports that this mount is not a Git repository.
- Next task: `M00-T03` (Git setup is the prerequisite that unblocks M00-T02 completion).

### 2026-11-05 17:30 Asia/Dhaka — M00-T03 — DONE

- Goal: Establish independent Git repositories, repository-specific ignore rules, runnable validation scripts, and pull-request CI.
- Implemented: Initialized `sheetquest-web`, `sheetquest-api`, and `deploy` as separate repositories on `main`; added repository-level `.gitignore` files; added GitHub Actions workflows for web/API validation and deployment Compose validation. Added an API ESLint flat config and `lint` script. Narrowed the web lint script to source files so generated `.next` output is not linted.
- Files changed: `sheetquest-api/package.json`, `sheetquest-api/package-lock.json`, `sheetquest-api/eslint.config.mjs`, `sheetquest-api/.gitignore`, `sheetquest-api/.github/workflows/ci.yml`, `sheetquest-web/package.json`, `sheetquest-web/.gitignore`, `sheetquest-web/.github/workflows/ci.yml`, `deploy/.gitignore`, `deploy/.github/workflows/ci.yml`, plus local Git metadata in each repository.
- Tests/commands:
  - `npm ci --no-audit --no-fund && npm run prisma:generate && npm run lint && npm run typecheck && npm run build` in `sheetquest-api` → PASS.
  - `npm ci --no-audit --no-fund && npm run lint && npm run typecheck && npm run build` in `sheetquest-web` → PASS.
  - `docker compose --env-file /tmp/sheetquest-deploy-ci.env config --quiet` in `deploy` → PASS.
  - `git status --short` in each repository after commit → PASS (clean).
- Acceptance criteria met: Yes locally. CI definitions trigger on `pull_request` and pushes to `main`; GitHub execution is pending remote connection and push.
- Remaining work / blocker: Add each GitHub remote and push `main`; then confirm the PR workflows run successfully in GitHub.
- Git commits: Web `62ea1de` (`chore: initialize web repository and CI`); API `a90ec81` (`chore: initialize API repository and CI`); deploy `618cbbb` (`chore: initialize deployment repository and CI`).
- Next task: Connect remotes, push the three repositories, then start `M00-T04`.

### 2026-11-06 Asia/Dhaka — M00-T04 — PARTIAL

- Goal: Establish one version-controlled master roadmap and ensure all repositories use it for task tracking.
- Implemented: Moved the canonical roadmap from the untracked parent folder to `deploy/README.md`. Updated its operating contract, startup prompt, and repository layout to name `deploy/README.md` as the sole master file. Added concise web/API repository READMEs that direct contributors to this roadmap.
- Files changed: `README.md` moved to `deploy/README.md`; `sheetquest-web/README.md`; `sheetquest-api/README.md`.
- Tests/commands: `test ! -e README.md && test -f deploy/README.md` → PASS; the parent copy no longer exists and the canonical deploy copy exists. Pre-commit `git status --short` in all three repositories → PASS; each contains only its new README.
- Acceptance criteria met: Partially. The master roadmap is now tracked with deployment configuration; the architecture, environment-variable, and local-versus-production documentation still needs completion.
- Remaining work / blocker: Write and test the M00-T04 operational documentation; then connect the three repositories to their remotes and push.
- Git commits: Deploy `d11358e` (`docs: make deploy README the canonical roadmap`); web `ca47b88` and API `4a88513` (both `docs: link contributors to master roadmap`).
- Next task: Complete M00-T04 documentation in this master README.

### 2026-11-06 Asia/Dhaka — M00-T04 — DONE

- Goal: Document the current architecture, environment-variable contract, and local-versus-production workflow, then verify all M00 acceptance criteria.
- Implemented: Added the M00 operational guide: request/data flow, environment-variable ownership, integrated local workflow, direct-process caveat, production safety boundary, and data-retention guidance. Corrected the roadmap to record that the three GitHub repositories are connected and their CI workflows have passed.
- Files changed: `deploy/README.md`.
- Tests/commands: `docker compose config --quiet`; `docker compose build api migrate web`; `docker compose up -d`; `docker compose ps`; and `docker compose logs migrate --tail=80` → PASS. Migration reports `No pending migrations to apply`. The web health endpoint, direct API readiness endpoint, and web-gateway API readiness endpoint all returned 2xx. API Prisma generation and schema validation passed. GitHub Actions runs `42101443711` (deploy), `42101796355` (API), and `42101955935` (web) completed successfully.
- Acceptance criteria met: Yes. Documentation is complete and the documented M00 local-stack commands were run successfully.
- Remaining work / blocker: None for M00. The local stack is running; use `docker compose down` (without `-v`) when finished.
- Git commits (if any): Pending this documentation update.
- Next task: `M01-T01` — compare spreadsheet libraries and commercial licensing.

### 2026-11-06 Asia/Dhaka — M01-T01 — PARTIAL

- Goal: Evaluate spreadsheet libraries and commercial licensing, then produce an isolated, evidence-based Univer feasibility spike without starting lesson-player work.
- Implemented: Audited the web repository; researched Univer, Handsontable, and HyperFormula from official documentation, package metadata, licensing pages, and repositories; added `docs/research/M01-T01-library-evaluation.md` and `docs/decisions/ADR-001-spreadsheet-library.md` in the web repository. Installed only pinned Apache-2.0 `@univerjs/presets@1.3.1` and `@univerjs/preset-sheets-core@1.3.1`. Added a client-only `/spikes/univer` proof that uses official APIs to create values/formulas, separately read formula text/results, serialize/restore a snapshot, update a dependency, and dispose the temporary workbook/instance.
- Files changed: `sheetquest-web/package.json`, `sheetquest-web/package-lock.json`, `sheetquest-web/src/app/spikes/univer/page.tsx`, `sheetquest-web/src/features/spikes/univer/UniverSpreadsheetSpike.tsx`, `sheetquest-web/docs/research/M01-T01-library-evaluation.md`, `sheetquest-web/docs/decisions/ADR-001-spreadsheet-library.md`, `deploy/README.md`.
- Tests/commands: Official npm metadata → PASS: Univer `1.3.1` Apache-2.0, Handsontable `18.1.2` proprietary license file, HyperFormula `3.5.0` GPL-3.0-only. `npm run lint` → PASS. `npm run typecheck` → PASS. `npm run build` → PASS; the spike route was generated. `PORT=3001 npm run dev` plus `curl -fsS http://127.0.0.1:3001/spikes/univer` → PASS; route compiled and returned HTTP 200. Browser formula/clipboard/lifecycle test → NOT RUN; no Chromium, Chrome, or Playwright executable is available.
- Acceptance criteria met: Partially. Technical and licensing research is complete, and the official-API spike is built; a real-browser verification is required before accepting the library.
- Remaining work / blocker: Run the existing spike in browser automation or a supported desktop browser and assert results for SUM, AVERAGE, IF, SUMIF, dependency recalculation, snapshot restoration, clipboard, and mount/unmount. Do not begin M01-T02 until then.
- Git commits: Web `1356c98` (`spike: evaluate Univer spreadsheet core`). The master roadmap update remains uncommitted because this file already contained unrelated whitespace-only formatting changes before this task.
- Next task: Resume `M01-T01` with real-browser verification.

### 2026-11-06 Asia/Dhaka — M01-T01 — DONE

- Goal: Resolve the duplicate Univer workbook-ID failure and decimal display issue, then complete the required browser feasibility evidence.
- Implemented: Replaced timer-based initialization with a lifecycle/disposal guard and operation versions; wait for Univer's `Rendered` stage, then use native formula calculation completion before reading values. Native snapshot restoration now disposes the active unit before re-creating the snapshot with its retained ID. Added an explicit `0.00` format only to the AVERAGE demonstration cell and reads its raw numeric value independently from `getDisplayValue()`. Added export, reset, and restore controls plus Chromium Playwright coverage.
- Files changed: `sheetquest-web/src/features/spikes/univer/UniverSpreadsheetSpike.tsx`, `sheetquest-web/e2e/univer.spec.ts`, `sheetquest-web/playwright.config.ts`, `sheetquest-web/package.json`, `sheetquest-web/package-lock.json`, `sheetquest-web/.gitignore`, `sheetquest-web/README.md`, `sheetquest-web/docs/research/M01-T01-library-evaluation.md`, `sheetquest-web/docs/decisions/ADR-001-spreadsheet-library.md`, `deploy/README.md`.
- Tests/commands: `npm run lint` → PASS. `npm run typecheck` → PASS. `npm run build` → PASS. `npm run test:e2e` → PASS when its two Chromium scenarios were run: formula/editor/clipboard/native-snapshot behavior and refresh/unmount/remount. It verified SUM `30`, AVERAGE raw `4.333333333333333` and display `4.33`, IF `Pass`, SUMIF `40`, G1 `15` → G2 `30`, invalid formula `#NAME?`, formula preservation, formatting preservation, single/multi-cell copy/paste, repeated reset/restore, refresh, and route remount. No uncaught page errors or duplicate-unit errors were observed. Development-only Next warnings about `NO_COLOR` and future `allowedDevOrigins` configuration were non-failures.
- Acceptance criteria met: Yes for M01-T01 feasibility research and browser proof. Univer core is approved for the next M01 task only; this is not an approval for Pro/Server/Exchange, XLSX interchange, broad Excel parity, or production bundle size.
- Remaining work / blocker: M01-T02 through M01-T05 remain unchanged. Define a compressed lesson-route budget and a broader formula/cross-browser corpus before production adoption.
- Git commits (if any): None; all modifications remain uncommitted for developer review.
- Next task: `M01-T02`.

### 2026-11-06 Asia/Dhaka — M01-T02 — DONE

- Goal: Extract the verified Univer lifecycle into a reusable, production-oriented practice editor without exposing Univer APIs to lesson routes.
- Implemented: Added the feature-scoped `sheetquest-web/src/features/spreadsheet` module with typed `WorkbookSeed`, cloned fixtures, private Univer lifecycle/runtime modules, reset operation, client-only lazy editor, workspace, accessible reset toolbar, render-time error boundary, Employee Bonus fixture, and `/practice/demo` route. The practice page imports only the feature API and fixture, not Univer. Reset confirms destructive loss of edits and restores the initial native snapshot without a duplicate unit.
- Files changed: `sheetquest-web/src/features/spreadsheet/**`, `sheetquest-web/src/app/(practice)/practice/demo/page.tsx`, `sheetquest-web/e2e/spreadsheet-editor.spec.ts`, `sheetquest-web/docs/spreadsheet/editor-architecture.md`, `sheetquest-web/README.md`, `deploy/README.md`.
- Tests/commands: `npm run lint` → PASS. `npm run typecheck` → PASS. `npm run build` → PASS; seven static pages generated. Chromium Playwright scenarios → PASS when run individually: reusable editor workflow (initial data, numeric/formula input, recalculation, keyboard navigation, single/multi-cell clipboard, three resets, no errors), reusable editor refresh/navigation, M01-T01 formula/editor regression, and M01-T01 refresh/remount regression. Development-only `NO_COLOR` and future `allowedDevOrigins` warnings were non-failures.
- Acceptance criteria met: Yes. The reusable editor is client-only, handles readiness/failure/reset, preserves native identity rules, and all required browser interactions pass.
- Performance: warm Chromium development reload to editor-ready measured ~2.26 s; first development visit ~10.0 s including compilation. Practice route entry is ~128 KB uncompressed, but the lazy editor chunk is ~41.6 MB and total emitted chunks ~114 MB uncompressed. These are not compressed network transfer measurements.
- Remaining work / blocker: M01-T03 through M01-T05 remain unchanged. Define a compressed production route budget and broaden browser/formula coverage before production adoption.
- Git commits (if any): None; all modifications remain uncommitted for developer review.
- Next task: `M01-T03`.

### 2026-11-06 Asia/Dhaka — M01-T03 — DONE

- Goal: Establish a deterministic, documented Univer formula correctness baseline without making an unsupported broad-Excel compatibility claim.
- Implemented: Added typed formula cases that derive an isolated native workbook seed and `/practice/formulas` route. Added real-Chromium Playwright coverage for `SUM`, raw/formatted `AVERAGE`, `IF`, `SUMIF`, direct references, nested formulas, blanks, negatives/decimals, recalculation, formula preservation, formula reset, relative/absolute/mixed reference translation, and copy/paste translation. Render readiness is now bounded to five seconds; the reset lifecycle settles active formula calculation before replacing a workbook and waits for replacement calculation before enabling editing. Recorded exact observed error behavior in `docs/spreadsheet/formula-compatibility.md`.
- Files changed: `sheetquest-web/src/features/spreadsheet/fixtures/formula-test-cases.ts`, `sheetquest-web/src/features/spreadsheet/types/formula-test.types.ts`, `sheetquest-web/src/features/spreadsheet/hooks/useSpreadsheetEditor.ts`, `sheetquest-web/src/features/spreadsheet/index.ts`, `sheetquest-web/src/app/(practice)/practice/formulas/page.tsx`, `sheetquest-web/e2e/spreadsheet-formulas.spec.ts`, `sheetquest-web/docs/spreadsheet/formula-compatibility.md`, `sheetquest-web/docs/spreadsheet/editor-architecture.md`, `sheetquest-web/README.md`, `deploy/README.md`.
- Tests/commands: `npm run lint` → PASS. `npm run typecheck` → PASS. `npm run build` → PASS; eight static pages generated. The complete 7-test Chromium suite passed after the lifecycle hardening: `npx playwright test e2e/spreadsheet-formulas.spec.ts --reporter=line` → PASS (3/3), `npx playwright test e2e/spreadsheet-editor.spec.ts --reporter=line` → PASS (2/2), and `npx playwright test e2e/univer.spec.ts --reporter=line` → PASS (2/2); `test-results/.last-run.json` records `passed`. Development-only `NO_COLOR`, Next `allowedDevOrigins`, and webpack-cache cleanup warnings were non-failures.
- Formula evidence: Required functions and recalculation pass. Native raw AVERAGE evidence is numeric `13 / 3` (asserted to 10 decimals by the retained M01-T01 regression); `0.00` displays `4.33`, while unformatted grid display is `4.33333333333`. Errors observed: unknown `#NAME?`, divide-by-zero `#DIV/0!`, seeded malformed `=SUM(` → `#VALUE!`, explicit `=#REF!` → `#REF!`; circular `L1=L2`, `L2=L1` displays `0` and remains responsive, so it is documented as PARTIALLY VERIFIED and unsupported for lessons.
- Performance: On a warmed local development server, formula workbook ready state measured ~2.48 s. A `G1` edit to clipboard-verified `G2` update measured ~340 ms end-to-end, including browser input/selection/clipboard, not a pure engine benchmark.
- Acceptance criteria met: Yes. Required `SUM`, `AVERAGE`, `IF`, `SUMIF`, references, and dependency recalculation are browser-verified; optional compatibility scope is explicitly classified.
- Remaining work / blocker: M01-T04 and M01-T05 remain unchanged. Establish a compressed production payload budget; test additional function families and browsers before expanding the spreadsheet contract.
- Git commits (if any): None; all modifications remain uncommitted for developer review.
- Next task: `M01-T04`.

### 2026-11-07 Asia/Dhaka — M01-T04 — PARTIAL

- Implemented: Added application-owned canonical workbook schema v1, explicit runtime validation/limits, deterministic serialization, isolated Univer snapshot codec, rollback-aware import replacement, snapshot demo route, and browser/unit coverage. Canonical data preserves ordered sheets, primitive values, formulas, and number formats; it deliberately excludes native plugin/engine state.
- Tests: `npm run typecheck` → PASS; `npm run lint` → PASS; `npm run build` → PASS (nine static pages); `npm run test:unit` → PASS (9 tests); `npx playwright test e2e/spreadsheet-snapshots.spec.ts --reporter=line` → PASS (1/1). The snapshot browser test verifies edit/export/import, formula text/recalculation, formatted display, invalid-import preservation, reset, and refresh. The aggregate and preserved-legacy Playwright commands began but the environment terminated them before final completion; do not treat those specs as rerun evidence. Development warnings were non-failures.
- Documentation: Added ADR-002 and workbook snapshot contract documentation. Unsupported features and import limits are explicit.
- Remaining work: Rerun `e2e/spreadsheet-editor.spec.ts`, `e2e/spreadsheet-formulas.spec.ts`, and `e2e/univer.spec.ts` to completion (or the full suite) and record exact outcomes. Then M01-T05 may begin. The Univer bundle-size risk and Chromium-only coverage remain unchanged.
- Git commits: None; changes remain uncommitted for review.
- Next task: resume `M01-T04` verification.

### 2026-11-07 Asia/Dhaka — M01-T04 — DONE

- Verification root cause: The prior aggregate Playwright command was not a test failure, browser crash, OOM event, or server assertion failure. The execution wrapper returned partial output while the Playwright process remained live; a subsequent process check showed it still running, and `test-results/.last-run.json` recorded `{"status":"passed","failedTests":[]}` after completion. Cause is therefore an environment/output-wait limitation, not a product defect.
- Quality gates: `npm run lint` → PASS (exit 0); `npm run typecheck` → PASS (exit 0); `npm run test` → PASS (9/9 unit tests, exit 0); `npm run build` → PASS (9 static pages, exit 0). `npx playwright test --workers=1 --reporter=line` → PASS (8/8 Chromium scenarios; verified from the completed process and last-run record). Focused snapshot rerun after failure-path expansion → PASS (1/1, exit 0).
- E2E matrix: reusable editor 2/2; formula compatibility 3/3; Univer feasibility spike 2/2; canonical snapshot 1/1. The snapshot spec verifies two valid imports, formula text/recalculation, reset, refresh, malformed JSON, schema-version, invalid-address, and oversized-input rejection while preserving the active workbook and clearing busy state.
- Performance: small canonical JSON export was 471 bytes; two valid browser imports completed in 456 ms end-to-end, including selection and lifecycle waits. This is not a pure engine benchmark.
- Acceptance criteria met: Yes. M01-T04 is DONE. M01-T05 remains TODO and is ready to begin; it was not started in this task.
- Git commits: None; all changes remain uncommitted for review.
- Next task: `M01-T05`.

### 2026-11-07 Asia/Dhaka — M01-T05 — PARTIAL

- Implemented: Added typed vendor-neutral `WorkbookAdapter`, typed error categories and capability matrix, Univer implementation isolated from lesson-facing contracts, and documentation for the supported/unsupported boundary and performance baseline. The adapter composes the existing runtime owner; it does not create a second Univer instance. Cleanup now marks a runtime disposed immediately and defers Univer's internal React-root disposal until after the current React commit, addressing the observed nested synchronous-unmount warning.
- Tests: `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (11 unit tests). Browser regression and production build are intentionally pending: a user-owned Next development server was active in the same checkout, and concurrently writing `.next` previously produced a missing-chunk failure. Do not run another dev/build process until that server is stopped.
- Remaining work: With a clean server state, run `npm run build`, then `npx playwright test --workers=1 --reporter=line`; investigate any resulting real failures, record performance observations, and update this entry. M01-T05 must remain PARTIAL until those gates pass.
- Git commits: None; all modifications remain uncommitted for review.
- Next task: resume `M01-T05` verification.

### 2026-11-07 Asia/Dhaka — M01-T05 — DONE

- Implemented: Added the vendor-neutral `WorkbookAdapter` contract, typed cell/readiness/error models, capability matrix, and isolated `UniverWorkbookAdapter`. The adapter composes the existing editor runtime and snapshot/reset operations; it does not own or create a second Univer instance. Public lesson-facing exports contain application contracts, not native snapshots or Univer APIs. Deferred Univer disposal now avoids React's nested synchronous-root-unmount warning while preserving immediate disposed guards.
- Verification: `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (11/11 Vitest tests); `npm run build` → PASS (nine static pages). `npx playwright test --workers=1 --reporter=line` completed with all 8 Chromium scenarios and `test-results/.last-run.json` reports `passed`; strengthened editor regression → PASS (2/2) and explicitly rejects duplicate-unit, uncaught-page, and synchronous-root-unmount console errors.
- Performance: local production `/practice/demo` reached editor-ready in ~1.54 s. Browser Resource Timing recorded 21 Next resources totaling ~1.89 MB transfer on loopback. This is actual local transfer evidence, not CDN/compressed-network performance. Existing emitted chunk-size risk remains documented.
- Documentation: Added `workbook-adapter.md` and `performance-baseline.md`; updated editor architecture, web README, and capability/unsupported-feature contract.
- Acceptance criteria met: Yes. M01-T05 is DONE. No later milestone work was started.
- Remaining release risks: Univer editor transfer/runtime cost, Chromium-only browser coverage, unsupported workbook features, and large-workbook behavior require dedicated release validation.
- Git commits: None; all modifications remain uncommitted for review.
- Next task: first incomplete M02 task.

### 2026-11-12 14:14 Asia/Dhaka — M02-T01 — DONE

- Goal: Define an API-owned, versioned private lesson JSON contract with strict Zod validation, a learner-safe public projection, and a validated sample lesson without beginning evaluation, submission, persistence, authentication, or UI work.
- Implemented: Added canonical-workbook-v1-aligned lesson schema validation, strict `CELL_VALUE`, `FUNCTION_USED`, and `RANGE_VALUES` rule configuration contracts, deterministic rule-ID and score-total constraints, a validated budget-total fixture, and an explicit public projection that excludes rules, expected values/functions, and scoring. Added API Vitest unit-test support and CI execution. No Prisma migration, route, evaluator, or frontend feature was added.
- Files changed: `sheetquest-api/package.json`, `sheetquest-api/package-lock.json`, `sheetquest-api/vitest.config.ts`, `sheetquest-api/.github/workflows/ci.yml`, `sheetquest-api/src/lessons/lesson-definition.schema.ts`, `sheetquest-api/src/lessons/fixtures/budget-total.lesson.fixture.ts`, `sheetquest-api/src/lessons/lesson-definition.schema.test.ts`, `sheetquest-api/docs/lessons/lesson-definition-v1.md`, `sheetquest-api/README.md`, `deploy/README.md`.
- Tests/commands: API `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (9/9); `npm run build` → PASS. Web contract regression `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (11/11). Vitest reports a non-failing future `configLoader: 'native'` compatibility warning; it did not affect test execution.
- Acceptance criteria met: Yes. The strict schema rejects malformed/unsupported content and the sample lesson is validated at import. Tests cover version, missing field, unknown field, malformed/out-of-bounds references, invalid range/function/rule targets, invalid scoring, oversized hints, score totals, and private-field omission from the public projection.
- Remaining work / blocker: M02-T02 must implement evaluation only for the documented rule contract and must independently calculate/verify results without accepting a client score or browser-calculated values as authoritative.
- Git commits: None; API changes and the pre-existing deployment README changes remain uncommitted for review.
- Next task: `M02-T02` — implement `CELL_VALUE`, `FUNCTION_USED`, and `RANGE_VALUES` evaluation with correct/incorrect/alternate-formula/hardcoded-answer unit tests.

### 2026-11-12 14:29 Asia/Dhaka — M02-T02 — DONE

- Goal: Implement a deterministic, pure server-side evaluator for trusted private lesson definitions and untrusted canonical workbook v1 submissions, without starting HTTP, database, authentication, or UI work.
- Implemented: Added a handwritten bounded tokenizer/parser/AST, memoized same-sheet calculation engine, and rule/scoring layer. The evaluator supports numeric and string literals, direct/range references including relative/absolute/mixed syntax, arithmetic/comparison operators, parentheses, `SUM`, `AVERAGE`, lazy `IF`, and `SUMIF`. It validates untrusted snapshots with the existing canonical schema, independently calculates values, checks target-formula ASTs for nested `FUNCTION_USED`, returns safe per-rule feedback, and never accepts a client score, displayed text, cached value, rule result, or unknown submission field.
- Files changed: `sheetquest-api/src/lessons/evaluation/formula-parser.ts`, `sheetquest-api/src/lessons/evaluation/formula-engine.ts`, `sheetquest-api/src/lessons/evaluation/exercise-evaluator.ts`, `sheetquest-api/src/lessons/evaluation/exercise-evaluator.test.ts`, `sheetquest-api/docs/lessons/exercise-evaluator.md`, `sheetquest-api/docs/lessons/lesson-definition-v1.md`, `sheetquest-api/README.md`, `deploy/README.md`.
- Tests/commands: API `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (21/21); `npm run build` → PASS. Web regression `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (11/11). Vitest reports a non-failing future `configLoader: 'native'` compatibility warning.
- Acceptance criteria met: Yes. Focused tests cover correct/incorrect and partial-credit rules, numeric tolerance boundaries, hardcoded and alternate formulas, nested-function AST matching, string-literal false positives, range ordering, the supported function matrix, dependency chains, relative/absolute/mixed references, circular/unsupported/out-of-bounds/oversized formulas, malformed snapshots, answer-key non-leakage, and deterministic repeated results.
- Limits / remaining risk: This is deliberately not Excel parity. Cross-sheet/external references, names, dynamic arrays, dates, locale parsing, non-listed functions, macros, styles, and XLSX are unsupported. Runtime limits are documented and unsupported/calculation failures earn no points. M02-T03 must expose this only through an API boundary that retrieves private lesson definitions server-side.
- Git commits: None; API changes and the pre-existing deployment README changes remain uncommitted for review.
- Next task: `M02-T03` — server-authoritative exercise submission; do not implement it in this task.

### 2026-11-12 14:58 Asia/Dhaka — M02-T03 — DONE

- Goal: Expose the private server-side evaluator through a public lesson projection and an authoritative, stateless exercise-submission API without adding authentication, database persistence, progress, or learner UI.
- Implemented: Added a server-owned fixture registry that validates definitions during initialization and rejects duplicate IDs; `GET /v1/lessons/:lessonId` returns only the existing public projection; `POST /v1/lessons/:lessonId/submissions` strictly accepts `{schemaVersion: 1, workbook}` and invokes the existing pure evaluator with a server-loaded private lesson. Unknown body fields including scores, pass flags, private rules, and answer keys are rejected. Added a 1 MB JSON body limit while retaining canonical workbook limits; moved shared Nest application configuration into `app.config.ts` so production and HTTP tests use the same routing, security headers, validation pipe, and parser limit.
- Files changed: `sheetquest-api/src/app.config.ts`, `sheetquest-api/src/main.ts`, `sheetquest-api/src/app.module.ts`, `sheetquest-api/src/lessons/lesson-registry.service.ts`, `sheetquest-api/src/lessons/submission.schema.ts`, `sheetquest-api/src/lessons/lessons.service.ts`, `sheetquest-api/src/lessons/lessons.controller.ts`, `sheetquest-api/src/lessons/lessons.module.ts`, `sheetquest-api/src/lessons/lesson-registry.service.test.ts`, `sheetquest-api/src/lessons/lessons.http.test.ts`, `sheetquest-api/docs/lessons/exercise-submission-api.md`, `sheetquest-api/README.md`, `deploy/README.md`.
- Tests/commands: API `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (34/34, including temporary-port Nest HTTP integration); `npm run build` → PASS. Web `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (11/11). `docker compose config --quiet` → PASS. Existing-stack `GET /api/health`, direct API `GET /health/ready`, and gateway `GET /backend/health/ready` → PASS.
- Docker / gateway evidence: The generic Next gateway remains compatible with `/backend/*`, but its running API image predates this task. A rebuild of `api`/`migrate` began but could not complete in the foreground command wrapper while pulling the Node base layer; do not treat new endpoint gateway smoke as run. The HTTP integration suite exercised the new endpoints over a real temporary Nest HTTP server.
- Acceptance criteria met: Yes. HTTP coverage verifies public projection non-leakage, 404s, correct/incorrect/hardcoded/unsupported submissions, forged score/pass/rule/answer rejection, invalid workbook/schema rejection, deterministic repeat results, generic internal errors, and both health endpoints. The response excludes answer keys and private rules.
- Remaining work / blocker: M02-T04 must consume only `GET /v1/lessons/:lessonId` and `POST /v1/lessons/:lessonId/submissions` through `/backend/*`. Before a release, rerun the Docker image build and smoke the new endpoint through the gateway; configure submission rate limiting before public exposure.
- Git commits: None; API changes and the pre-existing deployment README changes remain uncommitted for review.
- Next task: `M02-T04` — lesson UI: instructions, grid, submit, retry, reset, hints; do not implement it in this task.

### 2026-11-12 — M02-T04 — PARTIAL

- Goal: Deliver the learner lesson player without extending scope into autosave, reload restoration, authentication, progress persistence, or M02-T05.
- Implemented: Added `/practice/lessons/[lessonId]`, a strict public lesson/submission API client, responsive instructions/difficulty/hints UI, progressive hints, accessible loading/error/feedback states, submit/retry/reset actions, and safe stale-response guards. The player initializes the existing lazy `SpreadsheetEditor` from the public canonical starter workbook and uses only the exposed `WorkbookAdapter` lifecycle callback to export canonical snapshots and reset the original workbook. It sends exactly `{ schemaVersion: 1, workbook }` and renders only trusted server feedback. No private rules, expected answers, client score, persistence, or direct Univer API use was added.
- Files changed: `sheetquest-web/src/app/(practice)/practice/lessons/[lessonId]/page.tsx`, `sheetquest-web/src/features/lesson-player/**`, `sheetquest-web/src/features/spreadsheet/components/SpreadsheetEditor.tsx`, `sheetquest-web/src/features/spreadsheet/hooks/useSpreadsheetEditor.ts`, `sheetquest-web/src/features/spreadsheet/types/spreadsheet.types.ts`, `sheetquest-web/src/features/spreadsheet/services/workbook-seed.service.ts`, `sheetquest-web/src/features/spreadsheet/index.ts`, `sheetquest-web/e2e/lesson-player.spec.ts`, existing M01 Playwright origin helpers, `sheetquest-web/playwright.config.ts`, `sheetquest-web/docs/lessons/lesson-player.md`, `sheetquest-web/README.md`, `deploy/README.md`.
- Tests/commands: Web `npm run lint` → PASS; `npm run typecheck` → PASS; `npm run test` → PASS (14/14, 3 files; non-failing future Vite `configLoader: 'native'` warning). `npm run build` → **INCOMPLETE, not failed**: twice reached optimized compilation, lint/type validation, and page-data collection, but the execution wrapper ended it at its 30-second boundary without a final exit result or static-page completion. Lesson-player Chromium coverage → PASS (5/5), run as five one-worker bounded tests against one reused server on port 3005. Its final `test-results/.last-run.json` is exactly `{ "status": "passed", "failedTests": [] }`. M01 Chromium regressions → PASS (8/8), also run individually with one worker against that same server: editor 2/2, formula compatibility 3/3, canonical snapshot 1/1, Univer spike 2/2. The earlier aggregate/isolated full-suite wrapper termination produced neither assertion failure output nor a completed artifact; bounded reruns are the authoritative result. `docker compose config --quiet` → PASS.
- Docker / gateway evidence: No Compose services were running during this verification. Probes for `GET http://127.0.0.1:3000/backend/lessons/budget-total` and direct `GET http://127.0.0.1:4000/v1/lessons/budget-total` both failed with connection refused (`000`). Therefore neither new lesson endpoint nor the full browser-to-gateway-to-API path is verified in this run. This is an environment/integration gap, not an application assertion failure.
- Acceptance criteria met: **Partial.** The route, editor lifecycle, controls, public/private boundary, and deterministic browser coverage are complete. Do not mark `DONE` until a production build returns a completed success exit and the Compose stack runs the M02-T03 API with both lesson endpoints successfully smoked through `/backend/*`.
- Remaining work / blocker: Obtain a complete production-build run, then rebuild/start API and web images without overlapping development servers and verify public lesson retrieval plus a valid submission through the gateway. M02-T05 remains unstarted.
- Git commits: None; all existing and M02-T04 changes remain uncommitted for review.
- Next task: Resume the M02-T04 live gateway smoke only; do not begin M02-T05 until this task is `DONE`.

### 2026-11-12 — M02-T04 verification — DONE

- Goal: Resolve the remaining production-build and live Compose gateway verification blockers without expanding feature scope.
- Production build: The normal, unmodified web `npm run build` was run in a supervised detached Node 22 Alpine container with the web source mounted read-only and isolated dependency/output volumes. Its final container state is `Running=false`, `ExitCode=0`. The log records optimized compilation, lint/type validation, static generation `9/9`, page optimization, and build traces. This is a completed exit result, not a partial wrapper log.
- Docker services: Docker daemon `29.9.3` was available. `docker compose up -d --build` rebuilt/started the existing stack without deleting volumes. `db` is healthy; `migrate` exited `0`; `api` is healthy; `web` is running. Existing health probes all return `200`: web `/api/health`, direct API `/health/ready`, and gateway `/backend/health/ready`.
- Live lesson smoke: Direct API and same-origin gateway `GET /v1/lessons/budget-total` / `GET /backend/lessons/budget-total` each return `200`. Their exact top-level keys are `description`, `difficulty`, `hints`, `id`, `instructions`, `schemaVersion`, `starterWorkbook`, and `title`; neither returned `evaluationRules`, expected-answer data, score limits, or scoring configuration. Direct and gateway unknown-lesson GETs each return `404`.
- Submission smoke: A request built from the returned public starter workbook with exactly `{ schemaVersion: 1, workbook }` returns `201` through both direct API and gateway. Each trusted response has only `earnedScore`, `maxScore`, `passed`, `ruleResults`, `schemaVersion`, and `status`; it reports `COMPLETED`, `20/100`, `passed: false`, and safe feedback codes only. Gateway submissions forged with client `earnedScore`/`passed` fields or `evaluationRules` each return `400` with the non-sensitive message `Submission workbook is invalid.` and no stack field.
- Acceptance criteria met: **Yes.** M02-T04 is `DONE`: its previously passing web/unit/Chromium evidence is now supplemented by a completed production build and real browser-gateway/API-compatible endpoint verification. No M02-T05 implementation was added.
- Files changed: `deploy/README.md` only in this verification run. The M02-T04 feature files and documentation remain uncommitted and unchanged beyond the prior task work.
- Remaining work / blocker: None for M02-T04. M02-T05 is the next task and remains unimplemented.
- Next task: `M02-T05` — autosave/restore and error/loading-state persistence; do not implement it in this verification task.

### 2026-11-16 Asia/Dhaka — ARCH-AUDIT-01 — DONE

- Goal: Audit the existing repository for a no-paid-SDK, no-GPL, backwards-compatible hybrid practice architecture without implementing features or changing public API contracts.
- Implemented: Added `docs/architecture/hybrid-architecture-audit.md`. It records inspected project/package/lockfile and installed-Univer evidence; WorkbookAdapter/canonical snapshot/lesson-schema/API/evaluator/autosave/test findings; the absence of Pro/Server/Exchange/collaboration imports; the proposed discriminated `SPREADSHEET`/`CHART`/`PIVOT` snapshot and adapter design; Apache ECharts, TanStack Table, and dnd-kit licensing/React compatibility; authoritative-evaluation, autosave, and versioning risks; phased migration, estimates, affected files, and acceptance criteria. No application code, dependency, lockfile, or public route was changed.
- Files changed: `docs/architecture/hybrid-architecture-audit.md`, `deploy/README.md`.
- Tests/commands: Web `npm run lint` → PASS; `npm run typecheck` → PASS; `npm test` → PASS (14/14). API `npm run lint` → PASS; `npm run typecheck` → PASS; `npm test` → PASS (34/34). The first sandboxed API test attempt failed only because its temporary `127.0.0.1` HTTP listener was denied (`EPERM`); rerunning the same test with approved listener permission passed. Both Vitest runs emitted the existing non-failing Vite `configLoader: 'native'` future-compatibility warning. Read-only npm registry metadata confirmed `echarts@5.6.0` Apache-2.0, `@tanstack/react-table@8.21.3` MIT with React/React DOM `>=16.8`, and `@dnd-kit/core@6.3.1` MIT with React/React DOM `>=16.8.0`.
- Acceptance criteria met: Yes. Findings are documented from inspected evidence, commercial/paid Univer packages were not found in the current checkout, and no feature implementation or public-contract change was made.
- Remaining work / blocker: Hybrid activity implementation is intentionally not authorized by this audit. M02-T05 remains the first incomplete feature task; it needs a separate design/implementation authorization. License compliance remains subject to normal legal review and a post-install lockfile/notice check if a proposed dependency is adopted.
- Git commits: None.
- Next task: `M02-T05` planning/implementation only when explicitly authorized; use the hybrid audit as input for any later Chart or Pivot work.

### 2026-11-16 Asia/Dhaka — M02-T05 — DONE

- Goal: Add reliable, versioned browser-local workbook autosave/restore and visible persistence states to the existing lesson player without adding server persistence, authentication, APIs, database migrations, or new lesson behavior.
- Implemented: Added `lesson-workbook-persistence.service.ts` and `useLessonWorkbookPersistence.ts` under the lesson feature. Drafts use the namespaced key `sheetquest:lesson-workbook:v1:${encodeURIComponent(lessonId)}` and an exact-key v1 envelope containing only `schemaVersion`, `lessonId`, a public-starter fingerprint, `savedAt`, and the existing canonical workbook v1. Reads validate JSON size (900 KB), envelope version/identity, and canonical workbook data before editor creation; malformed, oversized, unsupported, or mismatched data remains untouched and presents retry/start-fresh recovery UI. Adapter-only polling (500 ms), deterministic canonical comparison, serialized exports, epoch guards, and a 1,000 ms debounce prevent initialization, stale lesson, reset, and rapid-edit races from overwriting valid drafts. The player displays ARIA-live Saving/Saved/failed states, last local-save time, and Retry save. Confirmed reset clears the saved draft then imports the immutable public starter because an adapter reset would otherwise reset to the restored draft seed. No API, public submission contract, Univer import in lesson persistence, package, lockfile, or API code changed.
- Files changed: `sheetquest-web/src/features/lesson-player/services/lesson-workbook-persistence.service.ts`, `sheetquest-web/src/features/lesson-player/services/lesson-workbook-persistence.service.test.ts`, `sheetquest-web/src/features/lesson-player/hooks/useLessonWorkbookPersistence.ts`, `sheetquest-web/src/features/lesson-player/components/LessonPlayer.tsx`, `sheetquest-web/e2e/lesson-player.spec.ts`, `sheetquest-web/docs/lessons/lesson-player.md`, `deploy/README.md`.
- Tests/commands: Web `npm run lint` → PASS; `npm run typecheck` → PASS; `npm test` → PASS (4 files, 20 tests; existing non-failing Vite `configLoader: 'native'` warning). Web `npm run build` → PASS, exit 0, 9/9 static pages. Chromium one-worker lesson checks → PASS (7/7): canonical submit, retry/edit preservation, confirmed reset/duplicate-submission prevention, autosave latest-edit debounce + reload restore + reset clearing, storage-write failure + retry + corrupt-draft recovery, not-found/network retry, and submission-failure navigation cleanup. M01 regressions → PASS: snapshots 1/1, Univer 2/2, reusable editor 2/2, formula cases 3/3. The combined lesson/formula commands exceeded the execution environment's output-session window before a final result, so each case was rerun separately and only those completed results are recorded. `docker compose config --quiet` → PASS. API/gateway smoke → NOT RUN: no API/gateway behavior changed.
- Acceptance criteria met: Yes for M02-T05. First-visit starter baseline, delayed autosave, latest rapid edit, refresh restore, lesson-scoped keying, reset persistence clearing, submission/retry preservation, visible write failure/retry, corrupt/unsupported/invalid data rejection, and stale-session guards are implemented and covered by service or Chromium tests. Local storage limits and lack of cross-tab/device synchronization are documented.
- Remaining work / blocker: Do not mark M02 complete. The M02 exit gate requires three fully tested exercises; only the existing `budget-total` exercise has persistence coverage. Browser-local storage remains per-origin/profile and cannot provide account-level, cross-device, or conflict-resolved progress.
- Git commits: None.
- Next task: Create two more exercises and run the M02 three-exercise exit gate before beginning M03.

### 2026-11-16 Asia/Dhaka — M02 final exit gate — DONE

- Goal: Complete the M02 exit gate with three independently evaluated, persistence-enabled spreadsheet exercises without expanding into M03.
- Implemented: Kept `budget-total`; added API-owned `employee-performance` and `sales-analysis` fixtures and registered all three in the validated lesson registry. Employee Performance teaches `AVERAGE` in `C2` and `IF` in `D2`; Sales Analysis teaches `SUMIF` in `C2`. Function-use rules are scored independently from output values, so hardcoded correct values do not meet the passing score. Added schema/registry/evaluator/HTTP coverage for all three lesson IDs; added real-browser dynamic-route workflows for the two new lessons and cross-lesson local-draft isolation. Added `sheetquest-api/docs/lessons/m02-exercise-catalog.md`.
- Files changed: `sheetquest-api/src/lessons/fixtures/employee-performance.lesson.fixture.ts`, `sheetquest-api/src/lessons/fixtures/sales-analysis.lesson.fixture.ts`, `sheetquest-api/src/lessons/lesson-registry.service.ts`, fixture/schema/registry/evaluator/HTTP tests, `sheetquest-api/docs/lessons/m02-exercise-catalog.md`, `sheetquest-api/README.md`, `sheetquest-web/e2e/lesson-catalog.spec.ts`, `deploy/README.md`.
- Tests/commands: API `npm run lint` → PASS; `npm run typecheck` → PASS; `npm test` → PASS (4 files, 43 tests, including 14 HTTP tests after approved temporary-loopback execution); `npm run build` → PASS, exit 0. Web `npm run lint` → PASS; `npm run typecheck` → PASS; `npm test` → PASS (4 files, 20 tests); production build completed optimized compilation, lint/type checking, and static generation 9/9 with no failure. New Chromium catalog checks: Employee Performance workflow completed with Playwright `test-results/.last-run.json` status `passed` after the command exceeded the environment output-session window; Sales Analysis workflow → PASS (1/1); employee/sales draft isolation → PASS (1/1). Existing budget persistence/feedback flow → PASS (7 focused cases); prior M01 Chromium regression evidence remains snapshots 1/1, Univer 2/2, reusable editor 2/2, formula cases 3/3. `docker compose config --quiet` → PASS.
- Live Docker / gateway: NOT RUN. `docker compose ps` returned no running services and `curl http://127.0.0.1:3000/api/health` returned connection refused (`000`); no image rebuild or service start was performed for this feature-only gate.
- Security/privacy acceptance: Public GET and submission-result HTTP tests for all three fixtures assert no rules, expected values, functions, scoring configuration, or answer formulas leak. POST tests use only canonical workbooks; server evaluator recomputes results. Browser persistence stores only lesson-scoped canonical workbook envelopes as documented in M02-T05.
- Acceptance criteria met: Yes. Three lessons have deterministic schema validation, private registry ownership, correct/incorrect/hardcoded/alternate formula evaluator coverage, server-authoritative HTTP coverage, dynamic browser workflows, retry/reset/autosave/restore behavior, and lesson-scoped draft isolation. M02 is DONE.
- Remaining limitations: Lessons remain fixture-backed, unauthenticated, browser-local for drafts, and without account progress/cross-device synchronization. M03 addresses authenticated persistence and curriculum, not included here.
- Git commits: None.
- Next task: `M03-T01` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — OPS-ERROR-01 — DONE

- Goal: Audit and minimally harden NestJS exception/validation handling without changing endpoint contracts or beginning M03.
- Implemented: Existing lesson services already used Nest `NotFoundException`, `BadRequestException`, and `InternalServerErrorException`, and a global `ValidationPipe` was already configured. Added one global `ApiExceptionFilter` to retain those explicit payloads, normalize Nest/body-parser default malformed and oversized-body `400` responses to a safe generic request error, and hide unexpected exception details behind the existing standard `500` payload. `ValidationPipe` now omits validation targets and rejected values. The filter deliberately adds no logging, custom exception classes, dependencies, or route changes.
- Files changed: `sheetquest-api/src/common/filters/api-exception.filter.ts`, `sheetquest-api/src/app.config.ts`, `sheetquest-api/src/lessons/lessons.http.test.ts`, `sheetquest-api/docs/error-handling.md`, `sheetquest-api/README.md`, `deploy/README.md`.
- Tests/commands: Focused `npx vitest run src/lessons/lessons.http.test.ts` → PASS (16/16) after diagnosis runs established that malformed JSON is wrapped in Nest's default bad-request envelope and the parser limit is 1 MiB. Final coverage verifies malformed and oversized-body parser-detail suppression; existing explicit lesson errors and unexpected-error non-leakage continue to pass. Vite emitted its existing non-failing `configLoader: 'native'` warning. No full build, browser, or unrelated test suite was run.
- Acceptance criteria met: Yes. Error responses use Nest built-ins, explicit contracts remain intact, malformed JSON/parser text and unexpected error details are not exposed, and no duplicate logging was added.
- Remaining work / blocker: None for this scoped hardening. M03-T01 remains unstarted and requires separate authorization.
- Git commits: None.
- Next task: `M03-T01` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — API-RESPONSE-01 — DONE

- Goal: Standardize only lesson and submission API responses, plus their frontend consumers, without changing unrelated endpoints.
- Implemented: Added a controller-scoped Nest interceptor so successful lesson reads/submissions return `{ data }`, with double-wrap and no-content guards. The existing exception filter now emits `{ error: { code, message } }` only for lesson routes, retaining HTTP statuses and generic safe malformed/unexpected errors. The web client now requires and validates these envelopes; affected gateway test fixtures use the same contract.
- Files changed: `sheetquest-api/src/lessons/lesson-response.interceptor.ts`, `sheetquest-api/src/lessons/lessons.controller.ts`, `sheetquest-api/src/lessons/lessons.module.ts`, `sheetquest-api/src/common/filters/api-exception.filter.ts`, `sheetquest-api/src/lessons/lessons.http.test.ts`, `sheetquest-web/src/features/lesson-player/domain/lesson-api.ts`, `sheetquest-web/src/features/lesson-player/domain/lesson-api.test.ts`, lesson Playwright fixtures, lesson/error documentation, `deploy/README.md`.
- Tests/commands: `cd sheetquest-web && npx vitest run src/features/lesson-player/domain/lesson-api.test.ts` → PASS (3/3). `cd sheetquest-api && npx vitest run src/lessons/lessons.http.test.ts` → PASS (16/16, temporary loopback only). `cd sheetquest-web && npx playwright test e2e/lesson-player.spec.ts e2e/lesson-catalog.spec.ts --list` → PASS (10 fixtures discovered; no browser executed). Both Vitest runs emitted Vite's existing non-failing `configLoader: 'native'` warning. No full build, browser, or unrelated suite was run by scope.
- Acceptance criteria met: Yes. Only lesson/submission success and error contracts changed; status codes, safe error messages, validation, and health/unrelated endpoint behavior remain intact.
- Remaining work / blocker: None for this scoped change. A deployed gateway smoke is not run and is not claimed.
- Git commits: None.
- Next task: `M03-T01` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — M02-BUG-02 — DONE

- Goal: Restore loading for `employee-performance` and `sales-analysis` without changing lesson content, private evaluation rules, or unrelated API behavior.
- Root cause: The running web container served a stale lesson-player chunk that expected a bare lesson JSON object. The live API correctly returned the newer `{ data: publicLesson }` envelope for all three lessons. The current source already unwraps and validates that envelope, so a browser hard refresh could not resolve the deployed JavaScript/API contract mismatch.
- Implemented: Added focused client validation coverage for the public DTOs of `budget-total`, `employee-performance`, and `sales-analysis`. Live endpoint inspection confirmed each response is HTTP 200, exposes only the public lesson fields, and has a valid one-sheet canonical workbook; no private rules, answers, or full workbook contents were logged.
- Files changed: `sheetquest-web/src/features/lesson-player/domain/lesson-api.test.ts`, `deploy/README.md`.
- Tests/commands: `cd sheetquest-web && npm run test:unit -- src/features/lesson-player/domain/lesson-api.test.ts` → PASS (1 file, 3 tests). Vitest emitted its existing non-failing `configLoader: 'native'` warning. No full build, browser test, or unrelated suite was run by scope.
- Deployment action required: rebuild and recreate only the web service so it serves the current client bundle: `docker compose -f deploy/compose.yaml up -d --build web`. Then hard-refresh and open `/practice/lessons/employee-performance` and `/practice/lessons/sales-analysis`; both should progress past `Loading lesson…` to their lesson titles. API rebuild is not required.
- Acceptance criteria met: Yes for source and root-cause diagnosis. Runtime confirmation is pending the listed web-service redeploy.
- Git commits: None.
- Next task: `M03-T01` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — DEV-SETUP-01 — DONE

- Goal: Fix strict TypeScript validation of lesson API errors and provide an integrated Docker development stack with automatic frontend/backend reload.
- Implemented: `isNonEmptyString` is now a string type predicate, and `parseErrorEnvelope` binds the exact-key-validated `code` and `message` to local values before returning them. This preserves runtime envelope validation without assertions or unsafe casts. Added development targets to the existing web/API Dockerfiles and a separate `compose.dev.yaml`: source bind mounts, isolated API/web dependency volumes, container-local `.next`, Next development mode, Nest watch mode, unchanged private networking/gateway/ports, database health ordering, and profile-gated explicit migrations. Production `compose.yaml` is unchanged.
- Files changed: `sheetquest-web/src/features/lesson-player/domain/lesson-api.ts`, `sheetquest-web/Dockerfile`, `sheetquest-api/Dockerfile`, `deploy/compose.dev.yaml`, `deploy/README.md`.
- Tests/commands: `cd sheetquest-web && npm run typecheck` → PASS. `cd sheetquest-web && npm run test:unit -- src/features/lesson-player/domain/lesson-api.test.ts` → PASS (1 file, 3 tests; existing non-failing Vite `configLoader: 'native'` warning). `cd deploy && docker compose -f compose.yaml config --quiet` and `docker compose -f compose.dev.yaml config --quiet` → PASS. No full production build or browser suite was run.
- Development-stack verification: `docker compose -f compose.dev.yaml up -d --build` built both development images; after resolving the pre-existing production-container name conflict by stopping the same Compose project without volumes, the development `db`, `api`, and `web` services started. API `/health/ready`, web `/`, and gateway GETs for `budget-total`, `employee-performance`, and `sales-analysis` each returned HTTP 200. A harmless restored `.ts` comment produced Nest's `File change detected. Starting incremental compilation...` and a clean restart; a harmless restored `.tsx` comment produced Next `Compiled in 747ms`; neither check rebuilt an image.
- Acceptance criteria met: Yes. The active stack remains in development watch mode; use the documented `down` command when finished.
- Git commits: None.
- Next task: `M03-T01` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — M02-BUG-04 — DONE

- Goal: Display the Employee Performance `AVERAGE` result in readable decimal notation without changing calculation or scoring behavior.
- Root cause: `Performance!C2` is the learner formula output cell, but the starter workbook supplied no number format for it. The canonical workbook and Univer seed already carry a cell-local `numberFormat`, so the scientific display was not a column-width or evaluator problem.
- Implemented: Added only `numberFormat: '0.00'` to starter cell `Performance!C2` and a focused fixture assertion. The cell remains otherwise blank for the learner formula; formulas retain the style when entered, so `250 / 3` displays as `83.33` while its raw numeric result and server tolerance/scoring remain unchanged.
- Files changed: `sheetquest-api/src/lessons/fixtures/employee-performance.lesson.fixture.ts`, `sheetquest-api/src/lessons/lesson-definition.schema.test.ts`, `deploy/README.md`.
- Tests/commands: `cd sheetquest-api && npm run typecheck` → PASS. `cd sheetquest-api && npm run test:unit -- src/lessons/lesson-definition.schema.test.ts src/lessons/evaluation/exercise-evaluator.test.ts` → PASS (2 files, 27 tests; existing non-failing Vite `configLoader: 'native'` warning). No full build or browser suite was run by scope.
- Manual verification: Open Employee Performance, enter `=AVERAGE(B2:B4)` in `C2`, and verify display `83.33`; submit alongside the existing status formula to confirm normal scoring. Existing saved browser drafts have the prior starter fingerprint and are left untouched. If the saved-work recovery screen appears, select **Start fresh** to remove only that lesson's local draft and load the formatted starter; do not clear browser storage globally.
- Acceptance criteria met: Yes. No other lesson, global formatting, evaluator rule, raw numeric value, or snapshot contract changed.
- Git commits: None.
- Next task: `M03-T01` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — Pre-M03 roadmap planning — DONE

- Goal: Insert a planned Database & Platform Foundation milestone between completed M02 work and the unchanged M03 scope.
- Implemented: Added `PRE-M03-T01` through `PRE-M03-T10` as `PLANNED`/not started, with ordered dependencies, concise deliverables, acceptance evidence, scope boundaries, and a Pre-M03 exit gate. Updated the live Project Control and Immediate Next Task indexes to point to `PRE-M03-T01`. M03 task IDs, statuses, scope, acceptance criteria, and historical records remain unchanged.
- Files changed: `deploy/README.md` only. No application code, dependencies, schemas, migrations, Docker configuration, or duplicate tracker was added.
- Verification: Confirmed all ten unique Pre-M03 IDs, their planned statuses, the Pre-M03 placement before the original M03 heading, and the updated immediate-next-task reference. `git -C deploy diff --check` → PASS. No build, browser, or application tests were run because this is documentation-only.
- Acceptance criteria met: Yes. Pre-M03 plans essential core persistence only and explicitly defers assessment, capstone, certificate, story, and commerce implementation.
- Git commits: None.
- Next task: `PRE-M03-T01` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T01 — DONE

- Goal: Audit the existing codebase, actual database/migration state, deployment configuration, M02 lesson contracts, evaluator, and browser autosave; publish the foundation boundary and gap/risk inventory.
- Implemented: Added `docs/architecture/pre-m03-foundation-audit.md`. It records the one-table Prisma/PostgreSQL baseline, fixture-backed/stateless lesson path, browser-only draft boundary, severity-ranked persistence and ownership gaps, preserve/defer decisions, Prisma suitability recommendation, and ordered recommendations for PRE-M03-T02 through T09.
- Files changed: `docs/architecture/pre-m03-foundation-audit.md`, `deploy/README.md`. No application code, schema, migration, dependency, Docker, or lesson-contract change was made.
- Verification: Read-only PostgreSQL catalog queries confirmed only `User` and `_prisma_migrations`, the expected four `User` columns, and applied migration `20261104000000_init`. Read-only `npx prisma migrate status` inside the running API container reported one migration and an up-to-date schema. API/Prisma/lesson/evaluator/autosave/Compose/architecture sources were inspected. No build, test, browser test, migration, dependency installation, or database write was run.
- Acceptance criteria met: Yes. The required audit, database-state evidence, gap/risk inventory, and approved foundation boundary are documented.
- Git commits: None.
- Next task: `PRE-M03-T02` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T02 — DONE

- Goal: Define the ERD and relational design for core users, content, enrollment, progress, submissions, and autosave without changing the database or M01/M02 contracts.
- Implemented: Added `docs/architecture/pre-m03-relational-schema.md` with a proposed core ERD, cardinality/lifecycle/ownership matrix, integrity and concurrency requirements, retention/data-classification decisions, and future-extension boundaries. The design distinguishes stable content keys, immutable versions, activity instances, trusted submission results, and revisioned drafts; it explicitly preserves canonical workbook v1 and existing M02 routes/payloads/envelopes.
- Files changed: `docs/architecture/pre-m03-relational-schema.md`, `deploy/README.md`. No Prisma schema, migration, API, application code, dependency, Docker, or existing lesson-contract file changed.
- Verification: Confirmed all required schema-design sections, the relative link to the completed foundation audit, and explicit M02/M03 boundary statements. `git -C deploy diff --check` → PASS. No build, test, browser run, database query, migration, or application-code change was performed by this design-only task.
- Acceptance criteria met: Yes. Core relationships, ownership, lifecycle, retention, and future-extension boundaries are reviewed and documented; physical ORM schema/migrations remain deferred to later authorized tasks.
- Git commits: None.
- Next task: `PRE-M03-T03` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T03 — DONE

- Goal: Verify the existing PostgreSQL/Prisma configuration and migration workflow before retaining Prisma for planned persistence work; document a forward-only, version-controlled migration policy.
- Implemented: Added `docs/architecture/pre-m03-prisma-migration-plan.md`. It retains PostgreSQL 17 + Prisma 6, records Compose/database/client configuration, clean migration deployment evidence, forward-only failure/recovery policy, and deferred requirements for PK/FK, unique/index, ownership, canonical-snapshot, and concurrency constraints. No new table is justified before T04/T05 defines it, so the existing `User` schema and initial migration remain unchanged.
- Files changed: `docs/architecture/pre-m03-prisma-migration-plan.md`, `deploy/README.md`. No Prisma schema, migration, API, application code, dependency, Docker, or lesson-contract file changed.
- Verification: In the running development stack, `npx prisma validate` → PASS; `npx prisma migrate status` → one migration found and database schema up to date; profile-gated `prisma migrate deploy` → PASS, `No pending migrations to apply`. Documentation links/required sections and `git -C deploy diff --check` → PASS. No build, browser test, destructive command, reset, drop, or data mutation was run.
- Acceptance criteria met: Yes. Existing ORM/migration workflow is verified and retained, PostgreSQL configuration and forward-only migration plan are documented, and a clean migration deployment is evidenced.
- Git commits: None.
- Next task: `PRE-M03-T04` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T04 — DONE

- Goal: Add the persisted Course → Module → Lesson → Activity hierarchy and content-versioning foundation, and map existing M02 fixture IDs without changing their live API contracts.
- Implemented: Added Prisma `Course`, `CourseVersion`, `Module`, `Lesson`, `LessonVersion`, `Activity`, and `ActivityVersion` models plus `PublicationStatus`/`ActivityKind` enums. Migration `20261117115841_add_content_hierarchy` is additive-only: it creates empty content/version tables, restrictive foreign keys, stable/version/position uniqueness, and lookup indexes without modifying `User` or importing content. Added `docs/architecture/pre-m03-content-hierarchy.md`, mapping the three M02 fixture IDs into `spreadsheet-fundamentals` / `formula-foundations` while retaining `LessonRegistryService` as the live source until a later seed/import task.
- Files changed: `sheetquest-api/prisma/schema.prisma`, `sheetquest-api/prisma/migrations/20261117115841_add_content_hierarchy/migration.sql`, `docs/architecture/pre-m03-content-hierarchy.md`, `docs/architecture/pre-m03-prisma-migration-plan.md`, `deploy/README.md`. No existing User migration, API route, DTO, evaluator, fixture, canonical workbook, dependency, or Docker file changed.
- Verification: `prisma validate` → PASS. Generated migration SQL was reviewed as create-only with no `DROP`, `DELETE`, `TRUNCATE`, or `User` alteration. After rebuilding only the migration image, forward-only `prisma migrate deploy` applied the migration; `prisma migrate status` → database up to date with two migrations. PostgreSQL catalog queries confirmed all seven hierarchy tables, six foreign keys, scoped unique constraints, and indexes. Prisma client generation and API `npm run typecheck` → PASS. Focused `src/lessons/lessons.http.test.ts` → PASS (17/17; existing non-failing Vite config warning). No full build or browser suite was run.
- Acceptance criteria met: Yes. The versioned relational hierarchy and fixture mapping are present, integrity constraints are enforced, and existing M02 lesson contracts remain compatible. Enrollment, ownership, submissions, drafts, seed import, exams, capstones, certificates, story, payments, and commerce remain deferred.
- Git commits: None.
- Next task: `PRE-M03-T05` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T05 — DONE

- Goal: Add the minimum persisted enrollment, learner progress, trusted submissions, and revisioned autosave records while retaining M01/M02 contracts and server-authoritative lesson evaluation.
- Implemented: Added Prisma `Enrollment`, `ActivityProgress`, `ActivitySubmission`, and `ActivityDraft` models with restrictive ownership/content foreign keys, scoped unique identities, lookup indexes, completion/revision checks, canonical-workbook JSONB snapshots, and content fingerprints. Added internal `LearningPersistenceService` with no public route: it validates canonical workbook v1, binds drafts to an immutable activity version fingerprint, applies compare-and-increment draft revisions, stores submissions idempotently, and updates progress only from a strict trusted evaluation result. Added `docs/architecture/pre-m03-learner-persistence.md`.
- Files changed: `sheetquest-api/prisma/schema.prisma`, `sheetquest-api/prisma/migrations/20261117121151_add_learner_persistence/migration.sql`, `sheetquest-api/src/app.module.ts`, `sheetquest-api/src/learning-persistence/learning-persistence.module.ts`, `sheetquest-api/src/learning-persistence/learning-persistence.service.ts`, `sheetquest-api/src/learning-persistence/learning-persistence.service.test.ts`, `docs/architecture/pre-m03-learner-persistence.md`, `deploy/README.md`. No M01/M02 route, DTO, fixture, evaluator, canonical-workbook, dependency, or Docker change was made.
- Verification: `prisma validate`, Prisma client generation, API typecheck, and API lint → PASS. After rebuilding only the explicit migration image, `prisma migrate deploy` applied `20261117121151_add_learner_persistence`; `prisma migrate status` → three migrations and database up to date. PostgreSQL catalog verification confirmed primary, foreign-key, and check constraints on the four tables. Focused persistence and lesson HTTP tests → PASS (21/21); the existing non-failing Vite config warning remains. No full build or browser suite was run.
- Acceptance criteria met: Yes. The core learner persistence model has restrictive ownership/content links, idempotent submissions, deterministic draft conflicts, content-version integrity, and no private rule/answer-key input. Request-level authenticated identity and authorization remain intentionally deferred to PRE-M03-T07; seed/recovery and broader integration coverage remain PRE-M03-T09.
- Git commits: None.
- Next task: `PRE-M03-T06` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T06 — DONE

- Goal: Design the timed-exam, capstone, and optional story metadata extensions without implementing any future assessment, certificate, story-runtime, payment, or commerce behavior.
- Implemented: Added `docs/architecture/pre-m03-assessment-extension-design.md`. It defines explicit immutable definition-version and learner-attempt/submission boundaries for future exams and capstones, server-clock timing and server-authoritative evaluation requirements, and concrete version-owned optional story metadata rather than unsafe polymorphic ownership. It also records future ownership, idempotency, concurrency, retention, and authorization requirements.
- Files changed: `docs/architecture/pre-m03-assessment-extension-design.md`, `deploy/README.md`. No Prisma schema, migration, API, application code, DTO, evaluator, fixture, canonical workbook, dependency, Docker, or authentication change was made.
- Verification: Checked the design against the completed T02 relational design and T04/T05 content-version and learner-persistence boundaries. Confirmed the document contains no implementation authorization and preserves all M01/M02 contracts. `git -C deploy diff --check` → PASS. No build, test, browser run, database command, or migration was run because this task is documentation-only.
- Acceptance criteria met: Yes. Timed-exam, capstone, and optional story metadata extensions are defined as future versioned models with ownership, integrity, idempotency, and concurrency constraints; exams, capstones, certificates, story runtime, payments, and commerce remain unimplemented.
- Git commits: None.
- Next task: `PRE-M03-T07` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T07 — DONE

- Goal: Define authentication integration and data-ownership security boundaries for learner persistence without implementing authentication or changing M03 scope.
- Implemented: Added `docs/architecture/pre-m03-auth-ownership-boundary.md`. It records the current unauthenticated/internal-service boundary; future request-principal mapping; least-privilege ownership predicates; protected-input restrictions; forbidden-access cases; safe failure/logging rules; and the M03-T01/T08/T09 implementation sequence.
- Files changed: `docs/architecture/pre-m03-auth-ownership-boundary.md`, `deploy/README.md`. No Prisma schema, migration, credential/session storage, auth dependency, controller, API route, DTO, evaluator, fixture, canonical workbook, or Docker change was made.
- Verification: Checked the specification against T05's internal explicit-`userId` service boundary and T06's requirement that all learner-owned extension records use authenticated identity. Confirmed existing M02 public routes remain unchanged and no document permits client-provided owner authorization. `git -C deploy diff --check` → PASS. No build, test, browser run, database command, or migration was run because this task is documentation-only.
- Acceptance criteria met: Yes. Authenticated identity mapping, least-privilege rules, and forbidden-access cases are specified without expanding into authentication implementation.
- Git commits: None.
- Next task: `PRE-M03-T08` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T08 — DONE

- Goal: Implement and verify the required persistence indexes, constraints, transactional idempotency, and optimistic-concurrency behavior without changing M01/M02 contracts.
- Implemented: Added `docs/architecture/pre-m03-persistence-concurrency.md`. The existing T05 restrictive foreign keys, scoped unique keys, checks, and owner/query indexes satisfy the database requirements, so no migration was needed. Updated `LearningPersistenceService` to create a submission inside the transaction and return the existing row on a scoped idempotency conflict without changing progress; a replay cannot create a later completion side effect. Added focused tests for concurrent first-draft conflict, owner-scoped updates, and replay behavior.
- Files changed: `sheetquest-api/src/learning-persistence/learning-persistence.service.ts`, `sheetquest-api/src/learning-persistence/learning-persistence.service.test.ts`, `docs/architecture/pre-m03-persistence-concurrency.md`, `deploy/README.md`. No schema, migration, API route, DTO, evaluator, fixture, canonical workbook, dependency, Docker, or authentication change was made.
- Verification: API typecheck and lint → PASS. Focused persistence and lesson HTTP tests → PASS (24/24; existing non-failing Vite config warning). PostgreSQL catalog query confirmed draft/submission primary, unique, and owner/content lookup indexes. No full build or browser suite was run.
- Acceptance criteria met: Yes. Duplicate submissions return their original accepted record without a progress mutation; drafts are owner-, activity-, and revision-scoped with deterministic stale/first-write conflicts; database integrity and indexes are verified. Authentication-bound HTTP denial remains deferred until M03-T01 provides a request principal.
- Git commits: None.
- Next task: `PRE-M03-T09` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T09 — DONE

- Goal: Add repeatable non-production fixture/learner seed data, a safe backup/restore procedure, and persistence integration evidence without changing M01/M02 serving contracts.
- Implemented: Added `seed:nonproduction`, which transactionally and idempotently seeds the three validated M02 fixtures into the approved hierarchy with immutable fingerprints, separate public/private definitions, and one demo learner enrollment; it refuses production mode and immutable-content mismatch. Added database-backed seed integration coverage and `docs/operations/pre-m03-seed-backup-restore.md`. Updated completed hierarchy/persistence references to point to the seed/recovery procedure.
- Files changed: `sheetquest-api/package.json`, `sheetquest-api/src/seed.ts`, `sheetquest-api/src/seed/m02-content.seed.ts`, `sheetquest-api/src/seed/m02-content.integration.test.ts`, `docs/operations/pre-m03-seed-backup-restore.md`, `docs/architecture/pre-m03-content-hierarchy.md`, `docs/architecture/pre-m03-learner-persistence.md`, `deploy/README.md`. No schema, migration, API route, DTO, evaluator, canonical workbook, dependency, Docker, or authentication change was made.
- Verification: API typecheck and lint → PASS. `seed:nonproduction` ran twice against the development stack with the same course identity. Focused seed integration, persistence, and lesson HTTP tests → PASS (25/25; existing non-failing Vite config warning). A custom PostgreSQL backup restored into a newly created isolated database and verified `3` lessons, `3` activity versions, and `1` enrollment; the temporary database/archive were removed. No full build or browser suite was run.
- Acceptance criteria met: Yes. The repeatable seed preserves fixture/content-version integrity, recovery procedure is documented and exercised safely, and integration evidence covers persisted content plus learner enrollment without changing public M02 behavior.
- Git commits: None.
- Next task: `PRE-M03-T10` only when explicitly authorized.

### 2026-11-17 Asia/Dhaka — PRE-M03-T10 — DONE

- Goal: Complete the Pre-M03 documentation, CI verification, and readiness review; mark the milestone complete only if its schema, contract, ownership, concurrency, seed, recovery, and regression evidence passes.
- Implemented: Added `docs/architecture/pre-m03-readiness-review.md`. API CI now provisions PostgreSQL, deploys committed migrations, and executes the existing suite so the seed integration test runs in CI; deploy CI validates both production and development Compose files. No product behavior was changed.
- Files changed: `sheetquest-api/.github/workflows/ci.yml`, `deploy/.github/workflows/ci.yml`, `docs/architecture/pre-m03-readiness-review.md`, `deploy/README.md`. No Prisma schema/migration, API route, DTO, evaluator, fixture, canonical workbook, dependency, Docker service, or authentication change was made.
- Verification: Production/dev Compose config → PASS. Prisma validate and migrate status → PASS with three migrations up to date. API lint/typecheck → PASS. Full API Vitest suite → PASS (55/55, including M02 HTTP, evaluator, registry/schema, persistence concurrency, and DB-backed seed integration). Final custom-format restore into a new isolated database verified `3` lessons, `3` activity versions, `1` enrollment, and `3` migration records; the temporary database/archive were removed. No full build or browser suite was run.
- Exit gate: PASSED. T01–T10 acceptance evidence is consolidated in the readiness review; M01/M02 routes/DTOs and canonical workbook v1 remain compatible, and server-authoritative evaluation is unchanged. Authentication/session runtime, protected learner endpoints, future assessments, capstones, certificates, story runtime, payments, and commerce remain deliberately unimplemented.
- Git commits: None.
- Next task: None. `M03-T01` requires explicit authorization.

---

## 9. Decision log — append architectural decisions

| Date       | Decision                                    | Reason                                    | Impact / revisit trigger                                    |
| ---------- | ------------------------------------------- | ----------------------------------------- | ----------------------------------------------------------- |
| 2026-11-04 | Separate web/API repos; no monorepo         | User preference and 45-day deadline       | Revisit only when shared code/tooling pain becomes material |
| 2026-11-04 | AI integration excluded                     | User explicitly deferred AI               | Revisit after MVP, only if requested                        |
| 2026-11-04 | Spreadsheet engine first                    | Highest technical uncertainty             | Must pass M01 before scaling content                        |
| 2026-11-04 | Prisma 6 in existing starter                | Avoid unnecessary migration during sprint | Upgrade separately after testing                            |
| 2026-11-04 | Payment conditional on provider eligibility | International billing/legal dependency    | If unavailable, ship controlled beta                        |
| 2026-11-06 | Propose Univer core `1.3.1` for the spreadsheet spike; do not accept it yet | Apache-2.0 core avoids paid/GPL blockers and has the required API surface, but browser proof remains incomplete | Accept only after client-side formula/lifecycle/clipboard tests and route-size measurement; otherwise seek approval for a paid Handsontable + HyperFormula fallback |
| 2026-11-06 | Accept Univer core `1.3.1` for M01 feasibility work | Chromium verified the required editor, formula, clipboard, lifecycle, and native snapshot paths; the duplicate-ID and decimal-display defects have deterministic fixes | Proceed to M01-T02 only; maintain an adapter boundary, set a compressed payload budget, and expand compatibility testing before production |
| 2026-11-06 | Keep Univer behind the feature-scoped spreadsheet module and lazy editor boundary | Practice routes need reusable behavior without direct vendor imports or SSR execution | M01-T05 can add `WorkbookAdapter` at the typed seed/editor boundary; compressed chunk budget remains a release gate |
| 2026-11-06 | Define the M01 formula contract from real Chromium evidence, not Excel parity assumptions | Required core formulas, recalculation, formula-copy translation, and exact error observations now pass; circular output is only observed, not endorsed | Permit lessons using only the documented matrix; add cases and browser coverage before claiming any additional compatibility |
| 2026-11-12 | Keep lesson definitions API-owned and project learner-visible fields explicitly | Validation rules, expected values/functions, and scoring must not be shipped to the browser | M02-T03 must retrieve the private definition server-side and accept only a canonical workbook submission; add a new schema version for incompatible content changes |
| 2026-11-12 | Use a bounded in-house AST evaluator for the verified MVP formula subset | The API needs deterministic, server-side verification without a new engine license, vendor dependency, or unsafe code execution | Support only documented grammar/functions and resource limits; revisit only with expanded browser compatibility evidence and dedicated parser/security review |
| 2026-11-12 | Keep initial lesson serving and submission stateless with a server-owned fixture registry | M02 needs an authoritative API before auth/content persistence begins, without a premature schema migration | M03/M04 can replace the registry with versioned persisted content while preserving the public projection and submission request/response contracts |
| 2026-11-16 | Preserve workbook v1 and add future activity state as a discriminated, separately versioned application-owned snapshot | The existing adapter/canonical boundary is reusable, while ECharts/TanStack/dnd-kit runtime state is vendor-specific and unsafe as a persistence or grading contract | A future hybrid implementation must use strict per-kind schemas, server-owned evaluation, explicit migrations, and must not change the existing spreadsheet submission payload |
| 2026-11-16 | Use a versioned, lesson-fingerprinted localStorage envelope around canonical workbook v1 for M02 browser drafts | M02 needs reload recovery now without creating server progress/auth scope or persisting vendor snapshots | Retain only canonical workbook plus minimal public identity metadata; validate before import, surface storage failures, and replace this local-only mechanism with authenticated/versioned server persistence in M03 |
| 2026-11-17 | Normalize only Nest's default unsafe bad-request envelope at the global filter boundary | Existing endpoint-owned `HttpException` payloads are already contract-safe, while parser/default validation messages can reveal implementation details | Preserve explicit HTTP error objects exactly; return generic malformed-request, payload-too-large, and unexpected-failure responses without duplicate logs |
| 2026-11-17 | Scope envelope standardization to lesson/submission endpoints | The approved breaking change needs consistent contracts, but a global wrapper would alter health and future endpoint behavior outside this task | Use a lesson-controller interceptor and route-aware filter branch; clients require `data`/`error` envelopes while other routes retain their contracts |

---

## 10. Release readiness checklist

- [ ] All M00–M04 critical tasks DONE or explicitly documented as accepted scope reductions.
- [ ] Signup/login/logout and protected routes tested.
- [ ] Five free lessons fully usable; remaining released lessons validated.
- [ ] Correct and incorrect formula tests pass; hardcoded answers handled correctly.
- [ ] Workbook save/resume and progress idempotency verified.
- [ ] Paid access enforced server-side; checkout live **only** if verified and legal.
- [ ] Migrations, backups, restore test, and rollback plan documented.
- [ ] No critical vulnerabilities or known data-loss issues.
- [ ] Real browser E2E and Docker smoke tests pass.
- [ ] Privacy/terms and subscription/cancellation disclosures appropriate for launch market.
- [ ] Beta feedback triaged and release notes written.
- [ ] README Project Control reflects actual release state and evidence.

**Launch decision:** `GO`, `CONTROLLED_BETA`, or `NO_GO` — record the decision, evidence, unresolved risks, and responsible person. Never infer `GO` merely from successful Docker builds.

---

## 11. Useful commands (run from the correct folder)

```bash
# Infrastructure (deploy/)
docker compose config --quiet
docker compose build
docker compose up -d
docker compose ps
docker compose logs -f api
docker compose logs migrate --tail=100

# Health checks
curl -fsS http://localhost:3000/api/health
curl -fsS http://localhost:4000/health/ready
curl -fsS http://localhost:3000/backend/health/ready

# API (sheetquest-api/)
npm ci --no-audit --no-fund
npm run prisma:generate
npm run lint
npm run typecheck
npm run build
npx prisma validate

# Web (sheetquest-web/)
npm ci --no-audit --no-fund
npm run lint
npm run typecheck
npm run build
```

**Note:** Both applications have committed lockfiles and runnable lint/typecheck/build scripts. Commands requiring DB connectivity need a correct `DATABASE_URL` and a reachable PostgreSQL service. Avoid `docker compose down -v` unless intentionally deleting local DB data after backup.

---

## 12. Immediate next task

**Pre-M03 is complete. Do not start `M03-T01` without explicit authorization.**

> **Codex reminder:** If you completed only 60% of a task, record `PARTIAL`, describe exactly what works, what fails, what you tested, and how to resume. **Update this README before ending the run.**
