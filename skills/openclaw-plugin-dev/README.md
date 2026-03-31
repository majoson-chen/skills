# openclaw-plugin-dev

An [Agent Skills](https://agentskills.io/specification) package: an English, book-style reference for **OpenClaw native plugin development** (anchored to `openclaw@2026.3.28`).

## Layout

- `SKILL.md` — router + instructions (load `references/` on demand)
- `references/` — numbered chapters (`00`–`19`)

## Validate

```bash
npx skills-ref validate .
```

## Install (skills ecosystem)

After this repository is public on GitHub, install with the [skills CLI](https://github.com/vercel-labs/skills) (see [skills.sh](https://skills.sh/docs)):

```bash
npx skills add majoson-chen/openclaw-plugin-dev
```

## License

Content license is not set in this repository; add a `LICENSE` file when you publish if you want one.
