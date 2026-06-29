# Task package

How to compose a **self-contained task package** for a worker dispatch — principles, skeleton, and per-scenario emphasis.

## Self-contained

Workers **do not inherit** the main session: no chat history, no files you already read, no skills you loaded, no other workers' output. Put everything the worker needs in **this dispatch**. Do not tell workers to read plan files or "see above."

Default model: **inherit** the controller's model unless the user specified or confirmed otherwise (see SKILL.md → Model policy).

## Skeleton

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
<see references/handoff.md; adapt as needed>
```

## Scenario emphasis

Extra points when writing the package — not a second template.

**Recon / investigation**

- Read-only; no edits or commits unless the user allows
- Acceptance = find X, compare A vs B — not "read as much as possible"
- Cite paths and line numbers, not pasted source walls

**Single deliverable**

- ≤3 tools and enough context already → controller should not have dispatched; if you are here, scope is real
- State deliverable and runnable acceptance (tests, lint)
- Full plan excerpt in the package

**Parallel**

- Package covers **one domain only**; do not assume other workers' context
- Scope constraints: do not modify paths outside this domain

**Serial gates**

- **Implement gate:** standard execution package
- **Spec-fit gate:** paste original requirements + changed paths; read-only; verify against code
- **Quality gate:** after spec fit; maintainability, style, tests
