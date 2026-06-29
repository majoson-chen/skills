---
name: using-subagent
description: >-
  Load whenever subagents are available in this environment — keep loaded for the
  session. Also load when the user mentions subagents, workers, dispatch, or
  parallel agents. Also load when work should run on a subagent instead of in
  this session.
---

# using-subagent

Meta-skill for the **controller** (this session): decide **whether** and **which scenario** to dispatch. Workers run in isolated context; you coordinate and integrate handoffs.

## Reading references

**Do not read** `references/` **on skill load.** When you are **about to dispatch**, read both files below, compose the task package, state handoff expectations, then dispatch.

| File | Contents |
| --- | --- |
| `references/task-package.md` | Self-contained package, skeleton, per-scenario emphasis |
| `references/handoff.md` | Handoff template and status meanings |

If a **workflow plugin** is active, its task format and handoff rules **override** these references.

## Lightweight vs heavyweight

| | Lightweight | Heavyweight |
| --- | --- | --- |
| **Signals** | ≤3 tool calls; short outputs; clear boundary; one-shot deliverable | Many tool calls; wide search or deep investigation; large read context; orthogonal to the main goal |
| **Action** | Do it in this session | **Dispatch before you start**; stay focused on orchestration |

Heavyweight includes **research** and **execution** (not read-only). If doing the work here would bloat context or dilute focus, treat it as heavyweight.

## Dispatch decisions

**Rules**

- Lightweight → do it here; no dispatch
- Heavyweight → dispatch **before** you begin; if already mid-task here, **do not interrupt** — finish the turn, dispatch next time
- User asked for subagents → dispatch; do not substitute serial main-session work
- Matching workflow plugin → load that plugin; its handoff and task format override this skill's references
- Otherwise pick a scenario below, then follow **Reading references**

| Scenario | Prefer workflow plugin |
| --- | --- |
| Lightweight | — (no dispatch) |
| Read-heavy recon / investigation | Offload-style |
| Single deliverable (incl. multi-file implementation, clear scope) | — |
| Multiple independent domains; no shared writes; no ordering dependency | Parallel dispatch |
| Dependent gate chain or multi-task plan | Plan-driven development |
| Skill evals, A/B harnesses, domain-specific | Matching domain plugin |

### Scenario notes

**Recon** — read-only by default; acceptance = answerable outcomes; narrow and deep.

**Single deliverable** — paste plan excerpt into the package; worker does not read plan files; acceptance = runnable checks.

**Parallel** — one package per domain; dispatch **all workers in the same turn**; reconcile conflicts after handoff.

**Serial gates** — fresh worker per gate; gate 2 reads code, does not trust implementer report; gates within one task stay serial.

### Examples

| Situation | Dispatch? | Scenario |
| --- | --- | --- |
| "Fix this typo" | No | Lightweight |
| "`pnpm test` failed" and you expect one or two file reads | No | Lightweight |
| "Use a subagent to map monorepo package dependencies" | Yes | Recon |
| Writing a PR while tracing an API across 20 files | Yes | Recon (orthogonal) |
| Three test files failing for unrelated reasons | Yes | Parallel |
| Multi-task plan with implement → review per task | Yes | Serial gates (or plan-driven plugin) |
| User says "use subagent" | Yes | Pick from scenario table |
| Half the session spent searching; only now considering a worker | Not this turn | Dispatch before starting next time |
| "Map the whole repo architecture" while the goal is a bugfix | Depends | Offload recon, or stay on the bug |

## Model policy

- Default: worker **inherits** this session's model
- User specifies a model → use it
- Do not upgrade or downgrade worker model without user confirmation — model switches cost real money and may reduce output quality

## After handoff

1. Fold results into the current goal; report without dumping the worker's full reply
2. `NEEDS_CONTEXT` / `BLOCKED` → add context and re-dispatch, or escalate; `DONE_WITH_CONCERNS` → resolve open items before moving on
