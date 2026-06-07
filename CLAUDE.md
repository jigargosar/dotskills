# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

`dotskills` authors **Claude Code skill specifications** — Markdown `SKILL.md` files with YAML frontmatter. There is no build, test, or lint system; everything is Markdown. Don't look for package manifests or CI.

## Skill lifecycle — parallel development

**Iron rule: the live skill folder is the *single* working copy. Never keep a separate local draft in the repo.** Two editable copies always drift out of sync (this caused real confusion once). The repo holds only frozen snapshots — never an active draft.

The live skill location:

`~/.claude/skills/kitchen-sink/skills/<name>/SKILL.md`

(installs are **copies**, not symlinks — see "Installing a skill").

### New skill

1. Develop directly in the live folder; `/reload-skills` and test as you go.
2. When it works, snapshot to the repo: `cp` live `SKILL.md` → `docs/references/<name>-SKILL.md`.

### Revise an existing live skill (parallel dev, zero downtime)

Run the new version *alongside* the old one so the live skill never goes down and you can break the in-progress copy freely.

1. **Duplicate live → temp sibling folder:** `cp -r …/<name>/ …/<name>-v2/`. The real `<name>` keeps serving live. In the temp copy, omit `name:` so the skill name is inferred from the folder (loads as `<name>-v2`).
2. **Develop + test on `<name>-v2`** — `/reload-skills`, iterate as many times as needed. The real `<name>` stays stable and usable throughout.
3. **Promote when done:**
   1. Archive the superseded version: `cp …/<name>/SKILL.md` → `docs/archive/<name>-SKILL-v<N>.md` (N = the version being replaced).
   2. Replace live: `cp …/<name>-v2/SKILL.md` → `…/<name>/SKILL.md`.
   3. Snapshot to repo: `cp …/<name>/SKILL.md` → `docs/references/<name>-SKILL.md`.
   4. Delete the temp folder: `rm -r …/<name>-v2/`. **Required** — a temp `-v2` folder is a *loadable skill* (it shows up as `/kitchen-sink:<name>-v2`), so leaving it behind creates a duplicate, confusing skill. Unlike `.bak` files (inert text, never loaded — see below), temp folders must not linger.
4. **No draft files remain** — the temp folder *was* the only working copy, and it's gone after promotion.

### Reverting in-progress state

Neither the live folder nor the temp `-v2` folder is git-tracked, so to return to an earlier state mid-development, take a manual backup before a risky edit: `cp SKILL.md SKILL.md.v<N>.bak`. `.bak` files are inert (never loaded by Claude Code) and harmless to keep — they're the lightweight history for these out-of-git folders. Only the promoted snapshot in `docs/` gets real git history.

### Naming

- Archived specs are version-stamped: `docs/archive/<name>-SKILL-v<N>.md` (e.g. `opn-SKILL-v1.md`).
- The current published snapshot is unversioned: `docs/references/<name>-SKILL.md`.
- Never put a version number in the live skill's folder/name — the live skill is always just `<name>`. The `-v2` suffix exists *only* on the temporary parallel-dev folder and is deleted on promotion.

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