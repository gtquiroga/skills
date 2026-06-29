# Skills

My personal collection of [agent skills](https://github.com/vercel-labs/skills) — reusable `SKILL.md` capabilities for coding agents (Claude Code, Cursor, Codex, and more).

This repo holds the skills. Installation is handled by the community `skills` CLI; there is nothing to build or publish here. See [ADR 0001](./docs/adr/0001-distribute-via-community-skills-cli.md) for why.

## Install

Install all skills globally (available across every project on the machine):

```bash
npx skills add gtquiroga/skills -g
```

Common variations:

```bash
# List what's available without installing
npx skills add gtquiroga/skills --list

# Pick specific skills, target specific agents
npx skills add gtquiroga/skills --skill my-skill -a claude-code -g

# Install into the current project instead of globally (omit -g)
npx skills add gtquiroga/skills
```

| Scope                | Command       | Lands in            |
| -------------------- | ------------- | ------------------- |
| Global (all projects)| `-g`          | `~/<agent>/skills/` |
| Project (this repo)  | (default)     | `./<agent>/skills/` |

Default agent is `claude-code`; target others with `-a cursor`, `-a codex`, etc. (or pick interactively).

## Adding a skill

1. Copy the template into a new folder under `skills/`:
   ```bash
   mkdir -p skills/my-skill
   cp SKILL_TEMPLATE.md skills/my-skill/SKILL.md
   ```
   (or scaffold with `npx skills init my-skill`)
2. Edit the frontmatter (`name`, `description`) and write the body — see [SKILL_TEMPLATE.md](./SKILL_TEMPLATE.md).
3. Commit and push. It's installable immediately via the command above.

## Managing installed skills

```bash
npx skills list              # what's installed
npx skills update            # pull latest versions
npx skills remove my-skill   # uninstall
```

## Structure

```
skills/                 # one folder per skill, each with a SKILL.md (empty for now)
SKILL_TEMPLATE.md       # copy this to start a new skill
CONTEXT.md              # domain vocabulary
docs/adr/               # architecture decisions
```
