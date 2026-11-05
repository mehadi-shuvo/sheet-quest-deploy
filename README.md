# SheetQuest — 45-Day Build Plan & Codex Operating Manual

> **Single source of truth for development, QA, and progress.** This document is written for a solo developer using Codex. Codex must **read it before starting any task and update it after every task attempt**, including partial progress, blockers, failed tests, and changes in scope.
>
> **Product:** Career-focused, browser-based Excel/spreadsheet learning SaaS for students, job seekers, and office professionals. **AI tutor is explicitly out of scope.**
>
> **Deadline:** 45 calendar days from the date development officially begins. Record that date in the Project Control table. Do not claim the entire 96-lesson vision can be built in 45 days. Ship a reliable, paid-ready MVP or a controlled beta if payment-provider onboarding is blocked.

## 0. Project control — update on every Codex run

| Field | Current value |
|---|---|
| Project | SheetQuest |
| Status | `IN_PROGRESS — FOUNDATION` |
| Start date | `2026-11-04` |
| Target date | `2026-12-18` |
| Current milestone | `M00 — Verify existing Docker starter` |
| Current task | `M00-T04 — Document architecture, environment variables, and workflows` |
| Last verified result | `Canonical roadmap moved into the deploy repository; web/API documentation directs contributors to this master file.` |
| Last update | `2026-11-06 — Canonical roadmap location established; M00-T04 remains in progress.` |
| Next action | `Document the architecture, environment-variable contract, and local/production workflow; then connect and push the three repositories.` |
| Known blockers | `None. GitHub Actions will run after the repositories are pushed to GitHub.` |

**Status vocabulary:** `TODO`, `IN_PROGRESS`, `PARTIAL`, `BLOCKED`, `DONE`, `DEFERRED`. A task is `DONE` **only after** its acceptance criteria and required checks pass. A file existing is not proof a feature works.

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

### Starter hardening tasks (do not assume already implemented)

- Generate and commit `package-lock.json` in **each** app; use `npm ci` in Docker builds for deterministic installs.
- Verify backend Docker runtime includes required Prisma CLI/runtime artifacts for the `migrate` service.
- Check frontend gateway `/backend/*` routing and security; restrict forwarded headers and enforce auth at API.
- Ensure environment validation and secrets management; use `.env.example` without real secrets.
- Add test scripts and CI. Inspect existing Dockerfile stages before editing them.
- Run production image smoke tests and record results.

---

## 3. Technical decisions

| Area | Decision | Rationale |
|---|---|---|
| Repositories | Separate `sheetquest-web`, `sheetquest-api`, `deploy` | Familiar workflow; no monorepo learning overhead |
| Frontend | Next.js App Router + TypeScript | SEO, learner UI, deployment |
| UI | Tailwind CSS + shadcn/ui (add when needed) | Accessible, maintainable components |
| Backend | NestJS modular monolith | Strong domain separation without distributed complexity |
| Database | PostgreSQL 17 + Prisma 6 in current starter | Transactional progress, relational content |
| API | Versioned REST + OpenAPI | Mobile-ready, stable contracts |
| Spreadsheet | **Time-boxed library spike**; test Univer first | Highest technical risk; do not assume full Excel compatibility |
| Validation | Rule-based evaluator with server-authoritative checks | No AI dependency, deterministic scoring |
| Frontend state | TanStack Query for server data; Zustand only if editor state warrants it | Avoid redundant global state |
| Local infra | Docker Compose | Reproducible setup |
| Deployment | Dockerized API, web container or managed Next hosting, managed DB when practical | Simple scaling path |
| Storage | PostgreSQL JSONB for small snapshots; object storage later | Avoid premature infrastructure |
| Auth | Secure HttpOnly cookies / server-side sessions or rotating tokens, documented threat model | Do not store sensitive tokens in localStorage |
| Payments | Provider chosen only after eligibility verification | Avoid illegal/unusable integrations |

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

| ID | Status | Task | Acceptance / proof |
|---|---|---|---|
| M00-T01 | DONE | Audit current checkout; fix Prisma schema if needed; boot Docker stack | `docker compose build`, `up`, migrations, 3 health checks pass; logs recorded |
| M00-T02 | DONE | Lock dependencies and Docker build reproducibility | Both lockfiles and `npm ci` builds verified; committed in the newly initialized application repositories |
| M00-T03 | DONE | Setup Git repos, ignore files, lint/typecheck scripts, CI skeleton | Three Git repositories are clean on `main`; lint/typecheck run locally; GitHub Actions workflows run on PR after push |
| M00-T04 | PARTIAL | Document architecture, env variables, local vs production workflows | Canonical README is in `deploy`; architecture/workflow documentation remains to be completed and commands must be tested |

