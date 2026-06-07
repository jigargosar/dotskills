---
name: install-skill
description: Copy a finalized SKILL.md from this repo into the kitchen-sink plugin so Claude Code can use it. Use when the user wants to install, deploy, or publish a skill authored here.
disable-model-invocation: true
argument-hint: <skill-name> [source-path]
---

# install-skill

Copies a finalized skill spec into the kitchen-sink plugin directory:
`~/.claude/skills/kitchen-sink/skills/<skill-name>/SKILL.md`

## Inputs

`$ARGUMENTS`: `<skill-name>` (required) and optionally an explicit `<source-path>` to the SKILL.md.

## Steps

1. **Resolve the source.** If a source path is given, use it. Otherwise search `docs/` for a finalized spec matching `<skill-name>` (e.g. `docs/references/<name>-SKILL.md`, `docs/<name>-SKILL.md`). Read it to confirm it has valid frontmatter (`name`, `description`). Refuse `*.draft.md` files — drafts aren't ready to install; tell the user to finalize first.

2. **Confirm.** Show the resolved source path and the target path. Ask the user to confirm before copying (mandatory — this writes outside the repo). If the target already exists, say so and ask whether to overwrite.

3. **Copy.** Create `~/.claude/skills/kitchen-sink/skills/<skill-name>/` if needed, then copy the SKILL.md there. The frontmatter `name:` must equal `<skill-name>`.

4. **Report.** State the target path written and remind the user it's available as `/kitchen-sink:<skill-name>`.