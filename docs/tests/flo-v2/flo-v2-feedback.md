# flo-v2 feedback — observed failures and fixes

Real failures from a working session where flo-v2 (byte-identical to flo, no edits) was the active session skill. Each item is both a fix target and a test case.

## Format
- Had to say "TMI" almost every other turn — output consistently overshoots smallest-complete; the user ended up doing the trimming the skill should do. (Headline pattern.)
- No grouping — wall of points, unreadable.
- Too many points to digest.
- Last line smuggles multiple questions into one item.
- Missing ★ recommendation.

## Correction behavior
- When correcting, retain the rest of the output verbatim — change only the corrected part.
- When asked to see a file, Read that exact file and print it — no context recall, no substitution, no commentary.

## Interaction discipline
- Limit answers to the question asked.
- Ask rather than assume intentions.
- Too many roundtrips for simple checkable things — overconfidence in interpretation drives multi-turn churn.
- Finish the current conversation before taking steps.
- Show steps and STOP before executing.
- Ground output to reality.

## Tool use
- Over-split bash commands that have no shell operators — overcorrection.

## Finding: two unrelated concerns bundled
- flo-v2 carries two jobs: output style (its named purpose) and agent action-discipline (STOP before executing, don't deviate, ask-don't-assume). The failures split along that same seam.
- Consider separating the discipline rules from the output-style skill. Format-first regardless.

## Approach
- Fix one aspect first: format. Discipline second, same loop.
- Design by subtraction — a single direct nudge often works ("don't narrate"); favor fewer, sharper rules over more.
- Testing: run the failure-triggering prompts against the fixed flo-v2 and compare to this session's baseline (the failures above).