**Exit gate:** Existing starter builds and runs; no more foundation blockers.

### M01 — Spreadsheet feasibility spike (Days 4–7) — highest priority

| ID | Status | Task | Acceptance / proof |
|---|---|---|---|
| M01-T01 | TODO | Compare spreadsheet libraries and commercial licensing | Decision Log entry with tradeoffs and license notes |
| M01-T02 | TODO | Render editable workbook in Next.js client component | Cell selection/editing, copy/paste, reset work in browser |
| M01-T03 | TODO | Support SUM, AVERAGE, IF, SUMIF and references | Correct calculations verified by tests and manual smoke |
| M01-T04 | TODO | Export/import canonical workbook snapshot | Reload restores cells/formulas without loss |
| M01-T05 | TODO | Define `WorkbookAdapter` and document unsupported features | No vendor-specific calls scattered through lesson UI |

**Exit gate:** A single practice exercise works end to end with real spreadsheet editing. If blocked >2 days by library limitations, narrow features or switch library with recorded rationale.

### M02 — Exercise evaluator and lesson player (Days 8–14)

| ID | Status | Task | Acceptance / proof |
|---|---|---|---|
| M02-T01 | TODO | Versioned lesson JSON schema + Zod validation | Invalid content rejected; sample lesson validated |
| M02-T02 | TODO | Implement `CELL_VALUE`, `FUNCTION_USED`, `RANGE_VALUES` rules | Unit tests: correct, incorrect, alternate formula, hardcoded answer |
| M02-T03 | TODO | Server-authoritative exercise submission | Server recomputes/verifies; client score cannot be forged |
| M02-T04 | TODO | Lesson UI: instructions, grid, submit, retry, reset, hints | One learner can finish lesson without manual intervention |
| M02-T05 | TODO | Autosave/restore, error/loading states | Reload/resume works; failed save is visible |

**Exit gate:** Three fully tested exercises with feedback, retries, persistence.

### M03 — Auth, curriculum and progress (Days 15–21)

| ID | Status | Task | Acceptance / proof |
|---|---|---|---|
| M03-T01 | TODO | User registration, login, logout, session protection | Auth tests; secure cookies; invalid login handled |
| M03-T02 | TODO | Course → module → lesson models, migrations, APIs | Seeded course visible; migration deploy succeeds |
| M03-T03 | TODO | Exercise attempts, completion, mastery, XP | Repeated submission does not duplicate XP |
| M03-T04 | TODO | Onboarding and learner dashboard | Goal saved; continue lesson and progress displayed |
| M03-T05 | TODO | Authorization: learner ownership and admin permissions | Forbidden access tests pass |

**Exit gate:** Signed-in learner can complete and resume a course lesson; backend persists verified progress.

### M04 — Content production and basic admin (Days 22–30)

| ID | Status | Task | Acceptance / proof |
|---|---|---|---|
| M04-T01 | TODO | Publish first five free lessons | All five pass content QA and automated validator cases |
| M04-T02 | TODO | Add 10–15 paid lessons, prioritizing fundamentals | Total 15–20 usable lessons, or record justified reduced scope |
| M04-T03 | TODO | Mini project: personal budget workbook | Rubric, starter dataset, feedback, save/resume work |
| M04-T04 | TODO | Content seed/import, preview, lesson versioning | Can publish content without editing React components |
| M04-T05 | TODO | Basic achievements and XP display | XP awarded once; progress is accurate |

**Exit gate:** Complete free journey + meaningful paid content, no broken lessons.

### M05 — Monetization, security, QA (Days 31–39)

