---
id: NNN
title: <short imperative title>
status: draft            # draft | ready | in-progress | blocked | in-review | done
size: S                  # S (<½ day) | M (≤2 days) | L (split it)
assignee:
depends_on: []
phase: 1
---

# NNN — <title>

## Goal
One or two sentences: what user-visible outcome this delivers and why.

## Out of scope
- …

## Reference implementation to copy
- `path/to/reference/`

## Contracts
- New/changed in `packages/contracts`: `…`
- Queue jobs: `<domain>.<action>` payload `…`

## Data
- Tables/columns/migrations: …
- RLS: yes/no (+ cross-tenant test)

## Acceptance criteria
1. Given …, when …, then …
2. …

## Security considerations
- Which SECURITY.md sections apply and how they are satisfied.

## Allowed paths
- `apps/api/src/modules/<x>/**`
- …

## Test plan
- Unit: …
- Integration: …
- E2E: …

## Implementation notes
(Implementer writes the plan here before coding for size M+.)

## Open questions
- …

## Handoff log
