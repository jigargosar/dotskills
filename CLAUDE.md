# CLAUDE.md

> ⚠️ Handle with care: this file accreted across plan revisions, so some wording and points are stale or no longer relevant. Read for intent, and don't take any single line out of its surrounding context.

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Current status (in progress)

Two tracks open:

1. Status report (docs/claude-status-report.md) — original task, in progress. MCP scope done (CLI list empty; Gmail + Context7 = claude.ai-scoped; shadcn deferred — desktop/cli/web split noted). Remaining: gather plugin component inventory (`claude plugin details`), verify cross-agent skill fan-out.
2. flo-v2 monk redesign — in progress, using the "Edit a copy" workflow (docs/skill-dev-workflow.md). See docs/flo-monk-improvement-plan.md.

## What this repo is

`dotskills` authors **Claude Code skill specifications** — Markdown `SKILL.md` files with YAML frontmatter. There is no build, test, or lint system; everything is Markdown. Don't look for package manifests or CI.

## Skill lifecycle

See docs/skill-dev-workflow.md.

## Installing a skill

Finalized skills are **copied** (not symlinked or published to a marketplace) into the kitchen-sink plugin so Claude Code can use them:

`~/.claude/skills/kitchen-sink/skills/<skill-name>/SKILL.md`

Use the `/install-skill` skill to do this.