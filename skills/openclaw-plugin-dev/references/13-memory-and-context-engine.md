# Memory plugins and context engine

## `PluginKind`

From `src/plugins/types.ts`:

- `PluginKind = "memory" | "context-engine"`

Set via manifest `kind` and/or `definePluginEntry({ kind })` when applicable.

## Memory exclusivity model

`OpenClawPluginApi` memory registrations (`src/plugins/types.ts`):

| API | Slot policy |
| --- | --- |
| `registerMemoryPromptSection` | Exclusive — one active builder |
| `registerMemoryFlushPlan` | Exclusive — one resolver |
| `registerMemoryRuntime` | Exclusive — one runtime adapter |
| `registerMemoryEmbeddingProvider` | Multiple adapters allowed **per process**, but each **`adapter.id` must be unique** — duplicates produce a registry **error** diagnostic |

Loader restores/clears memory plugin state and embedding provider lists across reloads (see imports in `src/plugins/loader.ts`: `memory-state`, `memory-embedding-providers`).

Use these APIs only when implementing a **first-class memory backend**; casual tools should not grab exclusive slots.

## Runtime enforcement (`src/plugins/registry.ts`)

When `registrationMode === "full"`, the registry **rejects** all `registerMemory*` calls unless the loaded plugin record has **`kind === "memory"`** (matches manifest / entry `kind`). Otherwise it records an **error** diagnostic (“only memory plugins can register …”).

Non-memory plugins must not call these APIs — they will not register.

## Context engine

`registerContextEngine(id, factory)` — **exclusive globally**: only one active context engine at a time (comment on `OpenClawPluginApi`).

Factory type: `ContextEngineFactory` from `src/context-engine/registry.ts`.

**Reserved id:** the registry **rejects** `id === "legacy"` — that id matches `defaultSlotIdForKey("contextEngine")` and is reserved for core (`registry.ts` diagnostic: “context engine id reserved by core”). Choose a different string id for plugin-owned engines.

**User config:** `plugins.slots.contextEngine` selects which plugin id supplies the context engine for runs (see `references/19-user-config-plugins-block.md`).

Delegation helper: `delegateCompactionToRuntime` exported from `openclaw/plugin-sdk/core` (`src/plugin-sdk/core.ts`).

## Bundled references

Inspect `extensions/memory-*` and related plugins in OpenClaw `extensions/` for working examples (names may change; grep `registerMemory` in the pinned tree).

## Related docs

- Official plugin architecture page discusses memory slots at a high level (`docs/plugins/architecture.md`).
- SDK overview lists memory-related subpaths if present for your pinned version (`docs/plugins/sdk-overview.md`).
