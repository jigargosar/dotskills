# Skill builder & reviewer tools — research

Question: which are the most popular skills/tools that help **create** and **test** robust Claude Code skills, with hard data for comparison.

- Observation date for all numbers: **2026-06-12**
- Method: 3 parallel research agents (sonnet) using WebSearch / WebFetch + GitHub API.
- Caveats: star counts could not be independently re-verified; several registry figures are single-source (flagged inline).

---

## Tier 1 — full lifecycle (create + eval/test)

These are the tools that both author skills and quantitatively test them — the direct answer to the question.

### skill-creator (Anthropic, official) — recommended

- Repos: `anthropics/claude-plugins-official` **29.9k★ / 3.2k forks**; spec repo `anthropics/skills` **150k★ / 17.7k forks** (Apache 2.0).
- SKILL.md: https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md
- Plugin page: https://claude.com/plugins/skill-creator
- **Create:** interview-driven creation, intent capture, SKILL.md drafting, description-trigger optimization.
- **Test / eval:**
  - Evals mode — runs skill against test prompts in `evals/evals.json`, grades each assertion PASS/FAIL via `agents/grader.md`.
  - Benchmarking — parallel with-skill vs baseline runs, captures `total_tokens` + `duration_ms`, aggregates into `benchmark.json`.
  - Variance analysis — mean ± std dev across runs; flags high-variance / flaky evals.
  - A/B blind compare — `agents/comparator.md` judges two outputs without knowing the version; `agents/analyzer.md` explains the winner.
  - Interactive HTML review UI (`generate_review.py`) — pass rates, timing, token usage.
- Only official tool with a real eval harness; the reference implementation that others build on.

### daymade/claude-code-skills — best community option

- Repo: https://github.com/daymade/claude-code-skills — **1.2k★ / 183 forks**, MIT, 61 skills.
- Plugin suite (`daymade-skill:<name>`):
  - `skill-creator` — guided creation with checkpoints, security scanning (gitleaks), validation scripts; same eval infra (60/40 train/test split, 3-run variance, HTML viewer, up to 5 iteration cycles, 20 synthetic trigger queries → `best_description`).
  - `skill-reviewer` — three modes: self-review, external clone-and-audit, auto-PR with additive fixes; checks frontmatter, instructions, resources.
  - `skills-search` — search/install from the CCPM registry.
- Adds an external review/audit step on top of evals.

### claude-code-skill-factory (alirezarezvani)

- Repo: https://github.com/alirezarezvani/claude-code-skill-factory — **800★ / 158 forks**.
- Create-primary: interactive builders and templates; includes a `/test-factory` command for basic output validation.

### opencode-skill-creator (antongulin)

- Repo: https://github.com/antongulin/opencode-skill-creator — **114★ / 7 forks**.
- Port of Anthropic's skill-creator to OpenCode (TypeScript). Tools: `skill_eval`, `skill_improve_description`, `skill_optimize_loop`, `skill_aggregate_benchmark`. Generates eval test sets, measures trigger accuracy, iterative optimization up to 5 rounds. Eval features mirror daymade's.
- Not Claude Code native — included as a parallel-ecosystem signal.

---

## Tier 2 — test / lint / eval only (no authoring)

