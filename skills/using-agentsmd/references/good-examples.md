# Good examples

Each example has a tag. Learn *why* it works — do not treat the layout as a mandatory template.

---

## Tag: orientation + routing + boundaries (skills-repo style)

```markdown
# Agent notes — personal Agent Skills

Small [Agent Skills](https://agentskills.io/specification) collection for coding agents.

## Authoring skills

Single entry point: load and follow `skill-creator` (`.agents/skills/skill-creator/SKILL.md`).

## Skill index

| Skill | Path | When |
| commit | skills/commit/ | /commit, commit messages |
| openclaw-plugin-dev | skills/openclaw-plugin-dev/ | OpenClaw plugin work |
| using-agentsmd | skills/using-agentsmd/ | create/maintain AGENTS.md |

Load the full skill file before running a workflow.

## Pre-ship checklist

- [ ] readme.md table updated when skills change
- [ ] no secrets in the diff

<important-notes>
This file is `AGENTS.md`. Reload `using-agentsmd` when project facts change.
</important-notes>
```

**Why it works:** brief orientation; workflows routed, not inlined; table is an index, not a tutorial; boundaries without rationale; `<important-notes>` elevates the maintenance block.

---

## Tag: key directories (placement)

```markdown
## Key directories

- `src/features/` — feature modules; new pages go here
- `server/routes/` — HTTP routes
```

**Why it works:** shallow list that guides placement, not a deep tree.

---

## Tag: green lines (affirmative commands)

```markdown
## Commands

- full test suite: `pnpm test`
- single test: `pnpm vitest run -t "<name>"`
- package-scoped: `pnpm --filter <pkg> <script>`
```

**Why it works:** copy-paste ready; no contrast noise; no explanatory paragraphs.

---

## Tag: one-line Skill route

```markdown
## Git

- commits: load `skills/commit/SKILL.md`
```

**Why it works:** triggers the Skill without pasting its full body into context.

---

## Tag: nested increment only

`packages/api/AGENTS.md` contains only:

```markdown
# API package

## Commands

- package tests: `pnpm test`

## Boundaries

- do not edit `src/generated/`

<important-notes>
This file is `packages/api/AGENTS.md`. Reload `using-agentsmd` when project facts change.
</important-notes>
```

**Why it works:** does not repeat root commit rules or repo-wide routes; footer names the nested file path.

---

## Tag: trailing `<important-notes>`

```markdown
<important-notes>
This file is `AGENTS.md`. Reload `using-agentsmd` when project facts change.
</important-notes>
```

For `CLAUDE.md`, use `CLAUDE.md` as the path; for nested files, use that file's path (e.g. `packages/api/AGENTS.md`).

**Why it works:** consistent template raises salience; path matches the file being maintained; reload points at `using-agentsmd`.

---

## Tag: trailing `<important-notes>` (non-English repo)

```markdown
<important-notes>
此文件为 `AGENTS.md`。事实变更时加载 `using-agentsmd` 及时修改。
</important-notes>
```

**Why it works:** inner text may follow maintainer language; tag name stays `important-notes`.
