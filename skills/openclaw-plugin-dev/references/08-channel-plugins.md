# Channel plugins

## Why channels are special

Channels integrate **inbound messaging**, **outbound delivery**, threading, pairing/security, media, and often **setup** flows. OpenClaw factors these into typed **adapters** and **surfaces** under `src/channels/plugins/`.

## Registration

`api.registerChannel(channelPlugin)` or `api.registerChannel({ plugin })` (`OpenClawPluginChannelRegistration` in `src/plugins/types.ts`).

## SDK entry

Use **`defineChannelPluginEntry`** from `openclaw/plugin-sdk/core` (not bare `definePluginEntry`) so channel capability wiring stays consistent (`src/plugin-sdk/core.ts`).

## Core type

`ChannelPlugin` — `src/channels/plugins/types.plugin.ts` (imported through channel plugin types).

Key adapter families (see `types.core.ts`, `types.adapters.ts`):

- **Messaging** — inbound/outbound pipelines
- **Threading** — reply routing, thread keys
- **Security / pairing** — allowlists, approvals
- **Outbound** — delivery to surfaces

## Subpath toolkit (official list excerpt)

From `docs/plugins/sdk-overview.md`:

- `plugin-sdk/channel-setup` — optional setup surfaces
- `plugin-sdk/channel-pairing` — pairing controllers
- `plugin-sdk/channel-reply-pipeline` — reply pipeline factory
- `plugin-sdk/channel-config-helpers` — hybrid config adapters
- `plugin-sdk/channel-config-schema` — schema types
- `plugin-sdk/channel-policy` — mention / group policies
- `plugin-sdk/channel-lifecycle` — account status sinks
- `plugin-sdk/channel-inbound` — debounce, mention matching, envelopes
- `plugin-sdk/channel-send-result` — result types
- `plugin-sdk/channel-actions` — structured actions (buttons/cards)
- `plugin-sdk/channel-targets` — target parsing
- `plugin-sdk/channel-contract` — contract types
- `plugin-sdk/channel-feedback` — reactions / feedback

## Manifest extras

- List owned channels in manifest `channels: string[]`.
- Use `channelConfigs` for per-channel JSON schema + UI hints (`PluginManifestChannelConfig`).

## Package metadata

- `openclaw.channel` block for UX/docs hints (`PluginPackageChannel` in `src/plugins/manifest.ts`).
- `openclaw.startup.deferConfiguredChannelFullLoadUntilAfterListen` when `setupEntry` covers pre-listen needs.

## Host enable logic (important for channels)

Bundled channel plugins that are **disabled by default** in their manifest can still be enabled when the **corresponding channel** is configured (`channels.<channelId>.enabled: true`). This is an exception in `resolveEffectiveEnableState` — see `references/19-user-config-plugins-block.md` for the full rule set including allow/deny, slots, and workspace defaults.

## Learning path

1. Read **`docs/plugins/sdk-channel-plugins.md`** in OpenClaw.
2. Study a **small** bundled extension (grep `defineChannelPluginEntry` under `extensions/`).
3. Cross-check adapter interfaces in `src/channels/plugins/types*.ts`.