| Tool | Stars/forks | Role |
|------|-------------|------|
| [agnix](https://github.com/avifenesh/agnix) (avifenesh) | 283★ / 28 | Linter/validator for SKILL.md, CLAUDE.md, hooks, MCP configs; 423 rules, auto-fix, LSP server for real-time editor diagnostics |
| [SkillCheck-Free](https://github.com/olgasafonova/SkillCheck-Free) (olgasafonova) | 32★ / 1 | Validates SKILL.md against the agentskills spec; 30+ structure/naming/semantic checks; no external API calls |
| [claude-code-test-runner](https://github.com/firstloophq/claude-code-test-runner) (firstloophq) | 22★ / 4 | NL E2E via Playwright + Claude Code SDK + Test State MCP; web-UI focused, not skill-trigger testing |
| [pulser](https://github.com/TheStack-ai/pulser) (TheStack-ai) | 16★ / 0 | Linter/validator: 8 rules (frontmatter, description quality, ≤500-line size, gotchas, allowed-tools, structure, trigger-overlap conflicts, usage-hooks); `--fix` with rollback; `eval.yaml` regression; GitHub Action for CI; ~200ms for 40+ skills |
| [claude-eval](https://github.com/bkper/claude-eval) (bkper) | 12★ / 0 | LLM-as-judge (Sonnet runs prompt, Haiku judges); YAML criteria; pass/fail only; `npx claude-eval evals/*.yaml` |
| [claudelint](https://github.com/pdugan20/claudelint) (pdugan20) | 8★ / 0 | Broad validator: CLAUDE.md, skills (frontmatter schema, structure, referenced files, security), settings JSON, hooks, MCP, plugins/manifests |
| prism-scanner (via [rohitg00 toolkit](https://github.com/rohitg00/awesome-claude-code-toolkit)) | n/a | Security scanner for skills/plugins: 39+ rules, AST taint tracking, A–F grading |

- pulser self-reported impact: broken skills shipped ~3/month → 0 in 6 weeks (DEV.to article, self-reported).

---

## Tier 3 — create-only / qualitative (no quantitative eval)

### obra/superpowers — `writing-skills`

- Repo: https://github.com/obra/superpowers — **225k★ / 20k forks** (community; Jesse Vincent / Prime Radiant).
- Highest star count of any tool here, but a general dev-methodology framework, not a skill-authoring tool specifically.
- `writing-skills` uses TDD-adapted authoring (RED baseline without skill → GREEN write → REFACTOR close loopholes) and pressure-scenario testing via subagents.
- Testing is **qualitative only** — no eval harness, no metrics, no variance analysis.

---

## Registry / marketplace adoption (installs, not stars)

- **CCPM** (https://ccpm.dev; manager repo https://github.com/kaldown/ccpm, 49★): 107 skills; 5,563 downloads last week / 23,184 last month. Top skill by installs is `skill-creator` — **15,420 downloads** (#1). No skill-reviewer/tester in the top 6. *Single-source.*
- **claudemarketplaces.com**: 21,600+ skills. Authoring tools do not rank top — discovery skill `find-skills` leads at **1.8M installs**. *Single-source.*
- **smithery.ai**: hosts `claude-code-plugin-builder`, `marketplace-manager` — install counts not retrievable (403/429).
- **skills.sh** (Vercel, npm-style `npx skills add`, launched Jan 2026): no live popularity numbers found.
- **SkillKit** marketplace: referenced as 400k+ skills; no popularity data found.

## Curated lists (context)

| List | Stars/forks |
|------|-------------|
| [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 64.2k★ / 7.1k |
| [hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code) | 46.2k★ / 4k |
| [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) | 29.9k★ / 3.2k |
| [VoltAgent/awesome-claude-code-subagents](https://github.com/VoltAgent/awesome-claude-code-subagents) | 21.6k★ / 2.5k |
| [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) | 13.4k★ / 1.5k |
| [BehiSecc/awesome-claude-skills](https://github.com/BehiSecc/awesome-claude-skills) | 9.5k★ / 1.2k (lists skill-creator, template-skill, SkillCheck-Free, agnix, skill-optimizer) |
| [rohitg00/awesome-claude-code-toolkit](https://github.com/rohitg00/awesome-claude-code-toolkit) | 2k★ / 658 |
| [karanb192/awesome-claude-skills](https://github.com/karanb192/awesome-claude-skills) | 375★ / 107 (lists skill-creator, template-skill, writing-skills) |

---

## Bottom line

1. For **create + test robust skills**: **Anthropic skill-creator** wins on test depth (parallel evals + variance/benchmark stats), official status, and registry installs (#1 on CCPM).
2. Add **daymade/claude-code-skills** for an external review/audit pass (`skill-reviewer`).
3. Add **pulser** or **claudelint** as a CI lint gate.
4. **superpowers/writing-skills** has the most stars but no quantitative eval — good methodology, not measurement.

## Open caveats

- `anthropics/skills` reported as 149,606 vs 150k vs a cached 111.4k (claudemarketplaces) across sources.
- CCPM and claudemarketplaces install figures are single-source and uncross-verified.
- npm download counts for pulser / claude-eval: not found.
- Tessl / LobeHub install counts for daymade skills: not found (404/403).
