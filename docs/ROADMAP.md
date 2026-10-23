# 🗺️ Social Hub Roadmap

**65 build phases + 1 business track.** Each phase is small, ships something that works,
and is tested before the next one starts.

Live status of every phase → `docs/PROGRESS.md`

<br>

## How to read the numbers

| Field | Meaning |
|---|---|
| **Size** | **S** small · **M** medium · **L** large |
| **Time** | Working days with AI agents building and you reviewing |
| **Tokens** | Total tokens processed by Claude Code for the phase (spec + build + review + fixes) |
| **Cost** | API-equivalent cost on Claude Opus 5.5 at ≈ **$1.5 per 1M tokens** (≈88% cached reads). On a Max subscription you pay a flat fee; tokens count against usage limits instead |
| **Needs** | Phases that must be ✅ first |
| ⇄ | Can run in parallel with the stage before it |

All numbers are estimates (±30%). Re-estimate after Stage A using real usage.

<br>

## At a glance

| Stage | Phases | Days | Tokens | Cost |
|---|---|---|---|---|
| 🧱 A · Foundation | 01–12 | 23 | 36.5M | ~$55 |
| 🔐 B · Accounts & access | 13–17 | 11 | 17.5M | ~$26 |
| 🖥️ C · Web app | 18–19 | 6.5 | 11M | ~$17 |
| 🔌 D · Connect & publish | 20–30 | 30 | 49M | ~$74 |
| 🧠 E · AI core | 31–36 | 17 | 28.5M | ~$43 |
| 💬 F · Engagement | 37–44 | 24 | 39.5M | ~$59 |
| 📈 G · Intelligence | 45–49 | 17 | 28.5M | ~$43 |
| 🎬 H · Media & video ⇄ | 50–54 | 16 | 27M | ~$41 |
| 💳 I · Business ⇄ | 55–59 | 14.5 | 23.5M | ~$35 |
| 🚀 J · Launch | 60–65 | 22.5 | 22M | ~$33 |
| **Total** | **65** | **≈ 181** | **≈ 283M** | **≈ $425** |

**Calendar time:** ≈ 8 months if every phase runs one after another (5-day weeks).
≈ **6–7 months** if Stages H and I run alongside F and G, and the four platform phases (27–30) run side by side.

<br>

## Timeline

```
Month        1         2         3         4         5         6         7
             |---------|---------|---------|---------|---------|---------|---------|
A Foundation ██████████
B Access               █████
C Web app                   ███
D Connect                      ██████████
E AI core                                ████████
F Engage                                         ███████████
G Intel                                                     ████████
H Media ⇄                                        ███████
I Business ⇄                                            ███████
J Launch                                                            ██████████
                                        ▲ M1          ▲ M2                   ▲ M3
```

M1 first real Facebook post (P26) · M2 MVP beta (P40) · M3 public launch (P65).
Scale: one block ≈ 2 working days, on the faster plan with parallel tracks.

<br>

## Rules for every phase

1. Start only when every phase in **Needs** is ✅.
2. **Exit gate** before moving on:
   every "Done when" is proven by a test or demo · CI green · deployed to staging (from P10) ·
   any new kind of code has its reference + generator · docs updated · git tag `phase-NN`.
3. A phase that grows past ~1 week is split, not stretched.
4. Never build something from a later phase early.

<br>

---

## 🏢 Phase 00 · Business track (parallel, non-code)

> Get every platform approval and legal basic in place while the code is being built.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| — | runs all project | ~0 | $0 | — |

**Build:** Company registered · Domain and business email · Privacy policy and terms published · Meta business verification · Meta app review for Facebook and Instagram · LinkedIn Community Management API approval · TikTok Content Posting API audit · YouTube API quota extension

**Done when:** every platform approval is granted, or has a known date.

<br>

---

# 🧱 Stage A · Foundation

*The ground everything stands on. Boring, and the most important stage.*

<br>

### Phase 01 · Repo & monorepo

> One repository that holds every app and service.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1 day | ~1.5M | ~$2 | — |

**Build:** Git repository with protected main · Yarn 4 workspaces · Turborepo · Node 22 and Python 3.12 pinned · uv workspace for Python

