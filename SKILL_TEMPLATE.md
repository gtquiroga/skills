# Skill template

Copy this file to start a new skill:

```bash
mkdir -p skills/in-progress/<your-skill-name>
cp SKILL_TEMPLATE.md skills/in-progress/<your-skill-name>/SKILL.md
# then edit the frontmatter + body below
```

Or let the CLI scaffold it for you: `npx skills init <your-skill-name>`.

A skill is just a folder under `skills/` with a `SKILL.md` inside. Supporting files
(reference docs, scripts, examples) can live next to `SKILL.md` and be linked from the body.

---

Everything below the line is the actual `SKILL.md` content — copy from the frontmatter down.

```md
---
name: your-skill-name
description: One sentence the agent reads to decide when this skill applies. Be specific about the trigger ("Use when the user wants to ..."). This is the most important field — it is how a model-invoked skill gets matched.
# disable-model-invocation: true   # uncomment to make it user-invoked only (run with /your-skill-name)
---

# Your Skill Name

Write the instructions the agent should follow when this skill runs. Be direct and
imperative — you are writing a prompt for the agent, not documentation for a human.

## Steps

1. ...
2. ...

## Notes

- Keep the body focused on *what to do*. Link supporting files with [relative links](./reference.md).
```

## Frontmatter fields

- **name** (required) — kebab-case, matches the folder name. Becomes the `/name` command.
- **description** (required) — when the skill applies. Drives model-invocation matching; write it for that.
- **disable-model-invocation** (optional) — set `true` so the skill only runs when explicitly invoked as `/name`. Omit for a model-invoked skill the agent can trigger itself.

## Conventions

- One folder per skill under a category — `skills/<category>/<name>/` — folder name == `name`. New skills start in `skills/in-progress/`; promote to `coding`/`personal` when solid. See [README](./README.md#categories--lifecycle).
- Keep `SKILL.md` instructions tight; push long reference material into sibling files.
- See [CONTEXT.md](./CONTEXT.md) for the vocabulary (Skill, Source, Scope, user- vs model-invoked).
