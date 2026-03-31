# Native manifest: `openclaw.plugin.json`

## Source of truth

- Filename: **`openclaw.plugin.json`** at the **plugin root** (`PLUGIN_MANIFEST_FILENAME` in `src/plugins/manifest.ts`).
- Type: `PluginManifest` in the same file.

## Fields (`PluginManifest`)

| Field | Purpose |
| --- | --- |
| `id` | Stable plugin id (required for identity) |
| `configSchema` | JSON-schema-shaped object for **plugin config** validation before runtime |
| `enabledByDefault` | Default enablement when not overridden by user config |
| `legacyPluginIds` | Old ids that normalize to this plugin |
| `autoEnableWhenConfiguredProviders` | Provider ids that imply enabling this plugin when present in config |
| `kind` | Optional: `memory` \| `context-engine` (see `PluginKind` in `src/plugins/types.ts`) |
| `channels` | Channel ids owned (static listing for discovery / docs) |
| `providers` | Provider ids owned |
| `cliBackends` | CLI inference backend ids for cheap startup lookup |
| `providerAuthEnvVars` | Map of provider id → env var names (auth without loading plugin JS) |
| `providerAuthChoices` | Structured auth-choice metadata for onboarding / CLI (see below) |
| `skills` | Skill roots or ids (when plugin ships agent skills) |
| `name`, `description`, `version` | Human metadata |
| `uiHints` | Per-field `PluginConfigUiHint` map (`src/plugins/types.ts`): e.g. `label`, `help`, `tags`, `advanced`, `sensitive`, `placeholder`. Keys may use **dotted paths** (e.g. `webSearch.apiKey`) to match nested config properties. |
| `contracts` | Static capability ownership snapshot (`PluginManifestContracts`) |
| `channelConfigs` | Per-channel JSON schema fragments + UI hints |

## `contracts` (`PluginManifestContracts`)

Optional static snapshot used for manifest-driven discovery and contract coverage **without** importing plugin code:

- `speechProviders?: string[]`
- `mediaUnderstandingProviders?: string[]`
- `imageGenerationProviders?: string[]`
- `webSearchProviders?: string[]`
- `tools?: string[]`

Keep this aligned with what `register*` calls actually register.

## `providerAuthChoices` (`PluginManifestProviderAuthChoice`)

Each entry wires a **provider** + **auth method** + stable **`choiceId`** for onboarding:

- `provider`, `method`, `choiceId` (required strings)
- `choiceLabel`, `choiceHint`
- `deprecatedChoiceIds` — migration from old choice ids
- `groupId`, `groupLabel`, `groupHint` — picker grouping
- `optionKey`, `cliFlag`, `cliOption`, `cliDescription` — CLI flag metadata
- `onboardingScopes` — subset of `text-inference` | `image-generation` (defaults described in type comments in `manifest.ts`)

## `channelConfigs`

Per channel id:

- `schema: Record<string, unknown>` — JSON-schema-like object (required)
- `uiHints`, `label`, `description`, `preferOver` (optional)

## Config schema contract (runtime)

Plugins may also supply a **runtime** config schema via `definePluginEntry({ configSchema })` using `OpenClawPluginConfigSchema` (`src/plugins/types.ts`):

- Zod-like `safeParse` / `parse`, or `validate`, optional `uiHints`, optional `jsonSchema`.

The manifest `configSchema` is what core uses **without executing** the plugin; keep manifest and runtime validation consistent to avoid user confusion.

## Common mistakes

- Missing or invalid manifest → plugin treated as **error**; can block config validation (see OpenClaw `docs/plugins/manifest.md`).
- Drift between `contracts` / `providers` / `channels` lists and actual registrations → confusing `inspect` output and onboarding.
- Putting **entrypoint paths** in the manifest — those belong in `package.json` → `openclaw.extensions` (see `references/03-package-json-openclaw-field.md`).
