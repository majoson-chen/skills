# Handoff

Fallback **handoff format and status values** for worker replies to the controller. State these expectations at the end of the task package.

Workers usually **do not load** `using-subagent`.

When a **workflow plugin** or **another skill** defines handoff format, status values, or review gates, **use that source as authoritative** — this file is a fallback only.

## Template

Adapt wording as needed:

```
### Reply to controller (handoff)

Status: <DONE | DONE_WITH_CONCERNS | NEEDS_CONTEXT | BLOCKED>

Summary:
<lead with conclusions; ~3–8 sentences>

Artifacts:
<changed paths; evidence or command pointers; read-only: paths and line numbers>

Open questions:
<items for controller or user; omit if none>

Keep it concise — no full tool logs or turn-by-turn transcripts.
```

## Status meanings

| Status | Meaning |
| --- | --- |
| `DONE` | Ready to integrate |
| `DONE_WITH_CONCERNS` | Done but with reservations or scope edge cases |
| `NEEDS_CONTEXT` | Missing info; re-dispatch after controller fills gaps |
| `BLOCKED` | Cannot proceed; escalate to user or revise the plan |
