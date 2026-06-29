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

## Categories & lifecycle

Skills are organized into four category folders under `skills/` (the [catalog layout](./docs/adr/0002-catalog-layout-with-lifecycle-categories.md) — the `skills` CLI groups the install picker by these folders automatically):

| Category      | What lives here                                            | In the install picker? |
| ------------- | ---------------------------------------------------------- | ---------------------- |
| `in-progress` | Drafts, not yet promoted. Installable but unstable.        | Yes                    |
| `coding`      | Skills that operate on a codebase / engineering workflow.  | Yes                    |
| `personal`    | Non-coding workflows (writing, planning, life admin).      | Yes                    |
| `deprecated`  | Retired skills, kept for reference.                        | No (`metadata.internal: true`) |

Lifecycle is just moving folders:

- **Promote** a draft: `git mv skills/in-progress/<name> skills/coding/<name>` (or `personal`). Nothing else changes — the `/name` and `npx skills update` keep working.
- **Deprecate** a skill: `git mv skills/<category>/<name> skills/deprecated/<name>` **and** add `metadata.internal: true` to its frontmatter.

## Adding a skill

1. Copy the template into a new folder under the right category (new skills usually start in `in-progress`):
   ```bash
   mkdir -p skills/in-progress/my-skill
   cp SKILL_TEMPLATE.md skills/in-progress/my-skill/SKILL.md
   ```
   (or scaffold with `npx skills init my-skill`)
2. Edit the frontmatter (`name`, `description`) and write the body — see [SKILL_TEMPLATE.md](./SKILL_TEMPLATE.md).
3. Commit and push. It's installable immediately via the command above.
4. When it's solid, **promote** it to `coding` or `personal` (see above).

## Managing installed skills

```bash
npx skills list              # what's installed
npx skills update            # pull latest versions
npx skills remove my-skill   # uninstall
```

## Structure

```
skills/
  in-progress/<name>/SKILL.md   # drafts (installable, unstable)
  coding/<name>/SKILL.md        # engineering / codebase skills
  personal/<name>/SKILL.md      # non-coding skills
  deprecated/<name>/SKILL.md    # retired (hidden from picker)
SKILL_TEMPLATE.md               # copy this to start a new skill
CONTEXT.md                      # domain vocabulary
docs/adr/                       # architecture decisions
```
