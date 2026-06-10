# Goal

1. Find and understand the current state of the global scope:
   - skills
   - mcp servers
   - plugins
   - skill-level plugins
2. Determine how the npm package `skills` is tied to the current state — what it has done to it.

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

NOT YET GATHERED — need the non-interactive list command (`claude --help` confirms `-p` exists; a skill-list subcommand not yet found).

# npm package `skills` — tie to current state

NOT YET VERIFIED — the `npm ls -g skills` / `which skills` probe was rejected before running.
Circumstantial only (not a fact): `skills.sh` appears as an allowed WebFetch domain in settings.json.
