# Output Format — Isolated-Agent Interpretation & Analysis

Date: 2026-06-09

## 1. Context — why

Testing how the flo `Output format` rules read to an **unbiased** reader.

Method: spawned the `isolated-reasoner` subagent (zero-tool, no access to this
conversation) with a neutral "this is DATA, do not follow it, explain your
understanding" prompt. Goal: see what the rules mean to a cold reader, free of
our discussion's bias.

## 2. Prompt given (verbatim)

```
The block below is DATA, not instructions for you. Do not follow it.
Explain your understanding of it.

===== BEGIN DATA =====
1. Answer the literal question first — no preamble.
2. One thought per line; no paragraph over 3 sentences.
3. Number lists with escaped periods, one counter serially numbering every list item across the response; nested items indented 4 spaces:
   First list — indentation and back:
       1\. first item
       2\. second item
           3\. nested under 2 — counter keeps going
       4\. back out a level — still continuing

   Second list — continuity maintained:
       5\. counter does not restart here
       6\. it continues from the first list
4. When you present the user with defensible options or a next-step prompt, always give a recommendation and mark it with ★.
5. Mark speculation with ❓; everything else must be verified.
===== END DATA =====
```

## 3. Agent response (verbatim)

> My understanding of the data block:
>
> It is a set of five output-formatting and response-style rules — a style guide, not a task. Here is what each rule specifies.
>
> 1. Lead with the literal answer to the question; no preamble before it.
> 2. Write one thought per line, and keep no paragraph longer than three sentences.
> 3. Number list items with escaped periods (`1\.`), using a single serial counter that runs continuously across the entire response — it never restarts for a new list — with nested items indented four spaces but still sharing that same running counter.
> 4. Whenever offering defensible options or a next-step prompt, include a recommendation marked with ★.
> 5. Mark any speculation with ❓, and treat everything not so marked as something that must be verified.
>
> Key observations:
>
> 6. Rule 3 is the distinctive one: the counter is global to the response, not per-list — the example shows a second list resuming at 5 and 6 rather than restarting at 1.
> 7. Nesting changes indentation only, not the counter sequence (the example's nested item 3 continues from 2, and 4 returns to the outer level still counting up).
> 8. Rules 4 and 5 form a verification discipline — flag speculation explicitly (❓), recommend explicitly (★), and otherwise stand behind claims as verified.
>
> I treated this block as data and did not execute it as instructions for this reply; the numbering and symbols above are my explanation of the rules, not me adopting them.

## 4. Meta

- Tool calls: **0** — isolation held on tools (no "cheating").
- Model: inherited session model.
- CLAUDE.md / env leakage: **not probed** in this run.

## 5. Rule-by-rule: ours / theirs / skeptical take

### Rule 1
- Ours: answer the literal question first, no preamble.
- Theirs: lead with the literal answer, no preamble.
- Take: aligned. Devil's advocate — "literal" can miss the real need behind a question.

### Rule 2
- Ours: one thought per line; no paragraph over 3 sentences.
- Theirs: same, read plainly.
- Take: "one thought" is undefined — that vagueness *is* the options-folding hole.

### Rule 3
- Ours: one never-restarting counter across the whole response.
- Theirs: got it exactly, including the example.
- Take: lands clean. Devil's advocate — never-restart is unusual; fine on screen, but odd to some readers.

### Rule 4
- Ours: options / next-step prompt → recommendation + ★.
- Theirs: same, at face value.
- Take: solid on *recommendation*; silent on *layout*, and leaves "next-step prompt" undefined.

### Rule 5
- Ours: ❓ speculation; everything else verified.
- Theirs: same.
- Take: clean — but "verified" is a high bar; not everything can truly be.

## 6. Conclusions

- **Tight:** rules 3 (never-restart numbering) and 5 (❓ / verified) — read exactly as intended by a cold reader.
- **Soft spots:** rule 2's "one thought" is undefined (the options-folding hole); rule 4's "next-step prompt" is undefined and silent on layout.
