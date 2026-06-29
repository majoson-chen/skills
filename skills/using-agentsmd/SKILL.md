---
name: using-agentsmd
description: Load before creating or maintaining AGENTS.md (same rules apply to CLAUDE.md). Use when the user asks to write, edit, audit, or init the file, or when you are about to create, edit, or append any root or nested AGENTS.md. Do not load when you are only following already-injected AGENTS.md content for other work. Covers prompt-style writing, red/green lines, placement and routing, nesting, and audit-driven trimming. Not for general coding tasks.
---

# using-agentsmd

Follow this skill before you create or modify any `AGENTS.md`. Claude Code uses `CLAUDE.md` with the same discipline; below, `AGENTS.md` means both unless noted.

## Hard gate

1. About to `create` / `edit` / `append` any root or nested `AGENTS.md` → **load this skill first**
2. Do not change `AGENTS.md` without a user prompt; edits require an explicit request or prior permission
3. When the user says "put this in AGENTS.md" → run placement judgment first (below); write only if they still want it there
4. Executing other tasks under already-injected `AGENTS.md` → **do not load this skill**
5. No `AGENTS.md` in the repo → **do not create one by default**; add when there are stable, non-obvious conventions or the user asks

## What it is

`AGENTS.md` is a repo-level, passively injected **high-signal prompt snippet**.

- Done well: fewer mistakes, faster defaults, less repeated human steering
- Not: human docs, an encyclopedia, a changelog, or a copy of Skills / Rules / README

## Prompt discipline

Write it like a **prompt**, not a README.

- **Affirmative phrasing**: say what to do and which tool/path to use; do not enumerate error paths that have not happened
- ❌ `use pnpm --filter <pkg>, don't blindly run from repo root` → ✅ `package-scoped tasks: pnpm --filter <pkg> <script>`
- One action per line; commands and paths must be complete
- **State constraints and practice only — no rationale**
- No meta-narrative ("this section explains…") and no conversational filler; **exception:** trailing `<important-notes>` maintenance block (see Workflow)

**Red lines** (don't / never): only when the mistake is frequent and costly if unstated. Otherwise omit.

**Green lines** (prefer / use X / put files here): the fastest, correct way in *this* repo.

## What belongs / what does not


| Include | Exclude |
| ------- | ------- |
| 2–4 sentences of orientation (what repo, scope) | Long background, vision, market positioning |
| Shallow key directories (for **where to put files**) | Deep trees, per-file inventories |
| Non-obvious, copy-paste-ready commands | Commands already in manifest / README |
| Green lines and necessary red lines | Generic best practices, user-rules paraphrase |
| Routing pointers (Skills / docs / nested `AGENTS.md`) | Full workflows, long architecture/API prose |
| Trailing `<important-notes>` on new or major rewrites (see Workflow) | Line-by-line edit notes, change history |


**Litmus test:** Would this still matter with a different maintainer or a different agent tool? If no → it does not belong in `AGENTS.md` (solo-maintainer repos may keep personal prefs pragmatically).

**Point, don't paste:** link to Skills and docs; never copy their bodies. Workflows need a route in `AGENTS.md` or they will not auto-trigger.

## Other context carriers


| Carrier | Holds |
| ------- | ----- |
| `AGENTS.md` | Cross-tool baseline: orientation, commands, red/green lines, routes |
| Skills | Full workflows |
| `.cursor/rules` | Cursor-specific; can scope by glob |
| `docs/` | Depth; `AGENTS.md` links only |


Maintain each fact in one place.

**Precedence:** user's current explicit instruction > `AGENTS.md` > agent defaults.

Match `AGENTS.md` language to repo maintainer habit; English is not required.

## Nesting and size

**Nested `AGENTS.md`** = an `AGENTS.md` in a subdirectory (e.g. `packages/api/AGENTS.md`). When working under that path, tools load the **nearest** file, merge with ancestors, **child wins**.

| File | Content |
| ---- | ------- |
| Root `AGENTS.md` | Repo-wide conventions |
| Nested `AGENTS.md` | Subtree-only: commands, key dirs, red/green lines |

Nested files are **incremental only** — never duplicate the parent.

**Prefer nesting when:** conventions apply to one package/app/module only; the root file is getting long or sprouting subtree-only sections; monorepo layout. **Move** subtree content into that directory's `AGENTS.md`; keep only repo-wide material at the root.

**On create:** decide repo-wide vs subtree-first; if subtree-specific → create nested file there, not in root.

- No fixed section template; omit empty sections
- "Short" means **signal density**, not a line-count KPI; if it grows long, nest or move content out
- When editing, revise existing paragraphs before adding new top-level headings

## Workflow

**New / init:** gather repo facts (package.json, CI, layout, code habits) → align in conversation (use a brainstorming-style plugin if available) → then write; no one-shot dump

**Placement (before writing):**

```
Where does this fact belong?
  → Skill workflow?     one-line route; never paste the Skill body
  → docs / Rule?        link there
  → Subtree-only?       nested AGENTS.md
  → Repo-wide, non-obvious? root AGENTS.md
```

**Writing:** mirror the repo; no guessing, no copying from other repos.

**Trailing `<important-notes>` (new file or major rewrite):** end the body with this template to raise attention:

```markdown
<important-notes>
This file is `AGENTS.md`. Reload `using-agentsmd` when project facts change.
</important-notes>
```

- Replace `AGENTS.md` with the **path of the file you are writing** (nested: `packages/api/AGENTS.md`; same for `CLAUDE.md`)
- Do not substitute the skill name for the file path
- Wording inside the block may follow repo language; **keep the tag name `important-notes`**
- Minor edits with an accurate block: leave it; major rewrite missing the block: add it

More footer variants in `references/good-examples.md`.

**Audit:** treat the file as an **editor**, not an expander — remove duplicate / stale / low-signal text first → relocate misplaced content (subtree sections → nested `AGENTS.md`) → add last. Delete means delete; no "removed X because…". Not a running log.

**Following existing `AGENTS.md` (other tasks):** comply directly; do not re-read for validation.

**Config changes:** you may verbally remind to sync `AGENTS.md`; load this skill before actually editing the file.

## Examples

Read when you need contrast:

- `references/good-examples.md`
- `references/bad-examples.md`
