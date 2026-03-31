# Plugin formats: native vs compatible bundles

## Types (`src/plugins/types.ts`)

- `PluginFormat = "openclaw" | "bundle"`
- `PluginBundleFormat = "codex" | "claude" | "cursor"`

## Native OpenClaw plugins

- `openclaw.plugin.json` manifest (strict schema expectations)
- `package.json` → `openclaw.extensions` entry files
- `definePluginEntry` / `defineChannelPluginEntry` registration

## Compatible bundles

Documented in `docs/plugins/bundles.md` and `docs/plugins/building-extensions.md` (in OpenClaw repo).

Typical manifest files:

- Codex: `.codex-plugin/plugin.json`
- Claude: `.claude-plugin/plugin.json` (or default component layout)
- Cursor: `.cursor-plugin/plugin.json`

OpenClaw **auto-detects** these layouts. They are **not** validated identically to `openclaw.plugin.json`.

Bundled plugins may expose:

- Skill roots
- Claude command roots
- Claude `settings.json` defaults
- Supported hook packs (when layout matches runtime expectations)

## MCP bundle runtime

Loader consults bundle MCP support (`inspectBundleMcpRuntimeSupport` from `src/plugins/bundle-mcp.ts` in `loader.ts` imports) — relevant when bridging MCP-enabled bundles.

## When to choose which

- **New first-party integrations** targeting OpenClaw features: prefer **native** plugins for full `OpenClawPluginApi` access.
- **Cross-tool portability**: bundle layouts may trade away some native-only surfaces.

## Skills inside plugins

Manifest field `skills?: string[]` can declare packaged skills. Cross-reference with bundle docs for how skills are discovered from bundle layouts.
