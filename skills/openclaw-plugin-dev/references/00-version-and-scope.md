# Version anchor and scope

## Frozen target

- **Primary anchor:** `openclaw@2026.3.28` (npm release or matching git tag).
- This skill is a **snapshot reference**. It is not maintained against future OpenClaw releases.

## What “plugin system” means here

In scope:

- Native OpenClaw plugins: `openclaw.plugin.json` + package `openclaw` metadata + entry module registering via `OpenClawPluginApi`.
- Discovery roots, loader behavior, registration modes, hooks, capabilities (channel, text provider, CLI backend, speech, media understanding, image generation, web search), tools, commands, gateway/CLI extensions, memory/context-engine slots.
- Compatible **bundle** layouts (Codex / Claude / Cursor) at the level OpenClaw documents — see `references/14-bundles-and-formats.md`.

Out of scope:

- Teaching TypeScript, Node, or general frontend patterns.
- Non-plugin OpenClaw internals (full gateway protocol, unrelated CLI commands) except where they touch plugin APIs.

## Evidence chain (in order of authority)

1. **Runtime types** in the pinned OpenClaw version: `src/plugins/types.ts` (`OpenClawPluginApi`, hooks, provider/channel types).
2. **Manifest normalization**: `src/plugins/manifest.ts` (`PluginManifest`).
3. **Load pipeline**: `src/plugins/loader.ts`, `src/plugins/discovery.ts`, `src/plugins/roots.ts`, `src/plugins/bundled-dir.ts`.
4. **Official English docs** shipped with OpenClaw: `docs/plugins/*.md` (overview, building, manifest, architecture, SDK guides).
5. **Bundled examples:** `extensions/*` in the OpenClaw repo (each is a real plugin package).

When docs and code disagree, **trust the pinned source tree** and file an upstream doc issue separately.

## Suggested upgrade workflow

When moving beyond `2026.3.28`:

1. Diff `src/plugins/` and `src/plugin-sdk/` between tags.
2. Re-read `docs/plugins/sdk-migration.md` if present in the newer tree.
3. Run `openclaw plugins inspect <id>` and `openclaw doctor` against a test workspace.
