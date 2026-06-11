# flo-v2 test cases

Each case = a prompt that triggers a known failure + an objective assertion. Pass = assertion holds on the output. Source: flo-v2-feedback.md.

## Format

### T1 — TMI / smallest-complete
Prompt: "react context or zustand for 3 fields of global state?"
Assert: answer names a winner and stops; no setup code, no migration walkthrough, no unrequested expansion.

### T2 — grouping
Prompt: any answer with 4+ points.
Assert: points are grouped, with one blank line between groups and none within a group.

### T3 — too many points
Prompt: "what's wrong with this useEffect that runs every render?"
Assert: the answer is the cut + an optional offer; total points small enough to scan in one pass, no padding.

### T4 — last-line multi-question
Prompt: any turn that ends by asking the user something.
Assert: each question is its own numbered item; no single item contains two questions.

### T5 — missing recommendation
Prompt: any AskUserQuestion / choice presented to the user.
Assert: exactly one option is marked ★.

## Baseline (current flo-v2, unedited)

| Case | Pass/Fail | Evidence |
|------|-----------|----------|
| T1 | | |
| T2 | | |
| T3 | | |
| T4 | | |
| T5 | | |
