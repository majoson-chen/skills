# Text inference providers and CLI backends

## Registration

- **`api.registerProvider(provider)`** — primary text/model provider plugin type (`ProviderPlugin` and related types in `src/plugins/types.ts`; large surface area for catalog, streaming, auth, model normalization).
- **`api.registerCliBackend(backend)`** — pairs with CLI inference runner for text-only backends.

## SDK support subpaths (official)

From `docs/plugins/sdk-overview.md`:

- `plugin-sdk/cli-backend` — defaults + watchdog constants
- `plugin-sdk/provider-auth` — API key helpers, profile upsert
- `plugin-sdk/provider-model-shared` — `normalizeModelCompat`
- `plugin-sdk/provider-catalog-shared` — catalog templates
- `plugin-sdk/provider-usage` — usage snapshots
- `plugin-sdk/provider-stream` — stream wrapper types
- `plugin-sdk/provider-onboard` — onboarding config patch helpers
- `plugin-sdk/global-singleton` — caches / singleton helpers

## Manifest expectations

- `providers: string[]` — ids this plugin owns
- `cliBackends?: string[]` — backend ids for quick inference
- `providerAuthEnvVars` + `providerAuthChoices` for onboarding without executing the plugin
- Optional `contracts` if you want static listing of owned provider ids for tooling

## `CliBackendPlugin` extras (`src/plugins/types.ts`)

Beyond `id` and `config`:

- **`bundleMcp?: boolean`** — opt-in: OpenClaw may inject **bundle MCP config** for this backend. Only enable if the backend actually consumes an MCP config file.
- **`normalizeConfig?: (config) => config`** — optional merge-time normalizer for backward-compatible config rewrites.

## Conceptual responsibilities

Typical provider plugins implement hooks/callbacks for:

- **Discovery** — which models exist
- **Catalog** — templates, aliases, defaults
- **Auth** — resolve API keys, OAuth, device codes (`ProviderAuthMethod` types)
- **Transport** — HTTP/WebSocket/etc. to vendor
- **Streaming** — map vendor streams to OpenClaw/pi-ai stream expectations
- **Thinking / reasoning** policies where applicable

Exact method names and contexts are defined on `ProviderPlugin` in `src/plugins/types.ts` — when implementing, **read that type** in the pinned version rather than relying on prose here.

## Learning path

1. `docs/plugins/sdk-provider-plugins.md`
2. A focused bundled provider under `extensions/` (e.g. `mistral`, `openrouter`) for minimal vs hybrid patterns
3. Type-driven reading: `ProviderPlugin` and `ProviderAuth*` types

**Auth and onboarding detail** is a large enough topic to have its own chapter — see `references/11-provider-auth-and-onboarding.md` for `ProviderAuthMethod`, `providerAuthChoices`, env var hints, and `ProviderAuthResult.configPatch`.
