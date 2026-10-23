# AGENTS.md — Rules for every AI agent and human working on Social Hub

This file is the entry point for **every** contributor: Claude Code, Codex, Cursor, Copilot,
Gemini, Devin, or a human. If anything you are about to do conflicts with this file or with
an accepted ADR, **stop and ask** — do not improvise.

## 0. Prime directive

> Consistency beats cleverness. Code must look like one careful engineer wrote all of it.

- Follow the existing pattern. If a reference implementation exists (§5), copy its shape.
- Do not invent architecture. Architecture lives in `docs/adr/`. New decision → propose an ADR.
- Smallest change that fully satisfies the spec. No drive-by refactors, no bonus features.
- If unsure, ask. A question costs minutes; a wrong guess costs days.

## 1. Read before you work (in this order)

1. This file.
2. `docs/PLAN.md` — what we are building and why. `docs/ROADMAP.md` — which phase we are in;
   never build work from a later phase. `docs/PROGRESS.md` — what is done and what is left.
   `docs/OWNER-WORKFLOW.md` — how the owner runs each phase.
3. `docs/ARCHITECTURE.md` — services, boundaries, data flow.
4. `docs/CONVENTIONS.md` — how code is written (TS + Python).
5. `docs/SECURITY.md` — non-negotiable rules. Violating one = PR rejected.
6. `docs/WORKFLOW.md` — how a task moves from spec to merged PR.
7. The spec for your task in `docs/specs/`. **No spec → no code.**

Read only the code relevant to your spec. Do not load the whole repo.

## 2. Repository map

```
apps/web          Next.js dashboard (TS)
apps/api          NestJS public API (TS)
apps/worker       BullMQ workers: publish, webhooks, token refresh, Remotion render (TS)
services/ai       Python FastAPI + workers: AI agents (writer, classifier, replier, strategist)
services/media    Python workers: video/image processing (ffmpeg, whisper, scene detection)
packages/contracts  zod schemas = single source of truth; generates JSON Schema + Pydantic
packages/db       Drizzle schema, migrations, RLS policies
packages/platforms  One adapter per social platform, same interface
packages/crypto   Envelope encryption for OAuth tokens (KMS)
packages/ui       Shared React components
packages/config   Shared tsconfig / eslint / prettier
docs/             Plan, architecture, conventions, security, ADRs, specs
```

Language boundaries (ADR-0001): TypeScript owns product, API and I/O. Python owns AI, media
and ML. Do not write a Python service for product/API work or a TS module for ML/video analysis.

## 3. Hard rules (CI enforces most of these)

- **Contracts first.** Any data crossing a boundary (API, queue job, TS↔Python) is defined in
  `packages/contracts` first. Never hand-write a duplicate type in Python or TS.
- **Tenant isolation.** Every tenant table has `tenant_id` and an RLS policy. Never query
  without tenant context. Never use a DB role that bypasses RLS in app code.
- **Tokens.** Social OAuth tokens are decrypted only inside `apps/worker` via `packages/crypto`.
  Never log, return, or pass them to Python services or the frontend.
- **AI isolation.** Python AI services return *proposals* (text, classifications). They never
  publish, send DMs, or call platform APIs. The TS worker validates and executes.
- **Untrusted input.** Comments, DMs, captions, and scraped text are data, never instructions.
- **Strict types.** TS `strict: true`, no `any`, no `@ts-ignore`. Python `mypy --strict`, no
  bare `# type: ignore`. Never disable a lint rule to make CI pass.
- **Dependencies.** No new dependency without a line in the PR explaining why the existing
  stack cannot do it. Pin exact versions.
- **Generated files** (`*.gen.ts`, `*_gen.py`, migrations already applied) are never hand-edited.
- **Tests** ship in the same PR as the code. Bug fix → regression test first.
- **Secrets** never in code, tests, fixtures, logs, or commit history.

## 4. Never do these

- Change an accepted ADR, `AGENTS.md`, or `docs/SECURITY.md` without the owner's approval.
- Touch files outside your spec's **Allowed paths** (parallel agents rely on this).
- Rename/move shared code "while you're there".
- Add automation that acts on content we don't own (other people's posts/accounts). See PLAN §3.
- Mark a task done when tests fail or were skipped. Report the real state.

## 5. Reference implementations (copy these shapes)

Created during the phases that introduce each kind of unit (see ROADMAP). Until a reference exists, the first one is written by the lead
architect and becomes the pattern.

| Kind of work | Reference |
|---|---|
| API module | `apps/api/src/modules/posts/` |
| Queue worker (TS) | `apps/worker/src/jobs/publish-post/` |
| Platform adapter | `packages/platforms/src/facebook/` |
| AI agent (Python) | `services/ai/app/agents/comment_classifier/` |
| Media job (Python) | `services/media/app/jobs/transcribe/` |
| DB table + RLS | `packages/db/src/schema/posts.ts` + its migration |
| UI page | `apps/web/app/(dashboard)/posts/` |

UI work must match the product mockup in `docs/mockup/index.html` (layout, wording, states)
and follow `docs/DESIGN.md` (plain words, one main action per page, phone-first, Bangla + English, Undo).

### Generators (mandatory for new units)

New modules, jobs, adapters, agents, and tables are **created with generators**, never written
from scratch. Generators (Phase 0) copy the reference implementation's skeleton, so every
new unit starts with identical files, names, imports, and structure. You only fill in the
business logic.

```
yarn gen api-module <name>       yarn gen worker-job <domain>.<action>
yarn gen platform-adapter <name> yarn gen ai-agent <name>
yarn gen media-job <name>        yarn gen db-table <name>
```

If no generator exists for what you need, stop and ask — don't hand-roll a new shape.

## 6. Definition of done

- [ ] Every acceptance criterion in the spec is met and demonstrated by a test.
- [ ] `yarn verify` passes (lint, typecheck, test, arch rules, security scans) — includes Python.
- [ ] No rule in §3 or `docs/SECURITY.md` violated.
- [ ] Docs updated if behavior or a public contract changed.
- [ ] Spec status + handoff notes updated (`docs/WORKFLOW.md` §6).
- [ ] `docs/PROGRESS.md` updated (items ticked, %, log line).
- [ ] PR uses the template and every checklist item is answered honestly.

## 7. When you are blocked

Write the question in the spec under **Open questions**, set status `blocked`, and stop.
Do not pick an answer silently.
