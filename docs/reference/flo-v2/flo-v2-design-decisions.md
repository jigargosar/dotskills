# flo-v2 — Decision Record

This file records the design decisions behind flo-v2 and *why* each was made. The
skill's wording will change over time; these decisions are the stable part. If a
future edit to SKILL.md seems to contradict something here, that is a signal to
stop and reconcile — either the decision changed on purpose (update this file) or
the edit drifted (fix the edit).

Each entry: the decision, the reason, and the alternative that was rejected.

---

## The four goals everything serves

flo-v2 exists to make every response: **fast to scan**, **trivially
referenceable** (any line can be cited in the next message), **aligned** (the user
can confirm intent was understood), and **honest** (no padding, no overclaiming).
Every decision below traces back to one of these.

---

## D1 — Alignment opener is a paraphrase, not a verbatim echo

**Decision.** Every response opens with a one-line restatement of the user's intent,
written in the assistant's own words.

**Why.** The opener is an alignment check. A paraphrase is the only form that can
expose a misunderstanding — if the restatement is wrong, the user catches it before
it costs anything. A verbatim copy proves only that the message was received, not
that it was understood.

**Rejected.** Echoing the user's exact words back.

---

## D2 — flo-v2 is a sticky mode

**Decision.** Once invoked, flo-v2 governs every following turn until the user
explicitly turns it off. It is not re-invoked per message.

**Why.** It is a working style, and a working style is set once. Making the user
re-invoke it each turn would defeat the "fast" goal.

**Rejected.** Single-response scope (applies only to the turn where it is invoked).

---

## D3 — The format wraps work turns too, not just answers

**Decision.** Turns where the assistant does work (edits files, runs commands)
follow the format as well: the proposed steps and the results are both in-format.

**Why.** Consistency, and it pairs with the tool-approval rule (D14) — the user sees
the numbered steps before anything happens. Switching to plain prose for work turns
would break referenceability exactly when it matters most.

**Rejected.** Format on answer-only turns; plain prose on work turns.

---

## D4 — Plain text always; the structured body lives inside code fences

**Decision.** The numbered, grouped, indented body is emitted as literal text inside
code fences — never as native markdown lists.

**Why.** This is the mechanical core of the whole skill. The markdown renderer
restarts numbered lists at 1 on every new list block and collapses indentation, so a
counter typed continuously across two groups renders as "1, 2, 1" — the unbroken-
counter promise (D7) silently breaks, and the grouping (D8) dissolves into a wall.
A code fence is the one construct the renderer reproduces exactly as written. This
decision was reached the hard way: a draft response rendered as a scrambled,
un-grouped block in front of the user, which proved the point directly.

**Rejected.** Native markdown lists (renumbered by the renderer). Escaped-period
markdown like `1\.` (works but fragile, and was the approach in the predecessor that
"just doesn't work").

---

## D5 — Styled blockquote opener + fenced body; multiple fences allowed

**Decision.** The alignment opener (D1) is a real markdown blockquote (`> ...`),
outside any fence, so it renders as a styled quote with no number. The body follows
in one or more code fences. Styled prose and blockquotes may sit between fences;
numbered or indented lists may not. The counter (D7) continues unbroken across
multiple fences.

**Why.** The opener benefits from real styling and carries no number, so it can stay
native markdown safely (a single line, no list to mangle). The body must be fenced
(D4). Allowing multiple fences lets real code (D6) and styled prose be interleaved
without breaking the body's structure.

---

## D6 — Real code is split out into its own fence, un-numbered

**Decision.** To show real (syntax-highlighted) code, close the text fence, emit the
code as its own language-tagged fence, then reopen a text fence to continue. The
code block carries no flo numbering; the counter pauses for it and resumes after.

**Why.** A code block nested inside a text fence renders as dead literal text —
visible backticks, no highlighting — because the outer fence makes everything inside
it literal. Splitting is the only way to get a real, highlighted block. Numbering the
code itself would be meaningless.

**Rejected.** Nesting a real code block inside the text fence.

---

## D7 — One continuous counter; one thought per line

**Decision.** A single counter runs across the entire response — serial, sequential,
unique, never restarting — and continues across groups and across fences. Each line
is one thought; a line carrying two thoughts is split into two items.

**Why.** This is what makes the response referenceable: the user can reply "redo 4
and 7" without re-describing anything. A restarting counter or a two-thoughts line
makes references ambiguous.

---

## D8 — Lines are grouped under labels, indented four spaces

**Decision.** Related lines sit under a short label that names the group (`Plan:`,
`Risks:`). Every line under a label is indented four spaces. One blank line between
groups, none within a group.

**Why.** Grouping is what makes a response scannable instead of a flat list. The
4-space indent and blank-line rules give the grouping a consistent, readable shape —
and because the body is fenced (D4), that shape survives rendering.

---

## D9 — Choices always carry one ★ recommendation

**Decision.** Any output that asks the user to choose marks exactly one option with
the ★ character as the recommendation, with a short reason.

**Why.** When the user is choosing, leaving them without a recommendation pushes work
back onto them that the assistant could have done. One ★ — not zero, not several —
keeps the recommendation unambiguous.

---

## D10 — No padding

**Decision.** Skip reasoning, narration, preamble, and apologies. Emit substance only.

**Why.** Padding buries the answer and slows every turn. The user did not ask to
watch the assistant think.

---

## D11 — Receipts, and tone that matches confidence

**Decision.** Claim only what was checked or can be pointed to (a file, a command's
output, a line that was read). Low-confidence receipts are acceptable, but the tone
must match the confidence. Speculation is welcome — labeled, and unpadded.

**Why.** Overclaiming poisons trust: one confident-sounding invention makes every
later claim suspect. Honest hedging keeps speculation useful instead of misleading.

---

## D12 — A question gets a direct answer

**Decision.** When the user asks a question, answer that question directly. Do not
treat it as criticism, as a request to review work for mistakes, or as a hint to
self-correct.

**Why.** Reading every question as a coded complaint wastes the turn and erodes
trust. If the user wants a change, they will ask for one.

---

## D13 — Corrections re-emit the whole response, and change the output not the story

**Decision.** On correction, change the output — not the explanation of why the error
happened. Do not narrate the mistake or the intended fix. Re-emit the entire response
with the fix folded in, never a diff.

**Why.** Narrating the error is fresh guessing on top of a possibly-wrong reading of
the correction, and it is padding (D10). Re-emitting in full means the latest message
always stands on its own — the user never reassembles the answer from an old version
plus a patch.

---

## D14 — Steps and explicit approval before any tool or action

**Decision.** Before any tool call or world-changing action, present the steps
in-format and wait for explicit approval. On approval, execute and report results
in-format. On any deviation from the approved steps, stop and surface it.

**Why.** This is the reason the format wraps work turns at all (D3): the user sees
exactly what is about to happen, numbered, before it happens, and stays in control.

---

## D15 — Plain-text fallback when the format cannot hold

**Decision.** If even fenced plain text cannot carry the structure for some response,
fall back to plain text rather than let markdown mangle it.

**Why.** A clean plain answer beats a numbered one that renders scrambled. Because
the fence-everything rule (D4) is robust, this fallback should almost never be needed
— but the floor is "never ship a mangled response."
