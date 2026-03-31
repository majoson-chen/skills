# `OpenClawPluginApi` — complete registration surface

## Source

`export type OpenClawPluginApi` in `src/plugins/types.ts` (around the `OpenClawPluginDefinition` / `OpenClawPluginApi` block).

**Note:** Some features use **separate registries** imported from `openclaw/plugin-sdk/*` (not methods on `api`). Example: ACP runtime backends — see `references/18-acp-runtime-and-side-registries.md`.

## Injected context (fields)

| Field | Meaning |
| --- | --- |
| `id`, `name`, `version?`, `description?` | Plugin identity |
| `source` | Resolved entry path |
| `rootDir?` | Plugin root on disk |
| `registrationMode` | `full` \| `setup-only` \| `setup-runtime` \| `cli-metadata` |
| `config` | Full resolved `OpenClawConfig` |
| `pluginConfig?` | This plugin’s config slice |
| `runtime` | `PluginRuntime` trusted helpers (see `references/15-plugin-runtime-surface.md`) |
| `logger` | `PluginLogger` (`info`/`warn`/`error`, optional `debug`) |

## Registration methods

| Method | Registers |
| --- | --- |
| `registerTool(tool \| factory, opts?)` | Agent tools (`AnyAgentTool` or factory with `OpenClawPluginToolContext`) |
| `registerHook(events, handler, opts?)` | Internal hook runner (`InternalHookHandler`) |
| `registerHttpRoute(params)` | HTTP routes on the gateway server |
| `registerChannel(registration)` | Native **channel** plugin (`ChannelPlugin` or wrapper) |
| `registerGatewayMethod(method, handler, opts?)` | JSON-RPC / gateway method with optional `scope` |
| `registerCli(registrar, opts?)` | CLI subcommands (`commands` / `descriptors` for lazy roots) |
| `registerService(service)` | Long-lived plugin service |
| `registerCliBackend(backend)` | Text-only CLI inference backend |
| `registerProvider(provider)` | Text **model provider** |
| `registerSpeechProvider(provider)` | TTS / speech capability |
| `registerMediaUnderstandingProvider(provider)` | Media understanding |
| `registerImageGenerationProvider(provider)` | Image generation |
| `registerWebSearchProvider(provider)` | Web search |
| `registerInteractiveHandler(registration)` | Interactive channel handlers (e.g. Telegram patterns) |
| `onConversationBindingResolved(handler)` | Conversation binding lifecycle |
| `registerCommand(command)` | Non-LLM slash/custom commands processed before agent |
| `registerContextEngine(id, factory)` | Context engine implementation (**exclusive** slot globally) |
| `registerMemoryPromptSection(builder)` | Memory prompt section (**exclusive** per memory slot policy) |
| `registerMemoryFlushPlan(resolver)` | Pre-compaction flush planner (**exclusive**) |
| `registerMemoryRuntime(runtime)` | Memory runtime adapter (**exclusive**) |
| `registerMemoryEmbeddingProvider(adapter)` | Embeddings adapter (**multiple** allowed) |
| `on(hookName, handler, opts?)` | Typed plugin lifecycle hooks (`PluginHookName`) |
| `resolvePath(input)` | Resolve paths relative to safe plugin roots |

## Tool factories

`OpenClawPluginToolFactory` receives `OpenClawPluginToolContext`:

- `config`, `runtimeConfig`, workspace/agent dirs, `sessionKey`, `sessionId`
- Browser bridge hints, `messageChannel`, `deliveryContext`, `requesterSenderId`, `senderIsOwner`, `sandboxed`, etc.

Use the context for **trusted** ambient data; never treat tool arguments as authentication.

## CLI registration nuances

`registerCli` supports:

- Explicit **`commands`** roots for ownership.
- **`descriptors`** for lazy parse-time registration when descriptors cover all top-level roots.

See inline comments on `OpenClawPluginApi` in `types.ts`.

## Gateway methods

`registerGatewayMethod(method, handler, { scope? })` — `scope` uses `OperatorScope` from gateway method scoping. Prefer narrow scopes.

## When registers are no-ops

`buildPluginApi` (`src/plugins/api-builder.ts`) supplies **noop** implementations for any registration family not wired for the current load mode. Plugins must tolerate being loaded in **metadata-only** modes without side effects that assume full registry mutation.

## Registry guardrails (full mode)

`createApi` in `src/plugins/registry.ts` attaches real handlers only when `registrationMode === "full"` (with exceptions: **`registerCli`** and **`registerChannel`** still exist on non-full paths for setup/metadata). Full-mode-only registrations include tools, hooks, HTTP routes, providers, gateway methods, services, memory, context engine, etc.

Memory and context-engine rules are detailed in `references/13-memory-and-context-engine.md`.

## Registration diagnostics (where failures surface)

Core records **`PluginDiagnostic`** entries (`level: "warn" | "error"`) during registration. Examples from `src/plugins/registry.ts` and `src/plugins/provider-validation.ts` (non-exhaustive):

- **Channel:** `channel registration missing id`; `channel already registered:`; `channel setup already registered:`.
- **Provider:** `provider already registered:`; validation messages such as missing provider id, **auth method** missing/duplicated id, setup/model-picker metadata ignored or method not found (fallback), **both catalog and discovery registered** (catalog wins).
- **Context engine / memory / embedding:** see `references/13-memory-and-context-engine.md` (reserved id, kind checks, duplicate embedding id).
- **Interactive handler:** failed registration returns a warn diagnostic from the registry helper.

`registerTool` itself does not emit the same style of `pushDiagnostic` from `registry.ts`; tool name conflicts may be handled in the tool registry path elsewhere—grep `registerTool` / tool registry if debugging duplicates.

When debugging, pair **`openclaw plugins inspect <id>`** with gateway logs that include plugin diagnostics.
