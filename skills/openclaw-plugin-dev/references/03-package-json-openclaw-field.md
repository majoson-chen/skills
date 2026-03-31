# Package metadata: `package.json` → `openclaw`

## Source of truth

- `src/plugins/manifest.ts` — types `OpenClawPackageManifest`, `resolvePackageExtensionEntries`, `getPackageManifestMetadata`.
- The JSON key on `package.json` follows `MANIFEST_KEY` from `src/compat/legacy-names.ts` (project name: **`openclaw`**).

## `OpenClawPackageManifest`

### `extensions?: string[]`

**Required for normal plugins:** one or more entry files (TS/JS) executed by the loader.

Resolution:

- `resolvePackageExtensionEntries` returns `ok` | `missing` | `empty`.
- If missing/empty, the package may still participate in discovery as a bundle or fail depending on layout — follow upstream discovery rules in `src/plugins/discovery.ts`.

Default entry **candidates** when using classic layout: `index.ts`, `index.js`, `index.mjs`, `index.cjs` (`DEFAULT_PLUGIN_ENTRY_CANDIDATES`) — but explicit `openclaw.extensions` is the reliable approach.

### `setupEntry?: string`

Separate module for **setup / onboarding** surfaces. Used when channel or provider setup should run without pulling in the full gateway registration path.

Interaction with loader options:

- `preferSetupRuntimeForChannelPlugins` (see `references/01-lifecycle-discovery-and-load.md`) can prefer this for configured channel plugins that opt in via package metadata.

### `channel?: PluginPackageChannel`

Channel-specific package metadata (labels, docs toggles, selection UX hints). See type `PluginPackageChannel` in `src/plugins/manifest.ts` for the full field set (`selectionLabel`, `markdownCapable`, `forceAccountBinding`, etc.).

### `install?: PluginPackageInstall`

- `npmSpec`, `localPath`, `defaultChoice`, **`minHostVersion`**

Use `minHostVersion` to prevent incompatible host installs.

### `startup?: OpenClawPackageStartup`

- `deferConfiguredChannelFullLoadUntilAfterListen?: boolean` — opt-in for channel plugins whose `setupEntry` covers everything needed before listen; allows deferring full channel plugin load until after the server listens.

## Compatibility / build metadata (common in extensions)

Many in-repo `extensions/*/package.json` also include:

```json
"openclaw": {
  "extensions": ["./index.ts"],
  "compat": {
    "pluginApi": ">=2026.3.24-beta.2",
    "minGatewayVersion": "2026.3.24-beta.2"
  },
  "build": {
    "openclawVersion": "2026.3.24-beta.2",
    "pluginSdkVersion": "2026.3.24-beta.2"
  }
}
```

Exact `compat` / `build` keys are **not** fully enumerated in this skill snapshot — grep `extensions/*/package.json` in the pinned OpenClaw tag for current patterns, and mirror them for third-party packages targeting the same host.

## Split responsibilities (manifest vs package)

| Concern | Where |
| --- | --- |
| Plugin id, static capability hints, JSON config schema | `openclaw.plugin.json` |
| Which files to execute, setup entry, install constraints | `package.json` → `openclaw` |
| Runtime registration | Exported plugin entry module |
