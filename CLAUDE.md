@AGENTS.md

## Claude Code specifics

- For any spec marked `size: M` or larger, plan first (plan mode) and write the plan into the
  spec's Implementation notes before editing code.
- One spec per session. `/clear` when the spec is done.
- Don't spawn subagents for work that fits in the current context.