**Done when:** a fresh clone runs `yarn install` and `yarn build` with no errors.

<br>

### Phase 02 · Quality gates

> Every file is formatted and type-checked the same way, automatically.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1 day | ~1.5M | ~$2 | 01 |

**Build:** ESLint and Prettier · ruff and mypy strict · `yarn verify` runs TS and Python checks · Pre-commit hooks with gitleaks

**Done when:** a badly formatted or badly typed file is rejected before commit.

<br>

### Phase 03 · CI pipeline

> Every pull request is checked by machines before a human looks.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1–2 days | ~2M | ~$3 | 02 |

**Build:** PR checks with build caching · Semgrep and CodeQL scans · Dependency audit · `yarn progress` script and check

**Done when:** a PR with a lint error, a leaked secret or a stale PROGRESS.md fails CI.

<br>

### Phase 04 · Local dev environment

> One command starts the whole system on your laptop.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1–2 days | ~2M | ~$3 | 01 |

**Build:** Docker Compose with Postgres, Redis, S3 storage and mail catcher · Typed config for TS that fails fast · Typed config for Python that fails fast · `yarn dev` starts everything · `.env.example` documented

**Done when:** a new developer is running locally in under 15 minutes.

<br>

### Phase 05 · Contracts & codegen

> One definition of every data shape, shared by TypeScript and Python.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 2–3 days | ~4M | ~$6 | 02 |

**Build:** `packages/contracts` with zod · zod to JSON Schema generation · JSON Schema to Pydantic generation · Shared AppError codes in both languages · CI drift check

**Done when:** changing a schema without regenerating fails CI.

<br>

### Phase 06 · Database foundation

> Postgres with safe, repeatable migrations.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1–2 days | ~2M | ~$3 | 04 |

**Build:** Drizzle ORM setup · Migration pipeline · Separate migrator and app database roles

**Done when:** migrations run up from an empty database in CI and locally.

<br>

### Phase 07 · Tenant isolation

> The database itself refuses to mix one customer's data with another's.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 2–3 days | ~4M | ~$6 | 05, 06 |

**Build:** Row-level security framework · Tenant context helper · Tables tenants, users and memberships · Cross-tenant test harness

**Done when:** tests prove tenant A can never read or write tenant B's rows.

<br>

### Phase 08 · API skeleton

> The public API, with safe defaults built in from the first endpoint.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 05, 07 |

**Build:** NestJS on Fastify with health checks · problem+json errors · Rate limiting and security headers · Idempotency-Key support · Reference endpoint with full tests

**Done when:** the reference endpoint passes contract, error and rate-limit tests.

<br>

### Phase 09 · Observability

> When something breaks, we can see where and why.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1–2 days | ~2.5M | ~$4 | 08 |

**Build:** Structured logs with secret redaction · OpenTelemetry traces in TS and Python · Sentry error tracking

**Done when:** one request can be followed from API to database in a single trace.

<br>

### Phase 10 · Staging & continuous deploy

> Every merge goes live on a staging server automatically.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 2–3 days | ~4M | ~$6 | 08, 09 |

**Build:** Dockerfiles with pinned base images · Staging environment · Secret manager · Migrations in the deploy pipeline · Auto-deploy on merge · Tested rollback

**Done when:** a merge to main is live on staging in minutes, and rollback works.

<br>

### Phase 11 · Generators

> New code starts from the same skeleton every time, whoever writes it.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1–2 days | ~3M | ~$5 | 08 |

**Build:** `yarn gen` engine · Generator for API modules · Generator for database tables

**Done when:** a generated module passes `yarn verify` with no hand edits.

<br>

### Phase 12 · Queues & workers

> Background jobs that run across TypeScript and Python, with retries.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 05, 09 |

**Build:** BullMQ setup · Versioned job contracts · Retries, dead-letter queue and alert · Python worker consumer · Trace across TS and Python · Reference job and worker-job generator

**Done when:** a job sent from TS runs in Python and back, visible in one trace.

<br>

---

# 🔐 Stage B · Accounts & access

*People can sign up, log in safely, and only see what their role allows.*

<br>

### Phase 13 · Authentication

