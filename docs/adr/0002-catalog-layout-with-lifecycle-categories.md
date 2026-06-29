# Catalog layout with lifecycle categories

Skills are organized as a **catalog** — `skills/<category>/<name>/SKILL.md` — with exactly four category folders: `in-progress`, `coding`, `personal`, `deprecated`. This refines [ADR 0001](./0001-distribute-via-community-skills-cli.md), which left the layout flat (`skills/<name>/SKILL.md`).

The `skills` CLI walks each skill container one level deep for the flat layout **and one extra level deep for the catalog layout** (`skills/<category>/<name>/SKILL.md`). The category folder therefore groups skills in the interactive install picker *for free* — there is no config file and no frontmatter field to declare a group. We get the grouping the reference repo ([`mattpocock/skills`](https://github.com/mattpocock/skills)) shows simply by placing each skill under a category folder.

A skill's identity is its `<name>` (folder/frontmatter), **independent of its category**. Moving a skill between categories is a plain `git mv`; it does not change the `/name` command or break `npx skills update`. This is what makes the lifecycle cheap:

- **Promote** a draft: `git mv skills/in-progress/<name> skills/coding/<name>` (or `personal`). No frontmatter change.
- **Deprecate**: `git mv skills/<category>/<name> skills/deprecated/<name>` **and** add `metadata.internal: true`.

We considered a flat layout (simplest, but no grouping and no place to stage drafts or retire skills) and a config-file-driven grouping (more flexible, but the CLI offers no such hook and folder-derived grouping already does the job). Folder-as-category wins on zero maintenance.

## Visibility

Only `deprecated` skills are hidden from the picker, via `metadata.internal: true`. `in-progress` stays **visible** so drafts can be dogfooded (`npx skills add ... --skill <name>` or interactively). We accept that the picker can surface unstable drafts; the alternative — hiding `in-progress` too — was rejected to keep dogfooding frictionless.

## Consequences

- New skills are created under `skills/in-progress/<name>/` and promoted when solid.
- The promote/deprecate lifecycle is a **documented convention** (README + [CONTEXT.md](../../CONTEXT.md)), executed by hand with `git mv`. No promote tooling is built until the skill count justifies it.
- Deprecating requires two steps (move + set `internal`); forgetting the flag leaves a dead skill in the picker. This is the cost of having no automation.
- Supersedes the "every skill is a folder directly under `skills/`" consequence in ADR 0001.
