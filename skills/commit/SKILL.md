---
name: commit
description: Guides the agent through a safe, structured git commit workflow — gathering diff and history, checking index state, running a risk gate for sensitive or unrelated files, planning multi-commit splits when the changeset is large or mixed, reading changed code before writing commit bodies, and composing well-structured messages that follow the repo's conventions. Use when the user types /commit, asks for help committing staged or unstaged changes, wants a commit message drafted, or needs to split a large changeset into atomic commits.
---

# commit

Triggered by `/commit`. The user may provide no additional context, or a single sentence of intent — treat it as a hint, not a substitute for analysis.

Execute the steps below **in order**. Do not skip or reorder them.

---

## Ground Rules

1. **Gather first, decide second, act last.** Never run `git commit` before you understand what has changed and why.
2. **No ad-hoc multi-commit.** If the changes need to be split into multiple commits, produce a complete plan first (see Step 4), get explicit user approval, then execute in order. Do not make it up as you go.
3. **Never use `--no-verify` by default.** Only use it if the user explicitly asks to bypass hooks in this conversation.
4. **Match the repo's conventions.** Infer commit message style and language from the repo's history and any contribution guidelines. Fall back to **Conventional Commits in English** (e.g. `feat(scope): subject`) only when the repo gives no clear signal.

---

## Step 1 — Gather Information

Run (or equivalent):

- `git status --porcelain` — full list of changed paths: staged, unstaged, untracked
- `git diff --stat` — size and shape of the diff; use `git diff` on key files as needed
- `git log --oneline -n 10` — infer message style and language

**Inferring style:** If the recent history strongly favors a particular style (e.g. Chinese subjects, no `type:` prefix), follow it this time and briefly note why. If `CONTRIBUTING.md`, `.cursor/rules`, or similar files define commit conventions, those take priority. Default fallback: **Conventional Commits in English**.

If the user supplied a hint, note it as **intent context** — it informs grouping and message drafting, but does not bypass risk checks or planning.

---

## Step 2 — Staging Strategy

**Start by checking whether the index has any staged content, then take the corresponding path:**

### Index has staged content

- The scope of this commit is **exactly what is currently staged**. Do not `git add` anything else unless the user explicitly asks.
- Cross-reference the staged content with the full list of changed files from Step 1. If files are **clearly related to the same intent but not staged**, you **must flag them** — list the paths and briefly explain why they seem related (e.g. "this file exports the function you just added"). Do not add them yourself; wait for the user to decide.

### Index is empty

- Treat this as a fully automated commit: the user wants you to handle everything.
- Do not run `git add -A` until the risk check in Step 3 passes and, if a multi-commit split is needed, the Step 4 plan is approved.
- If any paths should clearly be excluded (sensitive files, temp files, etc.), exclude them or stop and ask — never force-add them for convenience.

---

## Step 3 — Risk Gate (hard stop)

Before any `git add` or `git commit`, scan paths and diff content for:

- Suspected **secrets, tokens, private keys, or `.env` credentials** (check both filenames and content heuristically)
- Obviously **temporary, backup, or unrelated files** (e.g. `*.log`, `dist/`, editor swap files — cross-check against `.gitignore` and project norms)
- Anything else that clearly should not be committed

If anything is flagged: **stop immediately**. List the problematic files and suggest remediation options (unstage, add to `.gitignore`, `git restore`, etc.). Wait for the user's instruction before proceeding.

---

## Step 4 — Multi-Commit Plan (large or mixed changesets)

If the changeset spans many files or clearly mixes unrelated concerns — making a single atomic commit a bad fit — do the following **before executing any commit**:

1. Produce a **complete upfront plan**:
   - How many commits, and in what order
   - For each commit: the file list, a proposed subject, and whether a body is needed (with key points if so)
2. **Wait for explicit user approval** before executing anything. Once approved, carry out the full plan sequentially within the same conversation turn.

When in doubt about whether to split: err on the side of proposing a plan. A well-structured history is worth the extra step.

---

## Step 5 — Read the Diff (required before writing any body)

**Trigger:** When Step 6 determines that a body is needed for a commit, complete this step before drafting the message. Never write a body based solely on `--stat` output, file paths, or the user's one-line hint.

**Scope must match the commit:**

- Staged content: `git diff --cached`
- Files not yet staged but about to be included: `git diff -- <paths>`
- New or untracked files (no diff available): read the file contents directly (use the Read tool or equivalent) — do not guess purpose from the filename or extension

**Depth:** Understand *what kind* of change occurred — behavior, API/contract, configuration, or structure — well enough that each body bullet maps to a concrete diff hunk. If the hunks alone are unclear, broaden your reading (callers, adjacent modules, README, config comments). If you still cannot confidently describe the change, say what information is missing and ask the user rather than padding the body with vague filler. Phrases like "update code" or "improve logic" are not acceptable.

Large diffs may be read in chunks; only write about what you have actually read.

**In a multi-commit scenario:** Before writing the body for each individual commit, re-run `git diff --cached` scoped to that commit's file group. Do not reuse a vague impression of the overall diff.

---

## Step 6 — Write the Message and Commit

### Subject

- Keep it within the conventional limit (typically ≤72 characters; match the repo's norm if stricter).
- Must accurately reflect the actual change. Base it on the diff/stat gathered in Step 1.

### Body — when to include one

**A body is required** (separated from the subject by a blank line) when any of the following apply:

- The change touches **2 or more files**, or a single file with non-obvious diff (not a typo fix or pure formatting)
- The user's hint contains information that doesn't fit in the subject
- Behavior or contract changed (root cause of a fix, new usage for a feature, config migration, etc.)
- The repo's recent history consistently includes bodies — match that pattern

**A body may be omitted** only when the change is trivially self-explanatory (single typo, pure whitespace) *and* the repo's history is predominantly subject-only.

### Body — how to write it

Complete **Step 5** first. Write in bullet points or short sentences that map to real diff hunks. Use the same language as the subject. Add a `BREAKING CHANGE:` footer when applicable.

### Committing

Use `git commit -m "<subject>" -m "<body>"` or a heredoc — whichever ensures a blank line between subject and body. Do **not** add `--no-verify`. Respect any Husky / pre-commit hooks.

---

## Step 7 — Handle Failures

If `git commit` fails:

1. **Do not** retry with `--no-verify`.
2. Summarize the cause concisely — do **not** paste the full log output. Examples:
   - "ESLint failed in pre-commit — approximately N errors"
   - "TypeScript type-check failed"
   - "Tests failed (suite name / rough failure point)"
3. **Stop** and wait for the user's next instruction (fix the code, adjust staging, or explicitly request `--no-verify`).

---

## Wrap-up

- Report the commit hash (or a list if multiple) and the final message(s).
- Note any remaining unstaged or uncommitted changes, if any.
- If you flagged related-but-unstaged files in Step 2, briefly remind the user in case they still want to handle those.
