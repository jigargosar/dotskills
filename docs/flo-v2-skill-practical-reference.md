# flo-v2 skill reference — failures to clear (the bar)

## Approval & intent
- Acts/decides without approval — proposing is the limit (reads/searches included).
- Infers intent instead of asking.

## Output format
- Presents choices with no recommendation.
- Two questions in one line, esp. the closing lead-to-next line.
- No grouping; too many points to scan.
- Overshoots smallest-complete (TMI).
- Numbering rule over-complected — simplify or drop.

## Grounding
- States reasoning / motive / unfounded claims as fact.

## Concerns:
- When to use ; attribute need to be added.


## Dump of responses that were required to make it work

NOTE: This section is not organized and ambitious read it with a pinch of salt.

concise issues:

questions don't get answered without padding. I need to ensure that direct questions get direct simple answer!
also claims when cited, shouldn't cause further response. not long response at least.

remove run in bg, always. it was onetime thing. perhaps.

global claude needs rewriting.

---
Recording answers/fixes for real improvement to skill.

output no grouping cant read
output points too much to digest/comphrend.
output last line contains multiple questions smuggled as one point
when correcting output retain rest of output verbatim!
output is missing recommendation

This command uses shell operators that require approval for safety.
overcorrecting: splitting commands that dont even have shell operator

too many roundtrips to answer simple question that can be easily checked for, but overconfidence of interpretation causing multi turn conversation

Also try to limit your answers to my question.

self correcting rule:

Corrected rule for me: when you ask to see a file, Read that exact file and print it. No context recall, no substitution, no commentary.

Ground your output to reality.

**wrong, better to ask than assume my intentions.
before taking steps you need to finish current conversation.**
before executing, you need to show your steps and STOP.

first line not block quote
what if ask was not a question but an instruction, ? we need to give explicit inst to avoid confusion.

**restart /presentation mode.
absoloutely bad formatting, 2 options in second to last line!!**
**then seperate line for recommendation, terrible interpretion of instructions. one got fixed other started bracking.
present skill shouldn't number x of y and y remaining.


/present skill format.
▎ the line gaps are important, after quote, after context, then options clubed together. footer: x of y, z remaining.**

▎ block-quote: prior clean decision

1. context of the item.

2. ★ recommended option
3. other option(s), clubbed — no blank between them

4. footer: x of y, z remaining.

Gaps: blank after quote, blank after context, options grouped tight, blank before footer.

**✻ Baked for 8s

---**

Use templates at least for /present
generic responses can also use templates.
or yml with numbers :)

---
❯ Always show steps. STOP, let user decide what to do next. Never take any decisions unitarily

---

good words, output and tools

---

This exactly the reason, to not show reasoning line of chain, including applogy and its reasoning chain.

was not required, was expansion of unnecessary claim.

❯ didnt follow instruction, made more changes than asked. without confirmation of extrachanges.

