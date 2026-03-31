# OpenClaw official plugin docs (in-repo index)

**Navigation:** Use `SKILL.md` (this skill’s root) for the topic → `references/NN-*.md` map; open **this** file when you need the upstream `docs/plugins/*.md` filename for deeper narrative or tutorials.

These files live under **`docs/plugins/`** in the OpenClaw repository. They are the primary **English** narrative documentation. This skill duplicates none of them verbatim; use them for tutorials and prose, and use `src/plugins/*` for truth when behavior diverges.

| File | Topic |
| --- | --- |
| `architecture.md` | Internals: capability model, shapes, contracts, load pipeline commentary |
| `building-plugins.md` | Getting started, minimal tool plugin |
| `building-extensions.md` | Extension/bundle-oriented authoring |
| `bundles.md` | Compatible bundle formats |
| `manifest.md` | `openclaw.plugin.json` deep dive |
| `community.md` | Community ecosystem notes |
| `agent-tools.md` | Agent tools from a user/plugin perspective |
| `sdk-overview.md` | SDK subpaths + registration API summary |
| `sdk-entrypoints.md` | Entry helper specifics |
| `sdk-channel-plugins.md` | Channel plugin guide |
| `sdk-provider-plugins.md` | Provider plugin guide |
| `sdk-runtime.md` | Runtime helpers discussion |
| `sdk-setup.md` | Setup flows |
| `sdk-testing.md` | Testing plugins |
| `sdk-migration.md` | Migrating across SDK/host versions |
| `voice-call.md` | Voice-call plugin-specific notes |
| `zalouser.md` | Zalo user plugin notes |

## Web mirror

Public docs site (URL may redirect): start from **`https://docs.openclaw.ai`** and navigate to Plugins — content is generated from the same sources as the docs directory in git.

## Snippets and publish templates

ClawHub / npm publish examples may live under `docs/snippets/plugin-publish/` — check the pinned OpenClaw tree for exact paths.
