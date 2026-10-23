# Owner workflow — how you run this project, step by step

You are the owner. AI agents do most of the building; you decide, approve, and check.
Everything below uses files already in this repo.

## The big picture

```
Pick phase ─► Get specs written ─► Approve specs ─► Agents build (1 spec each)
     ▲                                                     │
     │                                                     ▼
Close phase ◄─ Update PROGRESS ◄─ Merge ◄─ You check demo ◄─ Review agent + CI
```

One loop = one phase (about 3–5 days). Repeat 65 times. The business track (Phase 00)
runs alongside the whole time.

## Step 1 — Pick the phase (5 min)

Open `docs/PROGRESS.md`. The current phase is at the top. Check its `depends on` phases in
`docs/ROADMAP.md` are ✅. If not, finish those first.

## Step 2 — Get the specs written (architect agent, ~30 min)

Give the architect agent (Claude Code, your strongest model) this prompt:

> Read AGENTS.md, docs/ROADMAP.md, docs/PROGRESS.md and the docs they link.
> Write the specs for Phase NN using docs/specs/_TEMPLATE.md. Split the phase into 2–6 specs
> of size S or M. Each spec must list acceptance criteria, allowed paths that do not overlap
> with other specs in this phase, contracts touched, reference implementation, and test plan.
> Mark dependencies between specs. Do not write any code. Set status to draft.

## Step 3 — Approve the specs (you, ~20 min)

Read each spec. Check three things:
- **Goal:** is this what I want my customers to get?
- **Out of scope:** is anything important missing or sneaking in?
- **Acceptance criteria:** could I check each one myself in a demo?

Ask for changes until you are happy, then set each spec `status: ready`.

## Step 4 — Build (implementer agents, 1–2 days per spec)

Run one agent per spec. Specs without dependencies on each other can run in parallel
(separate git worktrees or branches). Prompt:

> Read AGENTS.md and docs/specs/NNN-name.md. Implement exactly this spec, nothing else.
> Use the generators and copy the reference implementation's shape. Only edit the allowed
> paths. If something is unclear, write it under Open questions, set status blocked and stop.
> When done: yarn verify must pass, fill the PR template honestly, set status in-review,
> and write handoff notes.

You can use different tools for different specs (Claude Code, Codex, Cursor). The rules in
AGENTS.md apply to all of them.

## Step 5 — Review (a different agent + CI, ~30 min per PR)

Never let the agent that wrote the code review it. Use a second session or tool:

> Review this PR against docs/specs/NNN-name.md, AGENTS.md §3–§4, docs/SECURITY.md and
> docs/CONVENTIONS.md. List every deviation: missing acceptance criteria, files outside
> allowed paths, security rule violations, pattern differences from the reference
> implementation, missing tests. Be specific with file and line.

In Claude Code you can also run `/code-review` on the branch. Send findings back to the
implementer agent until the review is clean and CI is green.

## Step 6 — Check it yourself (you, ~15 min per spec)

From Phase 10 onward every merge deploys to staging. Open staging and try each acceptance
criterion yourself. Compare UI work with the mockup in `docs/mockup/index.html`.
If it doesn't match what you expected, it isn't done.

## Step 7 — Merge and update progress (2 min)

Merge the PR. Tick the item(s) in `docs/PROGRESS.md`, update the % and add a log line
(from Phase 03, `yarn progress` does the math and CI checks it).

## Step 8 — Close the phase (~30 min)

When every item in the phase is ticked, give this prompt to the architect agent:

> Check Phase NN against the exit gate in docs/ROADMAP.md: every Done-when item demonstrated,
> CI green, deployed to staging, references and generators added for any new kind of unit,
> docs and ADRs updated. Report what passes and what is missing. Do not fix anything.

If everything passes: mark the phase ✅ in PROGRESS.md and tag it `phase-NN`.

## Weekly routine (Saturday, ~1 hour)

1. **Progress:** read PROGRESS.md. Are you on the timeline in ROADMAP.md?
2. **Business track:** chase platform approvals (Meta, LinkedIn, TikTok, YouTube).
3. **Costs:** check AI token spend and hosting bills.
4. **Lessons:** if agents kept making the same mistake, add a rule to AGENTS.md or a CI check.
   A mistake that happens twice becomes a rule; a rule broken twice becomes a CI check.
5. **Decisions:** accept or reject any ADRs agents proposed.

## Golden rules for you

- No spec, no code. Even for "small" things.
- One agent per spec, one spec per branch.
- Reviewer is never the author.
- Never skip the exit gate to go faster. Skipped gates come back as bugs in production.
- When an agent asks a question, answer it in the spec, so every future agent sees the answer.
