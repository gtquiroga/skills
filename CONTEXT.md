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
