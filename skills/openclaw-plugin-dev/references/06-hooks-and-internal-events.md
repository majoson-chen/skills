# Hooks: `api.on` vs `registerHook`

## Typed plugin hooks — `api.on`

**Type:** `PluginHookName` + `PluginHookHandlerMap` in `src/plugins/types.ts`.

**Constant list:** `PLUGIN_HOOK_NAMES` (same file) — exhaustive at the pinned version.

### Names (as of the architecture snapshot)

Includes, among others:

- Model / prompt / agent: `before_model_resolve`, `before_prompt_build`, `before_agent_start`, `llm_input`, `llm_output`, `agent_end`
- Compaction / reset: `before_compaction`, `after_compaction`, `before_reset`
- Messaging: `inbound_claim`, `message_received`, `message_sending`, `message_sent`, `before_message_write`
- Tools: `before_tool_call`, `after_tool_call`, `tool_result_persist`
- Session: `session_start`, `session_end`
- Subagents: `subagent_spawning`, `subagent_delivery_target`, `subagent_spawned`, `subagent_ended`
- Gateway: `gateway_start`, `gateway_stop`, `before_dispatch`, `before_install`

**Priority:** `api.on(name, handler, { priority? })` — higher priority runs earlier when multiple handlers exist.

### Migration guidance (from official internals doc)

- `before_agent_start` is **legacy** but supported.
- Prefer **`before_model_resolve`** for provider/model override work.
- Prefer **`before_prompt_build`** for prompt mutation.

## Internal hook runner — `registerHook`

`registerHook(events: string | string[], handler: InternalHookHandler, opts?)`

Uses `InternalHookHandler` from `src/hooks/internal-hooks.ts` — broader internal integration surface than the typed `api.on` map.

**Guidance:** Third-party automation should prefer **`api.on`** where possible; `registerHook` is for deeper internal integration that may track core internals more closely.

## `registerHook` options (`OpenClawPluginHookOptions`)

- `entry?: HookEntry`
- `name?`, `description?`
- `register?: boolean` — conditional registration flag interpreted by core

## Global hook runner

Loader initializes global hook runner (`initializeGlobalHookRunner` import in `loader.ts`). Plugin hooks participate in the orchestrated agent/gateway lifecycle.

## Debugging hooks

- Confirm hook name spelling against `PLUGIN_HOOK_NAMES` (the constant list exported from `src/plugins/types.ts`); the typed `api.on` enforces names at compile-time, so type errors surface before runtime. `registerHook` with a raw string silently registers but may never fire if the name is wrong.
- Watch for **ordering** vs other plugins; use `priority` when deterministic ordering is required.

## Host-side hook control

Some hook behavior can be **gated at the host config level**: `plugins.entries.<pluginId>.hooks.allowPromptInjection` controls whether `before_prompt_build` and legacy `before_agent_start` prompt mutations from this plugin are applied. Hooks still **fire** regardless; only the prompt-mutation result is discarded when not allowed.

See `references/19-user-config-plugins-block.md` for the full per-plugin entry config schema.
