# Output Enforcer — backlog

Date: 2026-06-13. Companion to `2026-06-13-output-formatting-skill-design.md`.
Nothing here is shipped. Each item re-enters via brainstorm → writing-plans → writing-skills.

## Candidate rules (simple, not yet built)

Marked "locked" in earlier notes, but never built or tested:

- short lines, one idea per line, minimum words, no padding.
- answer the question; no sidetrack.
- state the positive action, not just the prohibition.
- short reply = one point.

## Hard rules (blocked — fight markdown rendering)

Wanted, but each needs its own approach/test:

- groups shown by indent, no bold, no prefix; sub-items indent 4 spaces — 4-space indent under a non-list line renders as a code block.
- items uniquely numbered, flat continuous integers across groups incl. sub-items — markdown auto-renumbers.

## Deferred decisions

- Persistence / always-on wiring: hook vs CLAUDE.md vs manual invoke. For now: start as an invocable skill.
- Name alternatives considered: output-format, intent-anchor; "structured-output" rejected (misleads). Decided: output-enforcer.


# Output Enforcer — design spec (v1, frozen)

Date: 2026-06-13. Status: v1 shipped, frozen.
Skill: `skills/output-enforcer/SKILL.md`. Name: output-enforcer (decided).
Future rules & deferred decisions: `2026-06-13-output-formatting-skill-backlog.md`.

## Purpose

QoL: make every Claude reply fast to scan, reference, act on. A fixed rule set for reply format, baked into the skill — not per-call, not a menu. Blockquote is rule 1, not the whole thing.

## Shipped in v1 (source of truth — mirrors SKILL.md)

1. Blockquote first: intent + anchor. Reply = blockquote, then body, no pause. Intent = what was understood. Anchor = the request part the output uses. Clean, sharp, no fillers.
2. No bold anywhere.
3. No fluff: no apology, no narration, no reasoning preamble. Answer directly.

Test: eyeball replies; fix inline.

## Decisions

- Formats Claude's own replies — not LLM-calling code, not a reference doc.
- Fixed rules baked in — not per-call, not a menu.
- Applies to every reply once invoked.
- user-invocable; model-invocation disabled.
- Testing: eyeball simple rules; harness for complex rules.

## Render notes (constraints for any future rule)

- Indent under a list item = nested list.
- 4 spaces under a non-list line = code block.

## Process note

writing-plans skipped intentionally for v1 — 3-rule skill, trivial. Every future rule batch routes through the full flow: brainstorm → writing-plans → writing-skills. Deferred items live in the backlog file (linked above) — not lost.
