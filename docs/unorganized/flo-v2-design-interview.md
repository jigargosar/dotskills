# flo-v2 — Design Interview

The decisions and their rationale live in `flo-v2-design-decisions.md`. This file
captures *how those decisions were reached* — the questions that were asked, the
options on the table, what was chosen, and the thinking behind it. The question
workflow turned out to be the most valuable part of the session: a vague format idea
was turned into concrete, defensible choices by forcing answers to a handful of sharp
questions. That workflow is worth keeping.

Two layers:

- **Part A — Reusable question-bank.** The questions, generalized, so they can be
  reused when designing *any* output-format or behavior skill.
- **Part B — This session's Q&A.** The actual questions asked while designing flo-v2,
  with options, choices, and reasoning — including the messy parts, because the mess
  is where the real decisions got made.

---

## Part A — Reusable question-bank

When designing an output-format or behavior skill, force an answer to each of these
*before* writing the skill. Each one surfaced a real ambiguity in flo-v2 that a vague
spec had glossed over.

**Alignment & intent**
- How does the format confirm it understood the user's intent — by restating it, or
  by echoing it? (A restatement can reveal a misread; an echo cannot.)

**Scope & lifecycle**
- Is the format sticky (persists across turns once invoked) or per-invocation?
- Does it govern turns where the assistant does work (tool calls), or only turns
  where it answers?

**Rendering mechanics** (the questions most often skipped, and the ones that bite)
- Will the target renderer (markdown, terminal, web) mangle the intended structure —
  renumber lists, collapse indentation, reflow groups?
- What is the most robust carrier for *exact* structure: native markdown, escaped
  markdown, or fenced literal text?
- Can that carrier hold mixed content (real code, links), or must it split around it?
- Can the carrier nest inside itself?

**Reference & structure**
- How are items numbered so any line can be cited in the next message? (Continuous
  counter? One thought per line?)
- How is related content grouped and labeled?

**Decision support**
- When the user must choose, how is a single recommendation surfaced?

**Restraint & trust**
- What gets cut to avoid padding (reasoning, narration, preamble, apologies)?
- How are claims grounded, and how does tone track confidence? How is speculation
  marked so it stays useful instead of misleading?

**Interaction handling**
- How is a user's question interpreted — as a request for an answer, or (wrongly) as
  criticism / a hint to self-correct?
- How are corrections handled — re-emit the whole response or a diff; fix the output
  or explain the error?

**Safety & control**
- What gate exists before a tool call or world-changing action?

**Fallback**
- What happens when the format simply cannot be achieved for a given response?

---

## Part B — This session's Q&A

Chronological. Each entry: the question as posed, the options, the choice, and the
thinking. Maps to the decision IDs in `flo-v2-design-decisions.md`.

### Q1 — Alignment opener: paraphrase or verbatim echo? → D1
- **Options.** Paraphrase the understood intent / echo the user's exact words.
- **Chosen.** Paraphrase.
- **Thinking.** The opener exists to prove alignment. Only a paraphrase can expose a
  misunderstanding before it costs anything; a verbatim copy proves the message was
  received, not understood.

### Q2 — Scope: sticky mode or single response? → D2
- **Options.** Sticky (governs every turn until turned off) / single response.
- **Chosen.** Sticky.
- **Thinking.** A working style is set once. Re-invoking it every turn would defeat
  the speed the format is for.

### Q3 — Does the format wrap work turns, or only answer turns? → D3
- **Friction.** The distinction wasn't obvious at first ("what's the difference?").
  Clarified: an *answer turn* just replies; a *work turn* changes the world (edits a
  file, runs a command) and then reports. Four distinctive examples were requested to
  make the choice concrete — single edit, run-a-command, multi-step setup, and
  investigate-then-act.
- **Chosen.** Every turn, work included.
- **Thinking.** Consistency, and it pairs with the approval gate (Q8/D14): the user
  sees numbered steps before anything happens. Dropping to plain prose on work turns
  would lose referenceability exactly when stakes are highest.
- **Side effect.** This is where the rendering problem first showed itself — the four
  examples rendered as a scrambled, un-grouped wall, which forced Q5.

### Q4 — Plain-text fallback: whole response or just the hard part? → reframed
- **Outcome.** The framing was wrong and got discarded. The real problem wasn't "a
  code block appeared in the response"; it was that markdown mangles the intended
  structure *at all*. That reframing led directly to Q5.

### Q5 — How is the structure kept from breaking on render? (the pivotal thread) → D4
- **What was established.** The markdown renderer restarts numbered lists at 1 on
  every new list block and collapses indentation. So a counter typed continuously
  across two groups renders as "1, 2, 1" — the unbroken-counter promise silently
  breaks, and grouping dissolves into a wall. This was proven *live*: the assistant's
  own example output rendered scrambled in front of the user.
- **Options considered.** Native markdown lists (renumbered by the renderer);
  escaped-period markdown like `1\.` (fragile, and the approach in the predecessor
  that "just doesn't work"); fenced literal text.
- **Chosen.** Plain text always — the structured body lives inside code fences, the
  one construct the renderer reproduces exactly as written.

### Q6 — Styled opener + fenced body? Multiple fences? → D5
- **Options.** Whether the blockquote opener could stay native (styled, unnumbered)
  while the body is fenced; whether several fences are allowed in one response.
- **Chosen.** Yes to both. The opener is a real markdown blockquote (styled, no
  number, safe because it's a single line with no list to mangle); the body is fenced;
  multiple fences are allowed and the counter continues unbroken across them.

### Q7 — Can fences nest? Real code inside a text fence? → D6
- **What was established.** A real code block nested inside a text fence renders as
  dead literal text — visible backticks, no highlighting — because the outer fence
  makes everything inside it literal.
- **Chosen.** Don't nest. Split the text fence around real code: close it, emit the
  code as its own language-tagged fence (un-numbered), then reopen a text fence. The
  counter pauses for the code and resumes after.

### Q8 — Mid-stream rule the user added → D14
- Before any tool call or world-changing action, present the steps in-format and wait
  for explicit approval; on approval, execute and report in-format; on any deviation
  from the approved steps, stop. This is the reason the format wraps work turns at all
  (Q3).

### Decisions not gated by an explicit question
These came from the user's initial requirements rather than a back-and-forth, but are
recorded for completeness: continuous counter + one-thought-per-line (D7); grouping
under labels with 4-space indent (D8); one ★ recommendation on choices (D9); no
padding (D10); receipts and confidence-matched tone (D11); questions get direct
answers (D12); corrections re-emit the whole response and fix the output not the story
(D13); plain-text fallback when the format can't hold (D15).

---

## Open items (as of this session)

1. **SKILL.md prose provenance.** The first SKILL.md draft borrowed wording from the
   predecessor `flo` skill ("move fast and stay aligned," "change the output not the
   story," "say only what you've checked," the ★ pattern), which the user flagged. The
   mechanics are original to this session; the prose wrapping them is not. A rewrite to
   original framing was offered and is **pending** — the user paused before deciding.
2. **Doc placement.** These two files were created in the working folder for speed;
   final placement (e.g. a `docs/` folder inside the skill) is deferred.
3. **Testing.** No eval runs yet. flo-v2 is largely a subjective formatting skill, but
   several properties are objectively checkable (opens with a blockquote, body is
   fenced, counter is continuous and unbroken, choices carry exactly one ★) and could
   be asserted if a test loop is wanted.
