# Synthetic capability providers (non-text)

These capabilities have **dedicated `register*` methods** on `OpenClawPluginApi` (`src/plugins/types.ts`):

| Method | Type name (approx) | Role |
| --- | --- | --- |
| `registerSpeechProvider` | `SpeechProviderPlugin` | TTS / voice output |
| `registerMediaUnderstandingProvider` | `MediaUnderstandingProviderPlugin` | Image/video/audio understanding |
| `registerImageGenerationProvider` | `ImageGenerationProviderPlugin` | Image generation backends |
| `registerWebSearchProvider` | `WebSearchProviderPlugin` | Web search backends |

## Manifest `contracts`

Populate the matching `contracts.*` string arrays so static analysis and bundled wiring see ownership without executing code:

- `speechProviders`
- `mediaUnderstandingProviders`
- `imageGenerationProviders`
- `webSearchProviders`

## Runtime helpers (`api.runtime`)

Trusted calls exposed on `PluginRuntime` (`src/plugins/runtime/types-core.ts` + `types.ts`):

- `runtime.tts.*` — `textToSpeech`, telephony variant, `listVoices`
- `runtime.mediaUnderstanding.*` — file describe/transcribe helpers
- `runtime.imageGeneration.*` — `generate`, `listProviders`
- `runtime.webSearch.*` — `listProviders`, `search`

Plugin implementations often **wrap** or **delegate** to these when extending core behavior, but third-party plugins should follow the **registration types** first.

For the **full `api.runtime` namespace table** (including `agent`, `system`, `media`, `stt`, `events`, `logging`, subagent, and channel surfaces), see `references/15-plugin-runtime-surface.md`.

## Hybrid plugins

Vendors like OpenAI may register **text + speech + media + image** in one plugin — see `references/07-capabilities-and-compatibility.md` (“hybrid-capability”).

## Authoring tips

1. Start from the **smallest bundled example** owning that capability (grep `registerWebSearchProvider` / `registerSpeechProvider` under `extensions/`).
2. Mirror manifest `contracts` and `register*` together.
3. Use `api.logger` for structured operational logs; avoid `console.log` in production plugins.