> Sign up, log in, reset password.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 08 |

**Build:** Signup with email verification · Login with secure sessions · Password reset · Rate limits on auth endpoints

**Done when:** end-to-end tests cover every auth flow, including abuse cases.

<br>

### Phase 14 · Two-step login & passkeys

> Accounts stay safe even if a password leaks.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 2–3 days | ~4M | ~$6 | 13 |

**Build:** Authenticator app codes · Passkeys · Recovery codes · Required for owners and admins

**Done when:** an owner cannot reach the dashboard without a second factor.

<br>

### Phase 15 · Workspaces & invites

> A business gets its own workspace and brings its team.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1–2 days | ~2.5M | ~$4 | 13 |

**Build:** Create and switch workspaces · Email invites · Accept and revoke invites

**Done when:** an invited person joins the right workspace and nothing else.

<br>

### Phase 16 · Roles & permissions

> Owner, admin, editor, approver, viewer, each with clear limits.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 2–3 days | ~4M | ~$6 | 15 |

**Build:** Five roles · Permission guard on every endpoint · CI check for unguarded endpoints · Role matrix tests

**Done when:** every role/action combination has a passing test.

<br>

### Phase 17 · Audit log

> A permanent record of who did what, human or AI.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1–2 days | ~2M | ~$3 | 16 |

**Build:** Append-only audit writer · Audit log API · Retention policy

**Done when:** audit entries cannot be edited or deleted by the app.

<br>

---

# 🖥️ Stage C · Web app

*The dashboard shell, matching `docs/mockup/index.html`.*

<br>

### Phase 18 · Web shell

> Log in and land in a working dashboard.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 14, 16 |

**Build:** Next.js app · Auth pages · Dashboard layout and navigation · Workspace switcher · Generated API client · Mobile bottom tab bar

**Done when:** a browser test signs up, creates a workspace and invites a member.

<br>

### Phase 19 · Design system

> One component library so every screen looks like the mockup.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–4 days | ~6M | ~$9 | 18 |

**Build:** `packages/ui` with mockup colors and fonts · Light and dark themes · Core components · UI page generator · Bangla and English interface

**Done when:** the Overview screen is built only from library components.

<br>

---

# 🔌 Stage D · Connect & publish

*Connect all five platforms and publish to them.*

<br>

### Phase 20 · Token vault

> Platform logins are encrypted and only the publisher can use them.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 2–3 days | ~4M | ~$6 | 07, 10 |

**Build:** Envelope encryption with KMS · Local KMS for development · Key rotation · social_accounts table · Architecture test that only the worker can decrypt

**Done when:** tokens are unreadable in the database and in every log.

<br>

### Phase 21 · Platform adapter framework

> One shape that every platform plugs into.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 12, 20 |

**Build:** Adapter interface and capability flags · Per-account rate limiter · Recorded-fixture test harness · Platform adapter generator

**Done when:** a fake adapter passes the full adapter test suite.

<br>

### Phase 22 · Facebook connect

> Connect a Facebook Page in two clicks.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 21 |

**Build:** Facebook login flow · Automatic token refresh · Disconnect and revoke · Data-deletion callback · Accounts screen

**Done when:** a real test Page connects, refreshes and disconnects cleanly.

<br>

### Phase 23 · Media library

> Upload photos and videos safely.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 2–3 days | ~4M | ~$6 | 10, 16 |

**Build:** Upload to object storage · File type and size checks · Malware scan · Signed URLs on a separate domain · Media library screen

**Done when:** a disguised malicious file is rejected in tests.

<br>

### Phase 24 · Posts & composer

> Write a post by hand, with a version for each platform.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–4 days | ~6M | ~$9 | 19, 22, 23 |

**Build:** Drafts API as the reference module · Per-platform versions · Platform limit validation · Composer screen

**Done when:** a post with media saves with valid versions for every platform.

<br>

### Phase 25 · Scheduler & calendar

> Pick a time, see it on the calendar.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 24 |

**Build:** Calendar screen · Scheduling with Bangladesh time zone · Reschedule and cancel · Per-platform posting limits

**Done when:** scheduled posts show correctly across time zones and days.

