# Testing, inspection, and debugging

## Official testing guide

Read `docs/plugins/sdk-testing.md` in the OpenClaw repository for Vitest patterns, SDK import guardrails, and channel/provider testing notes.

Key ideas typically covered:

- Use **`openclaw/plugin-sdk/testing`** helpers when available in your pinned version.
- Mind **ESM** and **subpath** imports in tests.
- Some bundled plugins use **Vitest-specific registries** or env (`VITEST`) to prefer source `extensions/` — mirror patterns from similar extensions when writing in-repo plugin tests.

## Loader diagnostics

`PluginDiagnostic` (`src/plugins/types.ts`):

```typescript
{ level: "warn" | "error"; message: string; pluginId?: string; source?: string }
```

`PluginLoadFailureError` aggregates failed plugins when `throwOnLoadError` is used (`src/plugins/loader.ts`).

## Local iteration checklist

1. **Validate manifest JSON** against expectations in `docs/plugins/manifest.md`.
2. Ensure `package.json` → `openclaw.extensions` paths resolve from package root.
3. Run **`openclaw doctor`** and **`openclaw plugins inspect <id>`** on a scratch workspace.
4. For gateway issues, capture logs with verbose logging if your host build supports it.

## Source-level debugging

Set breakpoints in:

- `src/plugins/discovery.ts` — is the plugin discovered?
- `src/plugins/loader.ts` — is the module executed; any noop registers?
- Your plugin entry `register(api)` — which mode is `api.registrationMode`?

## Common failure modes

- **Manifest missing/invalid** — hard error during config validation.
- **Schema mismatch** — config fails `safeParse` / JSON schema validation.
- **Wrong entry file** — `extensions` not listing the actual built output (dist vs src) for published packages.
- **Exclusive slot conflicts** — two plugins registering memory or context engine — last writer wins or error depending on core policy; check loader memory-state helpers.
