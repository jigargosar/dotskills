# Goal

1. Find and understand the current state of the global scope:
   - skills
   - mcp servers
   - plugins
   - skill-level plugins
2. Determine how the npm package `skills` is tied to the current state — what it has done to it.

Snapshot: 2026-06-10

# MCP servers (user-configured)

Source: `claude mcp list`. These are user-scope servers only; plugin-provided MCPs are not listed here.

1. claude.ai Gmail — https://gmailmcp.googleapis.com/mcp/v1 — Connected
2. claude.ai Context7 — https://mcp.context7.com/mcp — Connected
3. shadcn — `npx.cmd shadcn@latest mcp` — Connected

# Plugins

Source: `claude plugin list`.

| Plugin | Version | Scope | Status |
|---|---|---|---|
| daymade-skill@daymade-skills | 1.1.0 | local | disabled |
| frontend-design@claude-plugins-official | unknown | user | enabled |
| ralph-loop@claude-plugins-official | 1.0.0 | user | enabled |
| remember@claude-plugins-official | 0.7.3 | user | enabled |
| skill-creator@claude-plugins-official | unknown | user | enabled |
| superpowers@claude-plugins-official | 5.1.0 | user | enabled |

# Skill-level plugins (.claude/skills/*)

Source: `claude plugin list` (separate "Skills-directory plugins" section).

1. kitchen-sink@skills-dir — v0.1.0 — user scope — path `~\.claude\skills\kitchen-sink` — loaded.
   - A skills directory that is also a plugin container; its sub-skills load as `kitchen-sink:<name>` (e.g. flo, opn, critical-thinker).

# Marketplaces

Source: `claude plugin marketplace list`.

1. claude-plugins-official — GitHub anthropics/claude-plugins-official
2. chrome-devtools-plugins — GitHub ChromeDevTools/chrome-devtools-mcp
3. daymade-skills — Git github.com/daymade/claude-code-skills.git
4. dpt-plugins — GitHub Digital-Process-Tools/claude-marketplace

# Skills (global)

Source: `skills ls -g --json` plus `ls -la` of the skill roots. Standalone skills live in two roots.

Your own skills (plain folders in `~/.claude/skills/`, Claude Code only):

1. chezmoi-sync
2. discuss
3. frontend-baseline
4. git-init
5. language-elm
6. language-typescript
7. vite-ts-init

Installed by the npm `skills` CLI (real copies in `~/.agents/skills/`, symlinked into `~/.claude/skills/`, registered for 8 agents):

1. openspec-apply-change
2. openspec-archive-change
3. openspec-explore
4. openspec-propose

Plus the skill-level plugin `kitchen-sink` (see above) and all marketplace-plugin skills — neither is managed by the `skills` CLI.

# npm package `skills` — tie to current state

What it is: a CLI (skills.sh) that installs agent-skill packages from GitHub into skill folders, for one or many agents. Default is symlink; `--copy` copies.

What it did to your current state — exactly one thing, verified by `ls -la`:

1. Installed the 4 openspec skills: stored real copies in `~/.agents/skills/`, then symlinked them into `~/.claude/skills/`. They are registered across 8 agents (Claude Code, Codex, Gemini CLI, Copilot, Cline, OpenCode, Antigravity, Antigravity CLI).

2. Nothing else. The 7 Claude-only skills above are plain folders (not symlinks) — your own, not placed by the CLI's symlink mechanism. Plugins, MCP servers, and kitchen-sink are untouched by it.

3. Evidence: in `~/.claude/skills/`, only the 4 openspec entries are symlinks (`-> ~/.agents/skills/...`); everything else is a real directory.

4. Caveat: a plain folder could in theory come from `skills add --copy`, so "plain = yours" is highly likely but not airtight. A symlink is definitive proof of CLI management.