<br>

### Phase 26 · Facebook publishing 🏁 M1

> **Milestone 1: the first real automated post.**

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 2–3 days | ~4.5M | ~$7 | 25 |

**Build:** Publish job · Guardrails v1 with caps and duplicate check · Status tracking and retries · Live post on a real Facebook Page

**Done when:** a scheduled post appears on a real Page at the right minute.

<br>

### Phase 27 · Instagram

> Images, carousels and reels.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 26 |

**Build:** Connect Instagram Business account · Image posts · Carousels · Reels · Verified on a test account

**Done when:** all three formats publish to a test account.

<br>

### Phase 28 · LinkedIn

> Personal profile now, company page after approval.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 2 days | ~3M | ~$5 | 26 |

**Build:** Profile posting · Company page behind approval flag · Verified on a test profile

**Done when:** a post appears on a test profile; page posting switches on with the flag.

<br>

### Phase 29 · YouTube

> Upload videos and Shorts within the daily quota.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~4.5M | ~$7 | 26 |

**Build:** Video upload · Quota accounting per day · Private-until-verified handling · Verified on a test channel

**Done when:** an upload works and running out of quota is handled without errors.

<br>

### Phase 30 · TikTok

> Publish videos, and hide what TikTok doesn't allow.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 2 days | ~3M | ~$5 | 26 |

**Build:** Video publishing · Private-until-audit handling · Hide comments and DMs in the UI · Verified on a test account

**Done when:** a video publishes and the UI shows only supported features.

<br>

---

# 🧠 Stage E · AI core

*The AI team gets a brain, a budget and a quality check.*

<br>

### Phase 31 · AI service foundation

> A private Python service that talks to Claude safely.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 12 |

**Build:** Internal FastAPI service · Claude client with per-agent model config · Structured outputs into contracts · Prompt files with untrusted-data wrapping

**Done when:** a test agent returns validated output, and a hostile input stays treated as data.

<br>

### Phase 32 · AI budgets

> Every AI call is counted and capped per customer.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 1–2 days | ~2.5M | ~$4 | 31 |

**Build:** Token and cost metering per tenant · Monthly budgets with hard stop · Prompt cache hit tracking

**Done when:** a tenant over budget gets a clear refusal and an alert.

<br>

### Phase 33 · Evals & reference agent

> Measure AI quality before any prompt change ships.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 31 |

**Build:** Eval framework · Comment classifier reference agent · Classifier eval set in Bangla and English · AI agent generator

**Done when:** the classifier reaches the target score and a prompt change runs evals in CI.

<br>

### Phase 34 · Brand Brain setup

> Teach the AI who the business is.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 19, 31 |

**Build:** Onboarding wizard · Voice profile · Product catalog with prices · FAQs and banned words

**Done when:** a new customer completes setup in under 10 minutes.

<br>

### Phase 35 · Brand Brain retrieval

> The AI finds the right brand facts for each task.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 2 days | ~3M | ~$5 | 34 |

**Build:** pgvector search · Brand context builder · Cached brand context verified

**Done when:** repeated calls show cache hits and the right facts are retrieved.

<br>

### Phase 36 · AI writer & atomizer

> One idea becomes a post for every platform, in Bangla and English.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| L | 4–5 days | ~8M | ~$12 | 24, 33, 35 |

**Build:** Writer agent · One idea to five platform versions · Bangla and English output · Composer integration · Approval flow at L0 and L1 · Writer eval set passing

**Done when:** generated drafts pass evals and an approved draft publishes.

<br>

---

# 💬 Stage F · Engagement

*Comments and messages answered, leads turned into orders.*

<br>

### Phase 37 · Webhooks

> Comments arrive within seconds, verified and never duplicated.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~4.5M | ~$7 | 22, 27 |

**Build:** Meta webhook receiver · Signature verification · Duplicate and replay protection

**Done when:** a replayed or forged webhook is rejected in tests.

<br>

### Phase 38 · Comment polling & inbox data

> Platforms without webhooks are checked on a schedule.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 2 days | ~3M | ~$5 | 37, 28, 29 |

**Build:** YouTube comment polling · LinkedIn comment polling · Unified inbox data model

