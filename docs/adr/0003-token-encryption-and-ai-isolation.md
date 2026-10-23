# ADR-0003: Envelope-encrypted tokens; AI services never act

- Status: accepted
- Date: 2026-10-23
- Deciders: owner

## Context
Social OAuth tokens let anyone post as our customers. AI agents read untrusted text
(comments, DMs) and can be prompt-injected.

## Decision
- Tokens are envelope-encrypted (per-tenant data key wrapped by KMS) and decrypted only in
  `apps/worker`.
- Python AI services return proposals only. Publishing/messaging happens in the TS worker
  after guardrails and the tenant's autonomy level allow it.

## Consequences
- A compromised or manipulated AI service cannot post or exfiltrate tokens.
- One extra hop (proposal → worker) per action; acceptable latency.

## Alternatives considered
- Giving agents platform tools directly — simpler, unacceptable blast radius.
