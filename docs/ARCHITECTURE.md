# Architecture

Decisions behind this document are recorded in `docs/adr/`. This file describes the system;
ADRs explain why.

## 1. Services

```
                ┌────────────── apps/web (Next.js) ──────────────┐
                └───────────────────────┬────────────────────────┘
                                        │ HTTPS (session cookie)
                              apps/api (NestJS, public)
                                        │
          Postgres (RLS) ◄──────────────┼──────────────► Redis (queues)
                                        │
            ┌───────────────────────────┼────────────────────────────┐
            ▼                           ▼                            ▼
   apps/worker (TS)              services/ai (Python)        services/media (Python)
   publish · webhooks ·          agents: writer, classifier,  transcribe · clip ·
   token refresh · metrics ·     replier, strategist, trend   scene detect · thumbnails
   Remotion render               → returns PROPOSALS only     → returns ARTIFACTS (R2)
            │
            ▼
   packages/platforms → Meta · LinkedIn · TikTok · YouTube APIs
```

- Only `apps/api` and the webhook receiver in `apps/worker` are internet-facing.
- `services/ai` and `services/media` are reachable only on the private network.
- Media lives in Cloudflare R2; services exchange object keys, never raw bytes over queues.

## 2. Dependency rules (enforced by dependency-cruiser / import-linter in CI)

- `apps/*` may import `packages/*`. `packages/*` never import `apps/*`.
- `apps/web` never imports `packages/db`, `packages/crypto`, or `packages/platforms`.
- Only `apps/worker` imports `packages/crypto` decrypt functions and `packages/platforms`.
- `services/*` import only generated contracts (`socialhub_contracts`) and their own code.
- No service reads another service's tables. Cross-service data goes through contracts.

## 3. Data flow examples

**Generate + publish a post**
1. web → api `POST /v1/posts/drafts` (zod-validated, Idempotency-Key).
2. api enqueues `ai.generate-post` → services/ai returns `PostProposal` (structured output).
3. api stores draft; autonomy level decides: queue for approval or schedule.
4. At schedule time api enqueues `publish.post` → worker decrypts token, runs guardrails,
   calls adapter `publish()`, records result + audit log.

**Incoming comment**
1. Platform webhook → worker verifies signature → dedupe by platform event ID.
2. worker enqueues `ai.classify-comment` → services/ai returns `CommentClassification`.
3. If reply needed: `ai.propose-reply` → `ReplyProposal` with confidence.
4. Autonomy level + guardrails decide: auto-send via worker, or put in inbox for approval.

## 4. Queues

- Name format: `<domain>.<action>` in kebab case, e.g. `publish.post`, `ai.classify-comment`,
  `media.transcribe`.
- Every job payload is a contract in `packages/contracts/src/jobs/` with a `version` field.
- Jobs are idempotent (safe to retry). Retries: exponential backoff, max attempts per queue,
  then dead-letter queue + alert.
- Per-platform, per-account rate limiters live in the worker, not in adapters' callers.

## 5. Platform adapter interface

Every adapter in `packages/platforms/src/<platform>/` implements exactly this interface
(source of truth: `packages/platforms/src/types.ts`):

```ts
interface PlatformAdapter {
  readonly platform: Platform;
  readonly capabilities: PlatformCapabilities; // publish, comments, dms, analytics, mediaTypes…
  publish(ctx: AdapterContext, input: PublishInput): Promise<PublishResult>;
  replyToComment(ctx: AdapterContext, input: ReplyInput): Promise<ReplyResult>;
  fetchComments(ctx: AdapterContext, input: FetchCommentsInput): Promise<Page<PlatformComment>>;
  fetchMetrics(ctx: AdapterContext, input: FetchMetricsInput): Promise<MetricsSnapshot>;
  refreshToken(ctx: AdapterContext): Promise<TokenRefreshResult>;
  verifyWebhook(req: RawWebhookRequest): WebhookVerification;
}
```

Unsupported operations throw `CapabilityNotSupportedError`; the UI hides them via `capabilities`.

## 6. Data

- Postgres 16+, Drizzle ORM, migrations via drizzle-kit only.
- IDs: UUIDv7. Time: `timestamptz`, stored UTC. Money: integer minor units + currency code.
- Every tenant-owned table: `tenant_id uuid not null`, RLS enabled + forced, policy on
  `current_setting('app.tenant_id')`. The API sets it per transaction.
- Soft delete only where the spec requires it; otherwise hard delete + audit log entry.

## 7. Observability

- OpenTelemetry traces across TS and Python; trace ID propagated through queue job metadata.
- Structured JSON logs (pino / structlog) with `tenantId`, `traceId`; secrets/PII redacted.
- Sentry for errors. Metrics: queue depth, job latency, platform API errors, AI tokens per tenant.
