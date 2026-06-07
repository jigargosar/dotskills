# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

`dotskills` authors **Claude Code skill specifications** — Markdown `SKILL.md` files with YAML frontmatter. There is no build, test, or lint system; everything is Markdown. Don't look for package manifests or CI.

## Skill lifecycle

1. **Draft** in `docs/` (e.g. `docs/opn-SKILL.draft.md`).
2. **Archive / publish** when finalized: move to `docs/archive/` (superseded raw/draft material) or `docs/references/` (published reference copy). Don't delete drafts — archive them.

## SKILL.md frontmatter

Match existing skills (see `docs/references/flo-SKILL.md`). Minimum:

```yaml
---
name: <skill-name>
description: <what it does + when to use it>
user-invocable: true|false
disable-model-invocation: true|false
---
```

## Installing a skill

Finalized skills are **copied** (not symlinked or published to a marketplace) into the kitchen-sink plugin so Claude Code can use them:

`~/.claude/skills/kitchen-sink/skills/<skill-name>/SKILL.md`

Use the `/install-skill` skill to do this.