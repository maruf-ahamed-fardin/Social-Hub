# Social Hub — Product Plan

Status: living document · Owner: product owner · Last updated: 2026-10-24

## 1. What we are building

A multi-tenant SaaS that acts as an **AI social media team**: it plans, writes, designs,
publishes, replies, and learns — with a human approval layer where risk is high.

Priority platforms: **Facebook Pages, Instagram (Business/Creator), LinkedIn, TikTok, YouTube.**

## 2. The AI "team"

| Agent | Job | Runs in |
|---|---|---|
| Strategist | Weekly content plan from goals, analytics, trends | services/ai |
| Writer | One idea → platform-specific posts (Bangla + English) | services/ai |
| Designer | Images, carousels, video drafts in brand style | services/ai + services/media + apps/worker (Remotion) |
| Community manager | Classify and reply to comments/DMs | services/ai (proposes) → apps/worker (executes) |
| Trend scout | Niche/competitor/trend signals | services/ai |
| Analyst | Performance → feedback into Strategist/Writer | services/ai |

## 3. Automation boundaries (product rule, not a suggestion)

| Allowed (automate) | Requires 1-tap human approval | Never build |
|---|---|---|
| Publish/schedule on connected accounts; reply to comments on own posts; answer inbound DMs; analytics | Any engagement on content we don't own | Fake accounts, scraping against ToS, browser-bot posting, mass auto-follow/like/DM |

Only official platform APIs. No browser automation of platform UIs.

## 4. Autonomy levels (per tenant, per account, per action type)

- **L0** draft only · **L1** draft + 1-tap approve · **L2** auto when confidence ≥ threshold,
  else queue for approval · **L3** fully automatic within guardrails.
- Defaults: new content L1; FAQ/price replies L2; complaints and crisis L1 forever.

## 5. Platform capability matrix (verify against current docs before building an adapter)

| | Publish | Comments | DMs | Gate |
|---|---|---|---|---|
| Facebook Pages | yes | read + reply | Messenger (24h window) | Meta business verification + app review |
| Instagram | yes | read + reply | yes | same Meta app |
| LinkedIn | member: yes; org: approval | org pages after approval | no | Community Management API approval |
| TikTok | yes, private until audit | no public reply API | no | Content Posting API audit |
| YouTube | yes, private until verified | reply | no | quota extension + audit (upload ≈1,600 of 10,000 units/day) |

The UI must read capabilities from the adapter and hide what a platform can't do.

## 6. Key features

1. Brand Brain: voice, products/catalog, FAQs, banned words, top past posts (RAG).
2. Composer + content atomizer (one input → many platform assets).
3. Calendar + scheduler with per-platform rate limits.
4. Unified inbox with AI classification (question / lead / complaint / spam / toxic).
5. Comment → lead → DM → order capture → CRM (optional phone confirmation integration).
6. Learning loop: post features + performance → generation preferences.
7. Crisis radar: negative-sentiment spike pauses schedule + alerts.
8. Weekly AI report with one-click week approval.
9. Agency mode: many client workspaces, approvals, white-label.

## 7. AI cost policy

- Classification: Claude Haiku 5.5. Writing: Claude Sonnet 5.5. Strategy/reports: Claude Opus 5.5
  via Batch API. Model IDs live in config, never hardcoded.
- Rules/templates before LLM calls. Prompt-cache the Brand Brain. Per-tenant monthly AI budget.
- Video generation is metered credits.

## 8. Phases

The build is split into 65 small, shippable phases in ten stages, each with time, token and cost estimates — see `docs/ROADMAP.md`.
Milestones: M1 first real Facebook post (phase 26) · M2 MVP beta (phase 40) · M3 public launch (phase 65).

**Start now, in parallel (Phase 00):** company registration, Meta business verification + app
review, LinkedIn CMA application, TikTok audit, YouTube quota extension.
