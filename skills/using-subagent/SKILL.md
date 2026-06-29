---
name: using-subagent
description: Load whenever subagents are available and the task may involve delegation. Required when the user mentions subagent, worker, dispatch, parallel agents, or asks you to "spin up an agent"; when following a workflow plugin that depends on subagents; or when the user asked for subagents but you are still doing the work in the main session. Also load when you foresee a heavyweight task (dispatch before you start, not after context is bloated), multiple independent problem domains, or an implementation plan with several loosely coupled tasks. Do not load for lightweight one-step work with no delegation intent. Covers lightweight vs heavyweight, dispatch routing, self-contained task packages, model inheritance, and handoff.
---

# using-subagent

Meta-skill for the **controller** (this session): decide **whether**, **why**, and **how** to dispatch **workers** (subagents). Workers run in isolated context; you coordinate and integrate handoffs.

## Lightweight vs heavyweight

| | Lightweight | Heavyweight |
| --- | --- | --- |
| **Signals** | ≤3 tool calls; short outputs; clear boundary; one-shot deliverable | Many tool calls; wide search or deep investigation; large read context; orthogonal to the main goal |
| **Examples** | Fix a typo; run one command; answer a pointed question | Repo-wide recon; doc/API research; multi-file code changes; feature work; batch refactors; test fixes |
| **Action** | Do it in the controller session | **Dispatch before you start**; stay focused on orchestration |

Heavyweight covers **research** and **execution** (not read-only). If doing the work in the main session would bloat context or dilute focus, treat it as heavyweight.

## Terms

Host products name subagents differently; the roles are the same:

| Term | Role |
| --- | --- |
| **Controller** | This session — decide, integrate, reply to the user |
| **Worker** | Subagent with a self-contained task package |
| **Dispatch** | Start a worker in the host environment |
| **Handoff** | Worker's structured reply back to the controller |

Use whatever dispatch mechanism the host provides; do not hard-code tool names in this skill.

## Why delegate

| Goal | When | Typical move |
| --- | --- | --- |
| **Context length** | Multi-step work you can outsource; process detail need not stay in this session | Worker executes; controller takes handoff only |
| **Focus** | Heavyweight work is orthogonal to the current goal | Offload **before** you start digging |
| **Parallelism** | 2+ independent domains, no write conflicts | Dispatch multiple workers in one turn |
| **Model choice** | User asked for a specific model, or confirmed a switch | Set worker model only then |

## Dispatch decisions

**Rules**

- Lightweight → controller does it; no dispatch
- Heavyweight → dispatch **before** you begin; if already mid-task in the main session, **do not interrupt** — finish the turn, dispatch next time
- User asked for subagents → dispatch; do not substitute serial main-session work
- Task packages must be self-contained (see **Task packages**); controller schedules and integrates handoffs only
- Matching workflow plugin exists → load that plugin; otherwise dispatch using **Scenario notes** below

| Scenario | Prefer workflow plugin (if installed) |
| --- | --- |
| Lightweight | — (no dispatch) |
| Heavyweight, read-heavy recon / investigation | Offload-style |
| Single deliverable (including multi-file implementation, clear scope) | — |
| Multiple independent domains; no shared writes; no ordering dependency | Parallel dispatch |
| Dependent gate chain or multi-task plan | Plan-driven development |
| Skill evals, A/B harnesses, domain-specific flows | Matching domain plugin (out of scope here) |

### Examples

| Situation | Dispatch? | Scenario |
| --- | --- | --- |
| "Fix this typo" | No | Lightweight |
| "`pnpm test` failed" and you expect one or two file reads | No | Lightweight |
| "Use a subagent to map monorepo package dependencies" | Yes | Recon |
| Writing a PR while also tracing an API across 20 files | Yes | Recon (orthogonal) |
| Three test files failing for unrelated reasons | Yes | Parallel |
| Multi-task plan with implement → review per task | Yes | Serial gates (or plan-driven plugin) |
| User says "use subagent" | Yes | Pick scenario from table |
| Half the session spent searching; only now considering a worker | Not this turn | Dispatch before starting next time |
| "Map the whole repo architecture" while the goal is a bugfix | Depends | Offload architecture recon, or stay on the bug |