| ID | Status | Task | Acceptance / proof |
|---|---|---|---|
| M05-T01 | TODO | Free vs paid lesson authorization | Unpaid users cannot access paid content through API |
| M05-T02 | TODO | Verify payment-provider eligibility and choose release path | Decision Log: real checkout OR controlled beta |
| M05-T03 | TODO | If eligible: checkout, verified webhooks, subscriptions | Webhook signature, replay, cancellation, failure tests pass |
| M05-T04 | TODO | E2E flows and regression suite | Sign up → free lesson → save → resume → paywall tested |
| M05-T05 | TODO | Security and accessibility baseline | Input validation, rate limits, safe logs, keyboard/focus review |
| M05-T06 | TODO | Production ops: backups, restore rehearsal, monitoring | Restore tested; logs/alerts work; secrets excluded |

**Exit gate:** Safe, testable paid beta OR explicitly labeled no-payment beta. No fake production checkout.

### M06 — Release and feedback (Days 40–45)

| ID | Status | Task | Acceptance / proof |
|---|---|---|---|
| M06-T01 | TODO | Staging deployment + smoke tests | Fresh DB migration, API/web health, lesson completion pass |
| M06-T02 | TODO | 10–30 external beta testers | Feedback/issues captured; high-severity bugs triaged |
| M06-T03 | TODO | Fix release-blocking bugs, performance regressions | No known critical auth/payment/data-loss defects |
| M06-T04 | TODO | Production release or controlled beta, release notes | Live URL verified by human; rollback documented |
| M06-T05 | TODO | Post-launch plan based on real usage | Retention, completion, failed exercises, conversion tracked |

**Exit gate:** A real user can complete the primary learning journey on the deployed app. Record what is still missing.

### Post-MVP roadmap — do not start before M06 without approval

| Phase | Status | Scope |
|---|---|---|
| P1 | DEFERRED | Expand to 96 lessons, advanced functions, role-based tracks |
| P2 | DEFERRED | Three major capstones: budget, retail sales, operations/forecasting |
| P3 | DEFERRED | Timed exams, verified platform certificates, advanced achievements |
| P4 | DEFERRED | Shortcut arena, skill analytics, adaptive rule-based revision |
| P5 | DEFERRED | Community Q&A, moderation, teams, organization seats |
| P6 | DEFERRED | React Native companion app, after web metrics validate demand |

---

## 5. Content blueprint — first five lessons

| # | Lesson | Objective | Validation focus | Access |
|---|---|---|---|---|
| 1 | Navigation & Cells | Edit cells, select ranges, recognize addresses | Workbook state | Free |
| 2 | SUM & AVERAGE | Compute totals and averages | Value + required function | Free |
| 3 | Formatting | Format a basic professional worksheet | Formatting state | Free |
| 4 | IF Logic | Use conditional logic | Output + IF usage | Free |
| 5 | Personal Budget Challenge | Combine formulas in a small budget | Multi-rule workbook rubric | Free |

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
- Git commits (if any): Pending for this documentation move.
- Next task: Complete M00-T04 documentation in this master README.

---

## 9. Decision log — append architectural decisions

| Date | Decision | Reason | Impact / revisit trigger |
|---|---|---|---|
| 2026-11-04 | Separate web/API repos; no monorepo | User preference and 45-day deadline | Revisit only when shared code/tooling pain becomes material |
| 2026-11-04 | AI integration excluded | User explicitly deferred AI | Revisit after MVP, only if requested |
| 2026-11-04 | Spreadsheet engine first | Highest technical uncertainty | Must pass M01 before scaling content |
| 2026-11-04 | Prisma 6 in existing starter | Avoid unnecessary migration during sprint | Upgrade separately after testing |
| 2026-11-04 | Payment conditional on provider eligibility | International billing/legal dependency | If unavailable, ship controlled beta |

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
npm install
npm run typecheck
npm run build
npx prisma validate
npx prisma generate

# Web (sheetquest-web/)
npm install
npm run build
```

**Note:** Starter may not yet contain lint/test scripts or committed lockfiles; add and verify these in `M00-T02` and `M00-T03`. Commands requiring DB connectivity need a correct `DATABASE_URL` and a reachable PostgreSQL service. Avoid `docker compose down -v` unless intentionally deleting local DB data after backup.

---

## 12. Immediate next task

**`M00-T04`: Complete architecture, environment-variable, and local-versus-production workflow documentation in this master README.** Test each documented command, record evidence in the Execution Log, then update the task status and Project Control.

> **Codex reminder:** If you completed only 60% of a task, record `PARTIAL`, describe exactly what works, what fails, what you tested, and how to resume. **Update this README before ending the run.**