**Done when:** comments from all supported platforms land in one table.

<br>

### Phase 39 · Unified inbox

> Every comment and message in one place.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 38 |

**Build:** Inbox screen · Filters · Thread detail · Manual reply sent through the worker

**Done when:** a human reply from the inbox appears on the real post.

<br>

### Phase 40 · AI classify & reply drafts 🏁 M2

> **Milestone 2: MVP beta. The AI drafts replies and you approve them.**

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–4 days | ~6M | ~$9 | 33, 36, 39 |

**Build:** Classification of every comment · Reply proposals with confidence · One-tap approve and send · First beta customers onboarded

**Done when:** a beta customer approves an AI reply and it posts.

<br>

### Phase 41 · Guardrails v2

> Nothing unsafe goes out, even if the AI is tricked.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 40 |

**Build:** Link allowlist · Price must match catalog · Personal data scrub · Toxicity check · Prompt-injection test suite

**Done when:** every attack in the injection suite is blocked.

<br>

### Phase 42 · Autopilot

> Customers choose how much the AI does alone.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 41 |

**Build:** Levels L2 and L3 · Confidence thresholds · Per-tenant caps · Emergency stop · Autopilot screen

**Done when:** each level behaves as set, and the emergency stop halts everything within seconds.

<br>

### Phase 43 · Direct messages

> Reply to Messenger and Instagram DMs.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 42 |

**Build:** Messenger DMs · Instagram DMs · 24-hour reply window handling

**Done when:** a DM is answered inside the window, and is never sent outside it.

<br>

### Phase 44 · Leads & orders

> A "দাম কত?" comment becomes a confirmed order.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–4 days | ~6M | ~$9 | 43 |

**Build:** Lead detection · Order capture in DM · Leads board · Export and outbound webhook · Phone confirmation hook

**Done when:** comment → public reply → DM → order record works end to end.

<br>

---

# 📈 Stage G · Intelligence

*The system measures results and gets better every week.*

<br>

### Phase 45 · Analytics data

> Collect results from all five platforms.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 27, 28, 29, 30 |

**Build:** Metric fetch jobs per platform · Analytics tables · Backfill of past posts

**Done when:** every published post has reach and engagement numbers.

<br>

### Phase 46 · Analytics dashboards

> See what's working at a glance.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 45 |

**Build:** Reach chart · Best-time heatmap · Top posts table · Export to CSV

**Done when:** the Analytics screen matches the mockup with real data.

<br>

### Phase 47 · Learning loop

> The writer learns what works for each customer.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| L | 4–5 days | ~8M | ~$12 | 36, 46 |

**Build:** Post feature extraction · Performance patterns · Writer preferences per tenant · Eval proof of improvement

**Done when:** evals show learned drafts beat baseline drafts.

<br>

### Phase 48 · Strategist & weekly report

> Next week's plan, ready for one-click approval.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 47 |

**Build:** Strategist agent · Weekly report via Batch API · Approve-week flow

**Done when:** "Approve week" schedules every planned post correctly.

<br>

### Phase 49 · Trends & crisis radar

> Spot opportunities, and stop posting when things go wrong.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–4 days | ~5.5M | ~$8 | 40, 46 |

**Build:** Trend scout agent · Competitor signals from public data · Negative spike detection · Auto-pause and alert

**Done when:** a simulated spike pauses posts and alerts the owner.

<br>

---

# 🎬 Stage H · Media & video ⇄

*Runs in parallel with Stages F and G.*

<br>

### Phase 50 · Media worker

> A Python service for heavy video and image work.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 2 days | ~3M | ~$5 | 12, 23 |

**Build:** `services/media` with ffmpeg · Storage input and output · Reference media job and generator

**Done when:** a sample video is processed and stored by a queued job.

<br>

### Phase 51 · Transcripts & subtitles

> Every video gets Bangla and English subtitles.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 50 |

**Build:** Speech-to-text in Bangla and English · Subtitle files · Burned-in subtitle option

**Done when:** a Bangla test video gets accurate, synced subtitles.

<br>

### Phase 52 · Clip finder

