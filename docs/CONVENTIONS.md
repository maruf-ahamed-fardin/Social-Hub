# Conventions

If a rule here and the existing code disagree, the reference implementation (AGENTS.md §5)
wins and this file gets fixed.

## 1. Tooling

| | TypeScript | Python |
|---|---|---|
| Package manager | Yarn 4 (workspaces, `nodeLinker: node-modules`) + Turborepo | uv |
| Version | Node 22 LTS | Python 3.12 |
| Types | `tsc` strict | `mypy --strict` |
| Lint / format | ESLint + Prettier | ruff (lint + format) |
| Tests | Vitest; Playwright for e2e | pytest |
| Validation | zod | Pydantic v2 (generated from contracts) |
| Logging | pino | structlog |

One command runs everything: `yarn verify`.

## 2. Naming

- Files: TS `kebab-case.ts`; Python `snake_case.py`. React components `PascalCase.tsx`.
- Types/classes `PascalCase`; functions/variables `camelCase` (TS) / `snake_case` (Py).
- DB tables/columns `snake_case`, tables plural (`posts`, `social_accounts`).
- Queues `<domain>.<action>` kebab case. Env vars `SCREAMING_SNAKE_CASE`, prefixed by app.
- Booleans read as questions: `isApproved`, `hasMedia`.

## 3. Code shape

- Functions do one thing; prefer < 40 lines. Files prefer < 300 lines.
- Pure logic separated from I/O so it can be unit tested without mocks.
- No default exports in TS (except where a framework requires them, e.g. Next.js pages).
- No magic numbers/strings: named constants or config.
- Comments explain *why*, not *what*. No commented-out code.

## 4. Errors

- TS: throw subclasses of `AppError` (`code`, `httpStatus`, `isRetryable`) from `packages/contracts`.
  Never throw strings or plain `Error` in domain code.
- Python: raise subclasses of `AppError` mirroring the same codes.
- API errors return RFC 9457 `application/problem+json` with the error `code`.
- Never swallow errors. Catch only to add context, convert, or retry.

## 5. API

- REST under `/v1`. Resources plural. Cursor pagination (`?cursor=&limit=`), max limit 100.
- Request/response bodies are contracts. Validate at the edge, trust inside.
- Mutating POSTs accept `Idempotency-Key`.
- Every endpoint declares its required permission (RBAC guard). No unguarded endpoints.

## 6. AI code (services/ai)

- Prompts live in `services/ai/app/prompts/<agent>/<name>.md`, versioned; no prompt strings
  inline in code.
- Every LLM output uses structured outputs into a Pydantic contract. No regex-parsing free text.
- Model IDs, effort, and max tokens come from config per agent.
- Untrusted text is wrapped in clearly delimited data blocks and the system prompt states it is
  data. Agents have no tools that publish or message.
- Every call records tokens + cost against the tenant and checks budget first.
- A prompt change requires running that agent's eval set (`services/ai/evals/`) and noting
  the before/after score in the PR.

## 7. Testing

- Unit tests next to code (`*.test.ts`, `test_*.py`). Integration tests use real Postgres/Redis
  via Docker, not mocks.
- Platform adapters: tests against recorded fixtures; no live platform calls in CI.
- Every RLS-protected table has a cross-tenant access test.
- Coverage is not the goal; every acceptance criterion maps to at least one test.

## 8. Git

- Branch: `feat/<spec-id>-short-name`, `fix/<spec-id>-short-name`.
- Conventional Commits: `feat(api): add post drafts endpoint`.
- Small PRs (< ~400 changed lines excluding generated files). One spec per PR.
