# Tools, commands, interactive handlers, HTTP, gateway, CLI, services

## Agent tools — `registerTool`

- Accepts a concrete `AnyAgentTool` **or** a **factory** `(ctx: OpenClawPluginToolContext) => tool | tools | null`.
- Options (`OpenClawPluginToolOptions`): `name`, `names`, `optional`.

Factory context includes trusted routing fields (`deliveryContext`, `sessionId`, `senderIsOwner`, …). **Do not** treat tool arguments as identity.

## Plugin commands — `registerCommand`

For commands that should bypass the LLM (toggle state, status):

- Processed **before** built-in commands and **before** agent invocation (`OpenClawPluginCommandDefinition`).

## Interactive handlers — `registerInteractiveHandler`

Union type `PluginInteractiveHandlerRegistration` (`src/plugins/types.ts`) includes **Telegram**, **Discord**, and **Slack** variants — register the shape that matches your channel. Registration may **warn** if the handler package/layout is invalid (`registerPluginInteractiveHandler` result in `registry.ts`).

## HTTP routes — `registerHttpRoute`

Params (`OpenClawPluginHttpRouteParams` in `src/plugins/types.ts`):

- `path`, `handler` (`IncomingMessage` / `ServerResponse` → `boolean | void | Promise<…>`)
- **`auth`:** `"gateway"` \| `"plugin"` — who authenticates the request
- **`match`:** `"exact"` \| `"prefix"` (optional; controls path matching)
- **`replaceExisting`:** optional boolean to replace a conflicting route

Core normalizes paths and can detect overlaps (`normalizePluginHttpPath`, `findOverlappingPluginHttpRoute`). Consider authentication and SSRF policies carefully.

## Gateway methods — `registerGatewayMethod`

Extend the gateway JSON-RPC surface:

- `method` string + `GatewayRequestHandler`
- Optional `{ scope }` with `OperatorScope` — prefer least privilege.

## CLI — `registerCli`

Attach Commander registrars to the OpenClaw CLI:

- Pass explicit `commands` roots when declaring ownership.
- Supply `descriptors` so OpenClaw can lazy-load registrars on the normal root CLI path.

## Services — `registerService`

Long-running plugin services (`OpenClawPluginService` + `OpenClawPluginServiceContext` in `types.ts`). Use for background tasks that integrate with gateway lifecycle.

## Conversation bindings — `onConversationBindingResolved`

Hook channel/session binding events for plugins that need to react when conversations are bound to agents/sessions.

## Related skill chapters

- `references/04-openclaw-plugin-api.md` — full `OpenClawPluginApi` registration table, noop modes, and `registrationMode` values
- `references/06-hooks-and-internal-events.md` — `registerHook` and `api.on` for cross-cutting agent/gateway behavior
- `references/19-user-config-plugins-block.md` — how hosts limit tool and subagent model overrides via `plugins.entries.*.subagent`

## Related official docs

- `docs/plugins/agent-tools.md` — agent-facing tools patterns
- `docs/plugins/building-plugins.md` — minimal tool plugin walkthrough
