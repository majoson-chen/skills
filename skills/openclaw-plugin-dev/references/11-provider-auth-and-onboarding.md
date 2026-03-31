# Provider auth and onboarding

## Goals

OpenClaw separates:

1. **Cheap static metadata** (env vars, CLI flags, choice ids) in `openclaw.plugin.json`
2. **Interactive flows** implemented as `ProviderAuthMethod` objects inside provider plugins

This lets the CLI and wizards show auth options **before** heavy provider code loads.

## Manifest fields

- **`providerAuthEnvVars`:** `Record<providerId, string[]>` — env keys associated with a provider
- **`providerAuthChoices`:** array of structured choices (`PluginManifestProviderAuthChoice` in `src/plugins/manifest.ts`)

Each choice links:

- `provider` + `method` (auth method id inside the plugin) + stable `choiceId`

Optional CLI metadata: `cliFlag`, `cliOption`, `cliDescription`, `optionKey`.

Optional **`onboardingScopes`:** `text-inference` | `image-generation` (defaults documented in `manifest.ts`).

## Runtime types (`src/plugins/types.ts`)

Key types:

- `ProviderAuthKind` — `"oauth" | "api_key" | "token" | "device_code" | "custom"`
- `ProviderAuthContext` — includes `config`, `prompter`, `runtime`, optional `opts` (CLI preseed flags), `secretInputMode`, etc.
- `ProviderAuthResult` — returns `profiles` (credentials), optional **`configPatch`** to merge into config after auth, `defaultModel`, `notes`

Non-interactive flows use `ProviderAuthMethodNonInteractiveContext` where applicable.

## SDK helpers

Subpath **`openclaw/plugin-sdk/provider-auth`** (`docs/plugins/sdk-overview.md`):

- Patterns like `createProviderApiKeyAuthMethod`, `ensureApiKeyFromOptionEnvOrPrompt`, `upsertAuthProfile`

Also see **`plugin-sdk/provider-onboard`** for config patch helpers.

## Practice

1. Declare **all** first-class auth entry points in `providerAuthChoices`.
2. Keep **env var names** in `providerAuthEnvVars` synchronized with what `doctor` / docs expect.
3. Use `configPatch` in `ProviderAuthResult` for provider-owned defaults (model entries, aliases) — see type comments in `types.ts`.
