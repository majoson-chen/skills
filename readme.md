# Personal Agent Skills

Small collection of [Agent Skills](https://agentskills.io/specification) for Cursor / compatible agents.

## Skills

| Skill | Path | Summary |
|--------|------|---------|
| **commit** | [`commit`](skills/commit) | Safe, structured git commit flow: diff/history, staging, risk checks, split commits, message style. Use with `/commit` or when drafting commits. |
| **openclaw-plugin-dev** | [`openclaw-plugin-dev`](skills/openclaw-plugin-dev) | OpenClaw native plugin reference (manifest, loader, API, hooks, channels, providers). Anchored to `openclaw@2026.3.28`. |
| **using-agentsmd** | [`using-agentsmd`](skills/using-agentsmd) | Create/maintain AGENTS.md: prompt-style writing, red/green lines, placement routing, nesting, audit trimming. Load only when writing or editing. |
| **using-subagent** | [`using-subagent`](skills/using-subagent) | Load when subagents are available (session-resident), user mentions subagents, or work runs on a subagent; references/ only before dispatch. |

## Install

Use the [skills CLI](https://github.com/vercel-labs/skills) — see **[skills.sh docs](https://skills.sh/docs)** for commands and options (e.g. `npx skills add majoson-chen/skills --skills [name]` once this repo is on GitHub).
