---
name: openclaw-plugin-dev
description: Exhaustive OpenClaw native plugin reference—openclaw.plugin.json, package.json openclaw block, host plugins.* config (allow/deny/slots/entries/load.paths), discovery and loader, OpenClawPluginApi plus registration diagnostics, hooks, channels, text providers and registerCliBackend, speech/media/image/websearch, auth onboarding, tools Gateway CLI HTTP services, memory and context-engine, bundles, PluginRuntime, ACP SDK side registries, testing and plugins inspect/doctor. Use when building, debugging, or correlating with OpenClaw source. Anchored to openclaw@2026.3.28; English; load references/ on demand.
compatibility: Requires Node 22+, TypeScript ESM, and a checkout or install of openclaw matching metadata.openclaw_version. Optional local OpenClaw source tree for line-level verification.
metadata:
  openclaw_version: "2026.3.28"
  skill_kind: reference-book
  language: en
  upstream_docs_hint: "docs.openclaw.ai plugins section; OpenClaw repo docs/plugins/*.md"
---

# OpenClaw plugin development (book-style skill)

This skill is a **progressive-disclosure manual** for OpenClaw’s **native plugin system**. Read `SKILL.md` first for routing; load `references/*` only for the topic at hand.

## Audience and version anchor

- **Audience:** Developers comfortable with JS/TS, Node ESM, and CLI tooling. Not a general frontend tutorial.
- **Version:** Treat **`openclaw@2026.3.28`** as the contract anchor. Newer OpenClaw versions may differ; diff `src/plugins/` and `src/plugin-sdk/` in upstream when upgrading.

## How agents should use this skill

1. **Classify the task:** manifest-only change, new capability (channel/provider/speech/…​), tool/hook, CLI/Gateway surface, memory/context-engine, or bundle compatibility.
2. **Open one reference file** from the map below instead of loading everything. Cross-chapter pointers always use paths **relative to this skill directory**, e.g. `references/04-openclaw-plugin-api.md`.
3. **Cross-check upstream:** Official English docs live under `docs/plugins/` in the OpenClaw repository; types and behavior are authoritative in `src/plugins/types.ts`, `src/plugins/manifest.ts`, `src/plugins/loader.ts`, and `src/plugin-sdk/*`. Paths under `src/` in this skill match a **source checkout** (`.ts` files); published packages may list `.js` in import traces.
4. **Respect compatibility stance:** Capability registration is the direction of travel; hook-only plugins remain a supported baseline for external integrations. See `references/07-capabilities-and-compatibility.md`.

## Recommended reading order (first-time mastery)

1. `references/00-version-and-scope.md`
2. `references/01-lifecycle-discovery-and-load.md`
3. `references/02-manifest-openclaw-plugin-json.md`
4. `references/03-package-json-openclaw-field.md`
5. `references/04-openclaw-plugin-api.md`
6. `references/05-entrypoints-and-config-schema.md`
7. `references/06-hooks-and-internal-events.md`
8. `references/19-user-config-plugins-block.md` (how hosts enable/configure your plugin; also links hook behavior to `hooks.allowPromptInjection`)
9. Branch by task: capabilities `07`–`11`, surfaces `12`–`13`, then as needed **`14`** bundles, **`15`** `api.runtime`, **`16`** testing/debug, **`17`** upstream doc index, **`18`** ACP / side registries.

## Topic → reference map

| Topic | File |
| --- | --- |
| Version, scope, evidence chain | `references/00-version-and-scope.md` |
| Discovery roots, loader, modes, env overrides | `references/01-lifecycle-discovery-and-load.md` |
| `openclaw.plugin.json` schema and contracts | `references/02-manifest-openclaw-plugin-json.md` |
| `package.json` → `openclaw` block (entries, setup, channel, install) | `references/03-package-json-openclaw-field.md` |
| Full registration API (`OpenClawPluginApi`) | `references/04-openclaw-plugin-api.md` |
| `definePluginEntry`, `defineChannelPluginEntry`, config schema | `references/05-entrypoints-and-config-schema.md` |
| Hooks: `api.on`, `registerHook`, hook names list | `references/06-hooks-and-internal-events.md` |
| Capability matrix + external compatibility | `references/07-capabilities-and-compatibility.md` |
| Channel plugins (registration + doc pointers) | `references/08-channel-plugins.md` |
| Text inference + `registerCliBackend` | `references/09-provider-text-inference.md` |
| Speech / media understanding / image / web search providers | `references/10-synthetic-capability-providers.md` |
| Auth, onboarding, manifest `providerAuthChoices` | `references/11-provider-auth-and-onboarding.md` |
| Tools, commands, interactive handlers, HTTP, Gateway methods, CLI, services | `references/12-tools-cli-gateway-and-services.md` |
| Memory plugin slots, embeddings, context engine | `references/13-memory-and-context-engine.md` |
| Bundle formats (Codex/Claude/Cursor) vs native | `references/14-bundles-and-formats.md` |
| `api.runtime` (`PluginRuntime`) overview | `references/15-plugin-runtime-surface.md` |
| Testing, inspection, debugging load failures | `references/16-testing-and-debugging.md` |
| Official doc files in OpenClaw repo (index) | `references/17-official-docs-index.md` |
| ACP / side registries (not on `OpenClawPluginApi`) | `references/18-acp-runtime-and-side-registries.md` |
| Host `plugins.*` config (allow/deny, slots, entries, load.paths) | `references/19-user-config-plugins-block.md` |

## Minimal mental model

- **Manifest (`openclaw.plugin.json`):** Static metadata and JSON config schema — read **before** plugin code runs. Used for validation and discovery of auth/capabilities.
- **Package metadata (`package.json` → `openclaw`):** Entry file paths (`extensions`), optional `setupEntry`, channel/install/startup hints.
- **Entry module:** Exports `definePluginEntry` / `defineChannelPluginEntry` (or compatible shape). Receives `OpenClawPluginApi` and registers capabilities, tools, hooks, etc.
