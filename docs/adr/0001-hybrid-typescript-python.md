# ADR-0001: Hybrid TypeScript + Python, split by responsibility

- Status: accepted
- Date: 2026-10-23
- Deciders: owner

## Context
The product is I/O-heavy (APIs, webhooks, queues, dashboard) and also needs heavy AI, video
and ML processing. One language is weak at one of the two.

## Decision
- TypeScript: `apps/web` (Next.js), `apps/api` (NestJS), `apps/worker` (BullMQ, platform
  adapters, Remotion templated video).
- Python: `services/ai` (FastAPI + workers, agents), `services/media` (ffmpeg, Whisper,
  scene detection), analytics/ML.
- Shared contracts defined in zod (`packages/contracts`), generated to JSON Schema + Pydantic.

## Consequences
- Two toolchains; `yarn verify` must run both.
- Contract codegen check in CI prevents drift.
- If multi-step cross-language workflows become hard to manage on BullMQ, revisit with Temporal.

## Alternatives considered
- All TypeScript — weaker video/ML ecosystem.
- All Python — weaker frontend sharing; duplicated types with Next.js.
