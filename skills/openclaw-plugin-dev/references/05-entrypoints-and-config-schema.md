# Entrypoints and config schema

## `definePluginEntry`

**Module:** `openclaw/plugin-sdk/plugin-entry`  
**Source:** `src/plugin-sdk/plugin-entry.ts`

Canonical helper for **non-channel** plugins (providers, tools, hooks, commands, services, memory, context-engine).

Shape:

```typescript
definePluginEntry({
  id: string;
  name: string;
  description: string;
  kind?: "memory" | "context-engine"; // mirrors OpenClawPluginDefinition.kind
  configSchema?: OpenClawPluginConfigSchema | (() => OpenClawPluginConfigSchema);
  register: (api: OpenClawPluginApi) => void;
})
```

Returns a **normalized** object including resolved `configSchema` (defaults to `emptyPluginConfigSchema` from `src/plugins/config-schema.ts`).

## `defineChannelPluginEntry` and channel helpers

**Module:** `openclaw/plugin-sdk/core`  
**Source:** `src/plugin-sdk/core.ts`

Channel plugins should use **`defineChannelPluginEntry`** (or related builders such as `createChatChannelPlugin` / `createChannelPluginBase`) so **channel capability wiring** matches OpenClaw expectations.

See official: `docs/plugins/sdk-channel-plugins.md` and `docs/plugins/sdk-entrypoints.md`.

## `defineSetupPluginEntry`

Also exported from `plugin-sdk/core` for setup-only surfaces (onboarding, pairing) where full registration should not run on every process shape.

## `OpenClawPluginConfigSchema`

From `src/plugins/types.ts`:

- `safeParse(value)` / `parse(value)` — Zod-like
- `validate(value) → { ok, errors }`
- `uiHints` — per-field UI metadata (`PluginConfigUiHint`)
- `jsonSchema` — optional parallel JSON schema

**Rule:** Keep **`openclaw.plugin.json` → `configSchema`** aligned with this object so offline validation matches runtime parsing.

## Alternative module exports

OpenClaw supports loading:

- `OpenClawPluginDefinition` object (with `register` / `activate`)
- A **function** `(api) => void` as `OpenClawPluginModule`

Prefer `definePluginEntry` for stable shape and future fields.

## Imports: subpath discipline

Always import from **specific** `openclaw/plugin-sdk/...` subpaths (100+ entrypoints). This keeps startup fast and avoids circularity.

Authoritative list: `scripts/lib/plugin-sdk-entrypoints.json` in the OpenClaw repo (see `docs/plugins/sdk-overview.md`).

## `activate` vs `register`

`OpenClawPluginDefinition.activate` exists for lifecycle separation; many plugins only implement `register`. When using `definePluginEntry`, use `register` unless you intentionally split phases and the loader is invoked with `activate: true`.
