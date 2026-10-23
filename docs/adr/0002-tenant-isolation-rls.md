# ADR-0002: Tenant isolation with Postgres row-level security

- Status: accepted
- Date: 2026-10-23
- Deciders: owner

## Context
Multi-tenant SaaS. A single missing `WHERE tenant_id = …` would leak customer data.

## Decision
Shared database, shared schema, `tenant_id` on every tenant-owned table, RLS enabled and
forced. The API sets `app.tenant_id` per transaction. App roles never bypass RLS.

## Consequences
- Bugs in query code cannot cross tenants; the database refuses.
- Every new table needs a policy and a cross-tenant test.
- Admin/cross-tenant jobs use a separate, audited role.

## Alternatives considered
- Schema- or database-per-tenant — stronger isolation, much heavier operations at our scale.
