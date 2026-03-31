# `api.runtime` (`PluginRuntime`)

## Purpose

Trusted, in-process helpers for **native** plugins. Broader than hooks; prefer hooks for simple cross-cutting behavior unless you need these integrations.

**Types:** `src/plugins/runtime/types.ts` composes `PluginRuntimeCore` + `subagent` + `channel`.

## `PluginRuntimeCore` highlights (`src/plugins/runtime/types-core.ts`)

| Namespace | Examples |
| --- | --- |
| `version` | Runtime version string |
| `config` | `loadConfig`, `writeConfigFile` |
| `agent` | Defaults, `runEmbeddedPiAgent`, workspace helpers, session store load/save |
| `system` | System events, heartbeat (`requestHeartbeatNow`, `runHeartbeatOnce`), `runCommandWithTimeout`, native dep hints |
| `media` | `loadWebMedia`, MIME detection, image resize/metadata |
| `tts` | Delegates to `speech-runtime` (`textToSpeech`, telephony, `listVoices`) |
| `mediaUnderstanding` | File describe/transcribe helpers |
| `imageGeneration` | `generate`, `listProviders` |
| `webSearch` | `listProviders`, `search` |
| `stt` | Audio transcription helper |
| `events` | Agent + transcript event subscriptions |
| `logging` | Verbose checks, child loggers |

(Fields may extend in newer OpenClaw versions — diff `types-core.ts` when upgrading.)

## Subagent surface

`runtime.subagent`:

- `run`, `waitForRun`, `getSessionMessages`, `deleteSession`
- Deprecated aliases `getSession` — avoid in new code

## Channel surface

`runtime.channel` — `PluginRuntimeChannel` (`types-channel.ts`) for channel-scoped runtime integration.

## Usage discipline

1. Avoid persisting sensitive data from runtime helpers into world-readable files.
2. Prefer narrow SDK subpaths for **stable** utilities; `runtime` exposes powerful config/session access — treat as **trusted-only**.

## Related skill chapters

- `references/10-synthetic-capability-providers.md` — registration APIs for capabilities whose runtime counterparts (`tts.*`, `webSearch.*`, etc.) appear in this table
- `references/04-openclaw-plugin-api.md` — how `runtime` is injected into the `api` object (`api.runtime` field)
