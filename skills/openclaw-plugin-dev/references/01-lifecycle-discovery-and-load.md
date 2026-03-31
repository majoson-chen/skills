# Lifecycle: discovery, roots, environment, load

## Source anchors

- `src/plugins/discovery.ts` — finds plugin candidates on disk.
- `src/plugins/roots.ts` — resolves default search roots.
- `src/plugins/bundled-dir.ts` — locates **bundled** (stock) extensions inside the OpenClaw package.
- `src/plugins/loader.ts` — executes plugin modules, validates config, builds `OpenClawPluginApi`.

## Plugin source roots (`resolvePluginSourceRoots`)

From `src/plugins/roots.ts`:

| Root key | Typical path | Role |
| --- | --- | --- |
| `stock` | Resolved via `resolveBundledPluginsDir` | Built-in extensions shipped with OpenClaw |
| `global` | `<configDir>/extensions` | User-wide plugins (`resolveConfigDir`) |
| `workspace` | `<workspace>/.openclaw/extensions` | Per-workspace plugins |

Extra directories may be appended via config **load paths** (see `resolvePluginCacheInputs`); order matters for precedence.

## Bundled directory resolution

`resolveBundledPluginsDir` (`src/plugins/bundled-dir.ts`):

- Override with env **`OPENCLAW_BUNDLED_PLUGINS_DIR`** (must exist; stale overrides may fall back to argv-based package root for packaged installs).
- Source checkout: prefers `extensions/` at package root when `.git` + `src` + `extensions` exist.
- Otherwise prefers `dist-runtime/extensions` when paired `dist/` exists, else `dist/extensions`.

Tests often set **`VITEST`** so source `extensions/` are preferred.

## Discovery cache

`src/plugins/discovery.ts`:

- Short-lived cache (default **1000 ms**) to collapse bursty reloads.
- **`OPENCLAW_DISABLE_PLUGIN_DISCOVERY_CACHE`** — disable caching.
- **`OPENCLAW_PLUGIN_DISCOVERY_CACHE_MS`** — override TTL; `0` disables.

## Candidate shape (high level)

Each discovered candidate carries:

- `rootDir`, `source` (entry path), optional `setupSource`
- `origin`: `bundled` | `global` | `workspace` | `config`
- `format`: `openclaw` (native) or `bundle` (external bundle layout)
- Manifest data from `openclaw.plugin.json` when present

## Loader modes

`PluginLoadOptions.mode` (`src/plugins/loader.ts`):

- **`full`** — normal runtime registration.
- **`validate`** — validation-oriented load (lighter path; used when checking configs).

Other related flags:

- **`onlyPluginIds`** — restrict to a subset.
- **`includeSetupOnlyChannelPlugins`** — include channel plugins that only need setup surfaces pre-listen.
- **`preferSetupRuntimeForChannelPlugins`** — prefer `setupEntry` for configured channels that opt in via package metadata.
- **`activate`** — whether to run `activate` after `register`.
- **`throwOnLoadError`** — fail fast vs collecting diagnostics in the registry.

## Registration modes (`OpenClawPluginApi.registrationMode`)

Type: `PluginRegistrationMode` in `src/plugins/types.ts`:

- `full`
- `setup-only`
- `setup-runtime`
- `cli-metadata`

The loader may expose **no-op** register functions when a plugin is loaded only for metadata or setup phases; plugins should not assume every register call mutates global registries in all modes.

## Security and boundaries

- Manifest and config files are read with **boundary-aware** file open helpers (see `openBoundaryFileSync` usage in manifest/discovery code) to reduce unsafe path surprises.
- Plugins are loaded with **jiti** and SDK alias resolution (`src/plugins/sdk-alias.ts`) so imports like `openclaw/plugin-sdk/...` resolve consistently.

## Practical debugging

1. Confirm which root supplied the plugin: bundled vs global vs workspace.
2. Check `OPENCLAW_BUNDLED_PLUGINS_DIR` if bundled plugins fail only on one machine.
3. Use `openclaw plugins inspect <id>` (documented in OpenClaw CLI docs) for shape and capability breakdown after load.
