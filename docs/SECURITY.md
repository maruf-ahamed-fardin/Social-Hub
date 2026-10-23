# Security rules (non-negotiable)

Any PR that breaks a rule here is rejected regardless of other merit. Changes to this file
need owner approval.

## 1. Tenant isolation
- Every tenant-owned table has `tenant_id`, RLS enabled **and forced**.
- App DB roles never have `BYPASSRLS`. Migrations run with a separate role.
- Every new tenant table ships with a test proving tenant A cannot read/write tenant B.

## 2. Social OAuth tokens
- Stored only encrypted (envelope encryption: per-tenant data key wrapped by KMS).
- Decrypted only in `apps/worker`, in memory, for the duration of one platform call.
- Never logged, never in error messages, never sent to web, api responses, or Python services.
- Request minimum platform scopes. Revoke at the platform on disconnect.

## 3. Authentication and authorization
- MFA available to all users; required for workspace owners and admins.
- Sessions: httpOnly, Secure, SameSite=Lax cookies; rotation on privilege change.
- Login, signup, password reset are rate limited.
- RBAC roles: owner, admin, editor, approver, viewer. Every endpoint declares a permission.

## 4. AI and untrusted content
- Comments, DMs, captions, web content are untrusted data, never instructions.
- AI services cannot publish, DM, change settings, or reach platform APIs.
- Every outbound AI text passes guardrails: banned words, links allowlist, price must match
  catalog, PII scrub, toxicity check, duplicate check, per-account daily caps.

## 5. Input, webhooks, files
- Validate all input with contracts at the edge.
- Verify every webhook signature (e.g. Meta `X-Hub-Signature-256`), reject replays, dedupe by event ID.
- Uploads: type sniffing + size limits + malware scan; served via signed URLs from a separate domain.
- No server-side fetch of user-supplied URLs without an allowlist (SSRF).

## 6. Secrets and infrastructure
- Secrets only in the secret manager / env at runtime. gitleaks runs pre-commit and in CI.
- TLS everywhere; DB and Redis on private network; least-privilege IAM per service.
- Encrypted backups with point-in-time recovery; restore tested monthly.

## 7. Supply chain and CI
- Lockfiles committed, exact versions, Renovate for updates.
- CI runs: Semgrep/CodeQL, dependency audit, gitleaks, container scan. Failures block merge.
- No `curl | sh` in Dockerfiles or scripts. Base images pinned by digest.

## 8. Abuse of our platform
- Per-tenant action caps and content moderation on generated content.
- Kill switch per tenant. Higher-volume plans require verification.

## 9. Logging and audit
- Append-only audit log of every human and AI action (who/what, inputs ref, decision, confidence).
- Logs are JSON, redacted, and never contain tokens, passwords, or full message bodies of DMs.

## 10. Compliance
- Platform data-deletion callbacks implemented. Data retention limits enforced by jobs.
- Privacy policy + terms published before any platform app review.
- External penetration test before public launch.