### Scenario notes

Write task packages from **Task packages** below. These bullets are **extra emphasis per scenario**, not a second template.

**Recon / investigation (read-heavy)**

- Read-only by default; no edits or commits unless the user allows
- Acceptance = answerable outcomes (find X, compare A vs B), not "read as much as possible"
- Go narrow and deep; cite paths and line numbers, not pasted source walls

**Single deliverable**

- ≤3 tools, enough context already, no delegation intent → controller does it
- State the deliverable; acceptance = runnable checks (tests, lint)
- Paste the full plan excerpt into the package — worker does not read plan files

**Parallel**

- Domains independent; no writes to the same files; not implement+review on one task — else serial or one worker
- One self-contained package per domain; dispatch **all workers in the same turn**
- After all handoffs: check for conflicting edits or contradictory conclusions

**Serial gates (implement → spec fit → quality)**

- Fresh worker per gate; next gate only after the previous passes; on failure, fix implement then re-run from the failed gate
- Gate 2 (spec fit): paste gate-1 requirements + changed paths; **read the code**, do not trust the implementer's report
- Gate 3 (quality): after gate 2; maintainability, style, test quality
- Multi-task plans: gate 1 per task can run in parallel across tasks; **gates within one task stay serial**

## Task packages

### Self-contained

Workers **do not inherit** the main session: no chat history, no files you already read, no skills you loaded, no other workers' output. Put everything the worker needs in **this dispatch**. Do not tell workers to read plan files or "see above."

### Skeleton

Pick sections as needed; phrase them naturally:

```markdown
## Role and task
<one line>

## Requirements
<full text; for execution work, state the deliverable>

## Context
<paths, errors, decisions, relation to the parent goal>

## Before you start
Ask the controller if scope, requirements, or acceptance are unclear.

## Scope and constraints
<boundaries; read-only vs edits/commits allowed>

## Acceptance
<tests, lint, checks — for execution work>

## Working directory
<path>

## If stuck
Use handoff Status to escalate; do not guess.

## Handoff
<see Handoff template below>
```

Default model: **inherit** the controller's model. Change model only when the user specifies or confirms.

## Model policy

| Case | Behavior |
| --- | --- |
| Default | Worker **inherits** controller model |
| User specifies a model | Dispatch with that model |
| Forbidden | Upgrading or downgrading worker model without user confirmation |

Model switches cost real money.

## Handoff

Workers usually **do not load** this skill. State handoff expectations at the end of the task package; adapt the template below as needed.

When you are also following a **workflow plugin** or **another skill** that defines handoff format, status values, or review gates, **use that source as authoritative** — this section is a fallback reference only.

### Template

```
### Reply to controller (handoff)

Status: <DONE | DONE_WITH_CONCERNS | NEEDS_CONTEXT | BLOCKED>

Summary:
<lead with conclusions; ~3–8 sentences>

Artifacts:
<changed paths; evidence or command output pointers; for read-only work, paths and line numbers>

Open questions:
<items for controller or user; omit if none>

Keep it concise — no full tool logs or turn-by-turn transcripts.
```

### Status meanings

| Status | Meaning |
| --- | --- |
| `DONE` | Ready to integrate |
| `DONE_WITH_CONCERNS` | Done but with reservations or scope edge cases |
| `NEEDS_CONTEXT` | Missing info; re-dispatch after controller fills gaps |
| `BLOCKED` | Cannot proceed; escalate to user or revise the plan |

### After handoff

1. Fold results into the current goal; report to the user without dumping the worker's full reply
2. `NEEDS_CONTEXT` / `BLOCKED` → add context and re-dispatch, or escalate; `DONE_WITH_CONCERNS` → resolve open items before moving on
