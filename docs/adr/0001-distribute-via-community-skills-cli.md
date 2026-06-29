# Distribute via the community `skills` CLI instead of a custom installer

This repo holds skills only; it ships no installer code. Installation is delegated to the community `skills` CLI ([`vercel-labs/skills`](https://github.com/vercel-labs/skills)) via `npx skills add gtquiroga/skills`. We chose this over building our own NPX `bin` (full control, but installer code to write, publish to npm, and maintain) and over a Claude Code plugin marketplace (native, but not the NPX flow, and Claude-only).

The CLI already handles multi-agent targeting, global/project scope, symlink-vs-copy, and `update`/`remove`/`list`, so reimplementing it would be pure cost. The trade-off we accept: we depend on a third-party CLI and its repo conventions (top-level `skills/`, one folder per skill, each with a `SKILL.md`). If that CLI ever disappears or breaks, skills are still plain Markdown and can be copied by hand or installed by a replacement tool — so the lock-in is shallow.

## Consequences

- The repo must be reachable by the CLI. We host it **public** on GitHub so `npx skills add gtquiroga/skills` works with zero auth.
- We follow the CLI's discovery convention: every skill is a folder containing a `SKILL.md`. The exact placement under `skills/` is the catalog layout defined in [ADR 0002](./0002-catalog-layout-with-lifecycle-categories.md), which supersedes the flat `skills/<name>/` shape originally implied here.
- We write no `package.json`, `bin`, or publish step.
