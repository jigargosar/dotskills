# CLAUDE.md

> ⚠️ Handle with care: this file accreted across plan revisions, so some wording and points are stale or no longer relevant. Read for intent, and don't take any single line out of its surrounding context.

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Current status (in progress)

Two tracks open:

1. Status report (docs/claude-status-report.md) — original task, in progress. Remaining: correct MCP scope (shadcn = User config; Gmail + Context7 = claude.ai config), gather plugin component inventory (`claude plugin details`), verify cross-agent skill fan-out.
2. flo-v2 monk redesign — PAUSED. Plan in docs/flo-monk-improvement-plan.md. Restore point docs/archive/flo-v2-SKILL-v1.md. Resume after the report.

## What this repo is

`dotskills` authors **Claude Code skill specifications** — Markdown `SKILL.md` files with YAML frontmatter. There is no build, test, or lint system; everything is Markdown. Don't look for package manifests or CI.

## Skill lifecycle

Two tiers, by change size. **Minor fix → mini workflow** (snapshot to archive, edit live in place). **Substantial/risky change that needs zero-downtime → parallel dev.** Don't reach for parallel dev for a typo.

**Iron rule: the live skill folder is the *single* working copy. Never keep a separate local draft in the repo.** Two editable copies always drift out of sync (this caused real confusion once). The repo's record of a skill is its **archive history** in `docs/archive/`; the live folder is always the current version. The lifecycle never produces any other copy.

The live skill location:

`~/.claude/skills/kitchen-sink/skills/<name>/SKILL.md`

(installs are **copies**, not symlinks — see "Installing a skill").

### New skill

1. Develop directly in the live folder; `/reload-skills` and test as you go.
2. That's it — it lives in the live folder. Nothing is archived until its **first revision**.

### Minor fix (mini workflow)

For small, low-risk edits — typos, frontmatter tweaks, wording — skip parallel dev entirely.

1. **Snapshot current live → archive:** `cp …/<name>/SKILL.md` → `docs/archive/<name>-SKILL-v<N>.md` (N = next unused number; first revision archives `v1`). This is your restore point.
2. **Edit live in place** — `/reload-skills` and test. No temp folder, no `.bak`.

The archive snapshot *is* the safety net, so it's taken **before** editing. If the fix goes wrong, restore from the archived `v<N>`.

### Revise an existing live skill (parallel dev, zero downtime)

Use this only when the change is large or risky enough that the live skill must stay up while you work.

Run the new version *alongside* the old one so the live skill never goes down and you can break the in-progress copy freely.

1. **Duplicate live → temp sibling folder:** `cp -r …/<name>/ …/<name>-v2/`. The real `<name>` keeps serving live. In the temp copy, omit `name:` so the skill name is inferred from the folder (loads as `<name>-v2`).
2. **Develop + test on `<name>-v2`** — `/reload-skills`, iterate as many times as needed. The real `<name>` stays stable and usable throughout.
   - **No git safety in the temp folder.** Before a risky edit, take a manual backup: `cp SKILL.md SKILL.md.v<N>.bak`. `.bak` files are inert (never loaded by Claude Code), live *inside* the temp folder, and are throwaway in-progress history — they **die with the temp folder** on promotion. Never move them to the repo.
3. **Promote when done:**
   1. Archive the existing live version: `cp …/<name>/SKILL.md` → `docs/archive/<name>-SKILL-v<N>.md` (N = next number; the first revision archives `v1`).
   2. Replace live: `cp …/<name>-v2/SKILL.md` → `…/<name>/SKILL.md`.
   3. Delete the temp folder: `rm -r …/<name>-v2/`. **Required** — a temp `-v2` folder is a *loadable skill* (it shows up as `/kitchen-sink:<name>-v2`), so leaving it behind creates a duplicate, confusing skill. Its `.bak` files go with it.
4. **No draft files remain** — the temp folder *was* the only working copy, and it's gone after promotion. The only repo artifact is the new archive entry.

### Naming

- Archived specs are version-stamped: `docs/archive/<name>-SKILL-v<N>.md` (e.g. `opn-SKILL-v1.md`). The archive accumulates one entry per superseded version, starting at the **first revision** (N = next unused number for that skill).
- Never put a version number in the live skill's folder/name — the live skill is always just `<name>`. The `-v2` suffix exists *only* on the temporary parallel-dev folder and is deleted on promotion.

## SKILL.md frontmatter

Match existing skills (see `docs/archive/flo-SKILL-v1.md` or any live skill). Minimum:

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