> One long video becomes the best short clips.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| L | 4–5 days | ~8M | ~$12 | 51 |

**Build:** Scene detection · Moment scoring · Vertical crop · Video studio screen

**Done when:** a 10-minute video becomes three ranked, subtitled shorts.

<br>

### Phase 53 · Brand video templates

> Branded videos without editing software.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–4 days | ~6M | ~$9 | 52 |

**Build:** Remotion templates · Render workers · Template picker in the studio

**Done when:** a product spotlight video renders with the brand's colors and price.

<br>

### Phase 54 · AI images & video

> Generate new visuals from a sentence.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 53 |

**Build:** AI image provider · AI video provider · Credit metering

**Done when:** generation works and credits are deducted exactly once.

<br>

---

# 💳 Stage I · Business ⇄

*Runs in parallel with Stages F and G.*

<br>

### Phase 55 · International payments

> Customers abroad pay by card.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~4.5M | ~$7 | 16 |

**Build:** Paddle or Lemon Squeezy checkout · Plans and prices · Billing webhooks

**Done when:** upgrade, downgrade, cancel and failed-payment flows pass tests.

<br>

### Phase 56 · Bangladesh payments

> bKash, Nagad and local cards.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 55 |

**Build:** SSLCommerz integration · bKash and Nagad · Monthly renewal reminders

**Done when:** a local payment activates a plan in sandbox.

<br>

### Phase 57 · Plan limits

> Each plan gets exactly what it pays for.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 2 days | ~3M | ~$5 | 55, 32 |

**Build:** Usage meters · Limit enforcement · Upgrade prompts

**Done when:** hitting a limit shows a clear upgrade path and nothing breaks.

<br>

### Phase 58 · Agency mode

> Agencies run many clients from one login.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–4 days | ~6M | ~$9 | 57 |

**Build:** Multi-client workspaces · Client approval links · White-label reports

**Done when:** an agency manages three clients with separate approvals.

<br>

### Phase 59 · Admin & abuse tools

> Stop bad actors before they get the app banned.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 42, 57 |

**Build:** Internal admin panel · One-click tenant stop · Content moderation queue · Audited support access

**Done when:** an abusive tenant is stopped in one click, and every admin action is logged.

<br>

---

# 🚀 Stage J · Launch

*Make it production-strong, prove it's secure, then open the doors.*

<br>

### Phase 60 · Compliance & data lifecycle

> Customers control their data.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~5M | ~$8 | 44, 57 |

**Build:** Retention jobs · Data export · Full account deletion · GDPR request handling

**Done when:** a deletion request removes data everywhere, with proof.

<br>

### Phase 61 · Production infrastructure

> The real servers, rebuilt from code, with tested backups.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–4 days | ~5M | ~$8 | 10 |

**Build:** Infrastructure as code · Production environment · Backups with point-in-time recovery · Restore drill passed

**Done when:** production is rebuilt from scratch and a backup restore succeeds.

<br>

### Phase 62 · Reliability

> Know about problems before customers do.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3 days | ~4.5M | ~$7 | 61 |

**Build:** Alerts and on-call · Load test · Runbooks · Public status page

**Done when:** the load test meets targets and every alert has a runbook.

<br>

### Phase 63 · Security audit

> An outside team tries to break in.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 3–5 days (+ tester's calendar) | ~3M | ~$5 | 62 |

**Build:** External penetration test · Fix all high and critical findings · Retest passed

**Done when:** no open high or critical findings.

<br>

### Phase 64 · Private beta

> Ten real businesses use it every day.

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| M | 1–2 weeks (calendar) | ~3M | ~$5 | 63 |

**Build:** Onboard ten customers · Feedback loop · Help docs · Fix top issues

**Done when:** beta customers use it weekly and the top issues are fixed.

<br>

### Phase 65 · Public launch 🏁 M3

> **Milestone 3: anyone can sign up and pay.**

| Size | Time | Tokens | Cost | Needs |
|---|---|---|---|---|
| S | 2 days | ~1.5M | ~$2 | 64 |

**Build:** Public signup · Pricing page · Launch announcement · Paying customers onboard without help

**Done when:** a stranger signs up, connects accounts and pays without talking to you.
