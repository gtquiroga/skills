# Skills

A personal collection of agent skills, authored as `SKILL.md` files and installed onto a machine with the community `skills` CLI (`npx skills add gtquiroga/skills`). This repo holds the skills; it ships no installer of its own.

## Language

**Skill**:
A self-contained capability an agent can load and run, defined by a folder under `skills/` containing a `SKILL.md` plus any supporting files.
_Avoid_: command, plugin, tool

**SKILL.md**:
The single file at the root of a skill folder. Frontmatter (`name`, `description`, optional flags) declares the skill; the body is the instructions the agent follows.
_Avoid_: manifest, spec

**Source**:
A repository the `skills` CLI installs from, addressed as `owner/repo` (e.g. `gtquiroga/skills`). This repo is itself a Source.
_Avoid_: registry, marketplace, package

**Agent**:
A coding agent that consumes skills — Claude Code, Cursor, Codex, and others the CLI supports. One skill can be installed onto many agents.
_Avoid_: assistant, client

**Scope**:
Where an installed skill lives. **Global** (`-g`, `~/<agent>/skills/`) makes it available across all projects; **Project** (default, `./<agent>/skills/`) commits it alongside one project.
_Avoid_: level, location

**User-invoked skill**:
A skill run only when explicitly typed (`/name`), declared with `disable-model-invocation: true` in frontmatter.
_Avoid_: manual skill

**Model-invoked skill**:
A skill the agent may trigger on its own when a task matches its `description`. The default when `disable-model-invocation` is absent.
_Avoid_: auto skill, automatic skill

**Catalog layout**:
The repo's folder convention: `skills/<category>/<name>/SKILL.md`. The `skills` CLI walks one extra level deep for exactly this shape, so the category folder groups skills in the install picker for free — no config file or frontmatter needed.
_Avoid_: flat layout (the older `skills/<name>/` shape, no longer used)

**Category**:
The lifecycle bucket a skill lives in, expressed as its parent folder under `skills/`. Exactly four: `in-progress`, `coding`, `personal`, `deprecated`. A skill's identity is its `<name>`, independent of category — moving it between categories does not change its `/name` or break `npx skills update`.
_Avoid_: group, folder, tag

**In-progress**:
Category for drafts not yet promoted. Installable (left visible in the picker) so you can dogfood them, but unstable.

**Coding**:
Category for skills that operate on a codebase or an engineering workflow (e.g. `to-prd-codex`, TDD, code review).
_Avoid_: engineer, engineering

**Personal**:
Category for skills supporting non-coding workflows (writing, planning, learning, life admin).

**Deprecated**:
Category for retired skills. Kept in the repo for reference but marked `metadata.internal: true` so they are hidden from the install picker.

**Promote**:
Move a skill from `in-progress` into `coding` or `personal` (a `git mv`). No frontmatter change.

**Deprecate**:
Retire a skill: `git mv` it into `deprecated` and add `metadata.internal: true`.

**Internal**:
The `metadata.internal: true` frontmatter flag that hides a skill from the `npx skills add` picker. Used only on `deprecated` skills; it can still be installed explicitly with `--skill <name>`.
_Avoid_: hidden, private
