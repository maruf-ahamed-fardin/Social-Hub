# ADR-0004: Yarn 4 as the JavaScript package manager

- Status: accepted
- Date: 2026-10-24
- Deciders: owner

## Context
The team is most comfortable with Yarn. We need workspaces for the monorepo, a reliable
lockfile, and compatibility with Next.js, NestJS, Turborepo and Remotion.

## Decision
- Yarn 4 (Berry) with workspaces, version pinned via `packageManager` in root `package.json`.
- `nodeLinker: node-modules` (no Plug'n'Play) for maximum tool compatibility.
- CI installs with `yarn install --immutable`. `yarn.lock` is always committed.
- Turborepo orchestrates tasks; Python stays on uv (ADR-0001).

## Consequences
- Familiar workflow for the team.
- Slightly more disk usage than pnpm; acceptable.
- Root scripts: `yarn verify`, `yarn dev`, `yarn gen`, `yarn progress`.

## Alternatives considered
- pnpm — strict and efficient, but less familiar to the team.
- Yarn Plug'n'Play — faster installs, more friction with some tooling.
