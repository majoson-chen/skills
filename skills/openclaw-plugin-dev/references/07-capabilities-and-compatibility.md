# Capabilities, shapes, and compatibility stance

## Official capability table

From `docs/plugins/architecture.md` (Plugin Internals):

| Capability | Registration API | Typical examples |
| --- | --- | --- |
| Text inference | `registerProvider` | `openai`, `anthropic`, `openrouter` |
| CLI inference backend | `registerCliBackend` | provider plugins exposing CLI backends |
| Speech | `registerSpeechProvider` | cloud TTS plugins |
| Media understanding | `registerMediaUnderstandingProvider` | multimodal understanding |
| Image generation | `registerImageGenerationProvider` | image APIs |
| Web search | `registerWebSearchProvider` | search backends |
| Channel / messaging | `registerChannel` | chat platforms |

## Plugin shapes (runtime classification)

OpenClaw classifies loaded plugins by **what they actually register**:

- **plain-capability** — exactly one capability family
- **hybrid-capability** — multiple capabilities (e.g. OpenAI-style multimodal stacks)
- **hook-only** — only hooks, no tools/commands/services/capabilities
- **non-capability** — tools/commands/services/routes without capability registration

Use **`openclaw plugins inspect <id>`** to view shape and breakdown.

## External compatibility stance (summary)

Also from `docs/plugins/architecture.md`:

- **Existing external plugins:** hook-based integrations remain the conservative compatibility baseline.
- **New native/bundled plugins:** prefer explicit **capability registration** over ad-hoc hook-only designs.
- **Exported SDK subpaths:** not all exports are equally stable; prefer **narrow documented** imports from `docs/plugins/sdk-overview.md`.

Practical rules:

1. Prefer `registerProvider` / `registerChannel` / synthetic providers when modeling a first-class capability.
2. Use hooks when you must intercept cross-cutting agent/gateway behavior without owning a capability.
3. Avoid reaching into undocumented core modules from third-party plugins.

## Manifest alignment

Mirror ownership in `openclaw.plugin.json`:

- `providers`, `channels`, `cliBackends`
- `contracts.*` arrays
- `providerAuthEnvVars`, `providerAuthChoices`

Drift confuses discovery, doctor, and onboarding.
