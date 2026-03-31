# User config: `plugins` in OpenClaw config

## Source of truth

- Type: `PluginsConfig` in `src/config/types.plugins.ts`
- Zod shape: `plugins` object in `src/config/zod-schema.ts`
- Normalization / enable logic: `src/plugins/config-state.ts` (`normalizePluginsConfig`, `resolveEnableState`, `resolveEffectiveEnableState`, `resolveMemorySlotDecision`)
- Exclusive slots helper: `src/plugins/slots.ts` (`applyExclusiveSlotSelection`, `defaultSlotIdForKey`)

## Top-level `plugins` fields

| Field | Role |
| --- | --- |
| `enabled?` | Master switch; `false` disables all plugin loading (`normalizePluginsConfig` treats missing as enabled). |
| `allow?` | Allowlist of plugin ids. If non-empty, only listed plugins (plus special cases below) load when other rules pass. |
| `deny?` | Denylist — blocked plugins never load. |
| `load.paths?` | Extra directories to discover/load (see `resolvePluginCacheInputs` / discovery). |
| `slots.memory?` | Which plugin id owns the **memory** slot; use **`"none"`** to disable memory plugins for that slot (normalized to internal `null` in `normalizeSlotValue` / `config-state.ts`). |
| `slots.contextEngine?` | Which plugin id owns the **context-engine** selection (see also context engine resolution in agent runtime). |
| `entries?` | Per-plugin settings: `enabled`, `hooks`, `subagent`, `config`. |
| `installs?` | Records for installed plugins (marketplace/local metadata). |

## Per-plugin entry (`plugins.entries.<pluginId>`)

From `PluginEntrySchema` in `src/config/zod-schema.ts`:

- `enabled?: boolean`
- `hooks?: { allowPromptInjection?: boolean }` — controls prompt mutation via `before_prompt_build` and legacy `before_agent_start` prompt fields (see type comment in `types.plugins.ts`).
- `subagent?: { allowModelOverride?: boolean; allowedModels?: string[] }` — gate subagent provider/model overrides; `allowedModels` can use `"*"` for any (see type comments).
- `config?: Record<string, unknown>` — plugin-specific config (validated against manifest/runtime schema).

## Enable resolution (high level)

`resolveEnableState` (`config-state.ts`):

- Global off → disabled.
- Denylist → disabled.
- `entries[id].enabled === false` → disabled.
- **Workspace** plugins: disabled by default unless in `allow` or explicitly `entries[id].enabled === true`.
- **Allowlist** non-empty: must be explicitly allowed unless memory slot selects this id or entry forces enable.
- **Bundled** plugins: default **off** unless `enabledByDefault` from manifest **or** other enable paths apply.
- **Non-bundled** origins: generally enabled when not blocked (subject to allowlist rules).

`resolveEffectiveEnableState` adds an exception: bundled plugins that would be off for “disabled by default” can still enable when the **corresponding channel** is enabled in `channels.<id>.enabled` (`isBundledChannelEnabledByChannelConfig`).

## Memory slot (`resolveMemorySlotDecision`)

For plugins with `kind === "memory"`:

- If `slots.memory === null` → memory plugins disabled for that policy.
- If `slots.memory` is a string → only the matching plugin id is selected; others disabled with reason citing the slot owner.
- Competing selection uses `selectedId` to detect “slot already filled”.

Default memory slot id when unspecified: **`memory-core`** (`defaultSlotIdForKey("memory")` in `slots.ts`).

## Exclusive slot selection (`applyExclusiveSlotSelection`)

When a user picks a plugin for an exclusive `PluginKind` (`memory` or `context-engine`), core can:

- Set `plugins.slots.<slotKey>` to the chosen id.
- **Disable** other plugins of the **same kind** in `entries` (unless already `enabled: false`) to avoid two owners.

## Plugin id normalization

`normalizePluginId` applies bundled legacy and provider aliases (`BUNDLED_LEGACY_PLUGIN_ID_ALIASES`, `BUNDLED_PROVIDER_PLUGIN_ID_ALIASES` in `src/plugins/bundled-capability-metadata.ts`) so config keys and manifest ids stay consistent.

## Tests / Vitest

`applyTestPluginDefaults` (`config-state.ts`): under `VITEST`, if the suite did not set explicit plugin config, plugins may default to **disabled** and memory slot to **`none`** to keep tests hermetic. Plugin authors running tests should mirror patterns from `docs/plugins/sdk-testing.md` and bundled extension tests.

## Related skill chapters

- `references/13-memory-and-context-engine.md` — runtime registration constraints (must match `kind`)
- `references/01-lifecycle-discovery-and-load.md` — `load.paths` and discovery
- `references/02-manifest-openclaw-plugin-json.md` — `enabledByDefault`
- `references/04-openclaw-plugin-api.md` — diagnostics and registration modes
