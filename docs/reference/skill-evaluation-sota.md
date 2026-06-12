# Skill evaluation — state of the art

Snapshot: 2026-06-12. A reference capture of how the field tests/evaluates agent
skills, distilled to what changes our flo-v2 work. Read the TL;DR; the rest is
context for when a decision needs it.

## Contents
- TL;DR — what to actually do
- The core loop (eval-driven, baseline-differential)
- What's SOTA beyond the basic loop
- Metric set worth tracking
- Application to flo-v2 (output-style skill)
- Sources

## TL;DR — what to actually do

Four moves carry almost all the value for a one-author output-style skill:

1. **Run each eval prompt N times, not once.** Compliance varies run-to-run; n=1
   misleads on whether a change helped. Highest-value takeaway.
2. **Grade by per-property binary checks, not one holistic score.** e.g.
   "blockquote present? lists numbered right? code-fenced? no padding?" — each
   yes/no. Converts subjective into near-objective.
3. **Add false-trigger cases.** Prompts where the skill should stay OFF, so you
   measure description precision, not just recall.
4. **Keep the baseline arm.** With-skill vs. without is the whole experiment.

Overkill for our scale, file for later: sandboxed per-run environments, judge
ensembles, harness-variance tuning.

## The core loop (eval-driven, baseline-differential)

Anthropic, LangChain, and the agent-eval frameworks converge here. A skill is a
*delta* on the model, so you measure the delta:

1. Run representative tasks **without** the skill → document specific failures.
   This baseline run is the experiment, not an afterthought.
2. Build evals targeting exactly those gaps. Anthropic: start with ~3 scenarios,
   *before* writing the skill body.
3. Write minimal skill content to close the gaps.
4. Re-run with-skill vs. baseline, compare, iterate.

LangChain's headline: 82% task completion with skill vs. 9% without — meaningful
only because both arms ran identical constrained tasks in identical environments.

## What's SOTA beyond the basic loop

**Constrained tasks + programmatic grading beat open-ended + LLM-judge.**
"Fix this buggy code" with predefined pass/fail tests gives clean signal;
open-ended tasks end up measuring model reasoning, not skill utility. Use a
machine-checkable oracle where output allows. Output-style skills are the
exception — output quality is genuinely subjective, so a judge is unavoidable.

**Clean, reproducible environment is a first-class variable.** Coding agents
explore the directory before acting; what they find changes their approach. SOTA
sandboxes each run (Docker/Harbor) for identical starting conditions. Cautionary
tale: a Berkeley 2026 audit drove eight major agent benchmarks to near-perfect
scores *without solving anything* — leaked answers, scoring functions skipping
correctness. Treat your grader as attackable.

**Variance is the central measurement problem.**
- Same model in two harnesses swings 10–20 points on the same benchmark —
  methodology dominates the model signal. Single-run before/after is near
  meaningless.
- Fix: multiple runs per condition (pass@k thinking); report variance, not a
  point estimate.
- LLM-as-judge specifics: binary or 3-point rubrics (not 1–10 — low-precision
  scales cut judge variance); explicit rubrics matter more than temperature;
  ensembles of 3–5 judges shrink variance and self-preference bias. Pairwise/
  blind comparison is more reliable than pointwise but ~quadratic in calls.
  Judges agree with humans ~85–90%; critical cases still get human grading.

**Triggering is a separate eval from execution.** Two metrics: invoked when
relevant AND silent when irrelevant (false-trigger rate). LangChain saw only
~70% invocation even when relevant. Measured against name/description metadata,
independent of body quality. A perfect skill that never fires scores zero.

**Skill-set-level effects.** Performance degraded with ~20 similar skills loaded;
skills past 300–500 lines showed diminishing returns. Eventually test a skill in
the presence of its neighbors — triggering accuracy especially degrades there.

## Metric set worth tracking

| Metric | Why |
|---|---|
| Task completion (vs. baseline) | The headline delta |
| Invocation rate + false-trigger rate | Triggering is its own failure mode |
| Turns / steps to completion | Skills can succeed yet slow the agent |
| Variance across N runs | A point estimate is noise at n=1 |
| Per-model results (Haiku/Sonnet/Opus) | Effectiveness is model-dependent |

## Application to flo-v2 (output-style skill)

flo-v2 formats output → subjective, no clean pass/fail oracle → judge-bound.
Two emphases follow:

- **Invest in the judge.** A tight binary/3-point rubric per property (one-line
  blockquote present? lists numbered correctly? no padding? code-fenced so
  markdown can't renumber?), each graded as an independent pass/fail check,
  ideally by a 3-judge ensemble. Converts "subjective" into many near-objective
  checks.
- **Run each eval prompt multiple times per arm.** Formatting compliance is
  exactly what varies run-to-run; n=1 will mislead on whether a change helped.

This maps onto the skill-creator process table: baseline + with-skill same-turn
(step 5), variance-aware grading/aggregation (step 8), blind comparison
(step 12), triggering optimization (step 13).

## Sources
- Anthropic — Skill authoring best practices: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
- LangChain — Evaluating Skills: https://www.langchain.com/blog/evaluating-skills
- Confident AI — LLM Agent Evaluation Metrics 2026: https://www.confident-ai.com/blog/llm-agent-evaluation-complete-guide
- Hugging Face — AI evals are becoming the new compute bottleneck (Berkeley gaming + harness variance): https://huggingface.co/blog/evaleval/eval-costs-bottleneck
- Promptfoo — LLM-as-a-Judge guide: https://www.promptfoo.dev/docs/guides/llm-as-a-judge/
- SurePrompts — LLM-as-Judge practical guide 2026 (binary scales, ensembles, variance): https://sureprompts.com/blog/llm-as-judge-prompting-guide
- Medium (Adnan Masood) — Rubric-based evals & LLM-as-a-Judge: https://medium.com/@adnanmasood/rubric-based-evals-llm-as-a-judge-methodologies-and-empirical-validation-in-domain-context-71936b989e80
