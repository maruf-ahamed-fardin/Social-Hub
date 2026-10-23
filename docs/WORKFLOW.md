# Workflow — how many people and many AI agents build one consistent product

## 1. Roles

| Role | Who | Owns |
|---|---|---|
| Owner | Product owner (human) | Priorities, approves ADRs and SECURITY.md changes |
| Architect | Lead (human or designated lead agent) | Writes specs and ADRs, reviews every PR against them |
| Implementer | Any human or AI agent | Executes exactly one spec at a time |
| Reviewer | A different agent or human than the implementer | Checks PR against spec + checklists |

The implementer and reviewer are never the same agent session.

## 2. Lifecycle of a task

```
idea → spec (draft) → spec (ready) → in-progress → in-review → done
                         ↑                │
                         └── blocked ◄────┘
```

1. **Spec.** Architect writes `docs/specs/NNN-short-name.md` from `_TEMPLATE.md`, including
   acceptance criteria, allowed paths, contracts touched, and test plan. Status `ready`.
2. **Claim.** Implementer sets `status: in-progress` and `assignee:` in the spec and creates
   branch `feat/NNN-short-name`. One agent per spec.
3. **Plan.** For size M or larger, implementer writes an implementation plan in the spec before code.
4. **Build.** Contracts → DB/migration → core logic + tests → API/worker wiring → UI.
5. **Verify.** `yarn verify` green locally. Demonstrate each acceptance criterion by a test.
6. **PR.** Fill the PR template honestly. Set spec `status: in-review`.
7. **Review.** Reviewer checks spec, AGENTS.md §3/§4, SECURITY.md, CONVENTIONS.md. CI green.
8. **Merge + handoff.** Spec `status: done` with handoff notes; tick the item(s) in `docs/PROGRESS.md`.

## 3. Parallel work without collisions

- Each spec lists **Allowed paths**. Agents edit only those. Need more? Update the spec first.
- Shared files (`packages/contracts`, `packages/db/schema`, root config) change only through a
  dedicated spec, merged before dependent specs start.
- Specs declare `depends_on`. Don't start a spec whose dependencies aren't `done`.
- Rebase on `main` before opening a PR; never force-push someone else's branch.

## 4. Writing specs that any agent executes the same way

- Acceptance criteria are testable statements ("Given X, when Y, then Z"), not wishes.
- Name the exact contracts, endpoints, queue names, tables, and reference implementation to copy.
- State what is **out of scope**.
- Put decisions in the spec, not in the agent's head. Ambiguity is where agents diverge.
- Prefer several small specs (S/M) over one large one.

## 5. Machine enforcement (set up in Phase 0)

Rules that CI doesn't check get broken. These run on every PR and block merge:

| Check | Tool |
|---|---|
| Format + lint | Prettier, ESLint, ruff |
| Types | tsc strict, mypy --strict |
| Architecture boundaries | dependency-cruiser (TS), import-linter (Python) |
| Contracts in sync | codegen check: regenerated output must equal committed output |
| Tests | Vitest, pytest, Playwright smoke |
| RLS | cross-tenant test suite |
| Security | Semgrep, CodeQL, gitleaks, dependency audit |
| AI prompts | eval suite when `services/ai/app/prompts/**` changes |
| Ownership | CODEOWNERS: contracts, db, crypto, security docs need architect review |

## 6. Handoff notes (end of every session, in the spec)

```
### Handoff — YYYY-MM-DD — <agent/human>
- Done:
- Not done / remaining:
- Decisions made (and why):
- Gotchas for the next person:
```

## 7. Decisions

- Anything that changes architecture, adds a service/major dependency, or alters a contract
  used by more than one app needs an ADR in `docs/adr/` (template: `0000-template.md`).
- Agents may *propose* ADRs (status `proposed`). Only the owner/architect accepts them.
