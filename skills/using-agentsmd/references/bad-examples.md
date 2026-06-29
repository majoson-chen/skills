# Bad examples

---

## Tag: directory dump (for "discovery")

```markdown
## Project structure

src/
  components/
    Button.tsx
    Modal.tsx
  hooks/
  utils/
  … (30 more lines)
```

**Problem:** written for browsing; burns context; the agent can `ls` on its own.

---

## Tag: full Skill pasted

```markdown
## Commit workflow

1. Run git status…
(paste 80 lines from the commit skill)
```

**Problem:** should be one route line: `load skills/commit/SKILL.md`.

---

## Tag: contrast priming

```markdown
- use pnpm --filter <pkg>, don't blindly run from repo root
```

**Problem:** "blindly run from repo root" plants an extra failure mode. Write: `package-scoped tasks: pnpm --filter <pkg> <script>`.

---

## Tag: generic filler

```markdown
## Code standards

Follow best practices. Keep code clean and maintainable.
```

**Problem:** no repo-specific signal; the model already knows this.

---

## Tag: explain the deletion

```markdown
## Tests

<!-- removed npm test; now pnpm -->

- full suite: `pnpm test`
```

**Problem:** changelog noise; if it's gone, don't narrate it.

---

## Tag: empty template sections

```markdown
## Security

(no content)

## Deployment

TBD
```

**Problem:** omit sections with nothing to say.

---

## Tag: nested file copies parent

```markdown
# API package

## Git

- do not commit without user request
- commits: load skills/commit/SKILL.md

## Commands

- package tests: `pnpm test`
```

**Problem:** commit rules already live in root `AGENTS.md`; nested files hold subtree deltas only.

---

## Tag: footer names the skill

```markdown
<important-notes>
This file comes from using-agentsmd. Update when facts change.
</important-notes>
```

**Problem:** name the file path (e.g. `packages/api/AGENTS.md`), not the skill.

---

## Tag: maintenance note without `<important-notes>`

```markdown
This file is `AGENTS.md`. Reload `using-agentsmd` when project facts change.
```

**Problem:** bare text gets low priority; wrap with `<important-notes>`.
