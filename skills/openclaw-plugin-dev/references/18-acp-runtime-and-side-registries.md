# ACP runtime backends and other side registries (not on `OpenClawPluginApi`)

## Why this exists

Some bundled plugins register behavior through **narrow SDK modules** that call into a **core registry** directly, instead of using a method on `OpenClawPluginApi`. If you only read `OpenClawPluginApi` in `src/plugins/types.ts`, you will miss these patterns.

## ACP (Agent Client Protocol) runtime backend

**Source:** `src/plugin-sdk/acpx.ts` (re-exports from `src/acp/runtime/registry.ts`).

**APIs:**

- `registerAcpRuntimeBackend(backend: AcpRuntimeBackend): void`
- `unregisterAcpRuntimeBackend(id: string): void`

**Import:** `openclaw/plugin-sdk/acpx` (or the extension-local re-export pattern used under `extensions/acpx/`).

**Comment in upstream:** `acpx.ts` describes the surface as *private ACPX runtime backend helpers for bundled extensions* and asks to keep the contract narrow.

**Example consumer:** `extensions/acpx/` registers a backend during plugin startup and unregisters on teardown.

## Takeaway for plugin authors

1. Not every integration point is `api.register*`. Grep **`extensions/*/index.ts`** and **`extensions/*/src/*.ts`** for `register` and `openclaw/plugin-sdk` imports when cloning a pattern.
2. Treat ACP backends as **specialized**; follow an existing bundled plugin (e.g. `acpx`) rather than inventing a parallel registry.

## Related

- `references/04-openclaw-plugin-api.md` — primary injection surface
- `references/05-entrypoints-and-config-schema.md` — SDK subpath discipline
