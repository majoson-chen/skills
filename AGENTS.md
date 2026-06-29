# Agent 说明 — 个人 Agent Skills

本仓库是面向 Coding Agent 的 [Agent Skills](https://agentskills.io/specification) 小集合。

## 目录结构

```
skills/
  <skill-name>/
    SKILL.md              # 必需 — 技能正文 + YAML frontmatter
    README.md             # 可选 — 人类可读概览（大型参考类技能）
    references/           # 可选 — 按需加载的章节
    scripts/              # 可选 — 辅助脚本
    ...
```

- **不要**在 `~/.cursor/skills-cursor/` 下新增技能 — 该路径保留给 Cursor 内置技能。
- 新增或重命名技能后，同步更新 `[readme.md](readme.md)` 中的表格。

## 在本仓库中工作

### 修改前先做的事

1. **阅读目标技能** — 打开 `skills/<name>/SKILL.md`，以及本次编辑相关的 `references/` 文件。
2. **控制改动范围** — 一个目录对应一个技能；避免在一次改动中无关地修改多个技能。

### 编写或编辑技能

**唯一入口：加载并遵循** `skill-creator`（Anthropic 官方技能创作流程，含起草、评测、迭代、description 优化）。

路径：`.agents/skills/skill-creator/SKILL.md`

不要再用 `create-skill`、`writing-skills` 或其他技能编写指南——二者与 `skill-creator` 在 description 写法、验证流程上存在冲突，以 `skill-creator` 为准。

规范与格式以 [Agent Skills 规范](https://agentskills.io/specification) 及 `skill-creator` 为准。

**本仓库额外惯例**（`skill-creator` 未覆盖时）：

- 待发布的技能放在仓库根目录 `skills/`；`skill-creator` 自身在 `.agents/skills/`（创作工具，不列入 `readme.md` 目录表）。
- 勿在 `~/.cursor/skills-cursor/` 下新建技能（Cursor 内置保留路径）。
- 技能正文默认**英文**；绑定上游产品的领域技能须在 `metadata` 与 `00-`* 章节写明版本锚定。
- 大型参考类技能沿用 `openclaw-plugin-dev` 的渐进式披露：`SKILL.md` 作路由，`references/` 放章节。

**Cursor 环境注意**：`skill-creator` 中依赖 Claude Code 子代理、`claude -p` CLI 或浏览器的步骤，在 Cursor 中按技能内的 Claude.ai / Cowork 适配说明降级执行（人工 review 替代 benchmark viewer、跳过 description 自动优化等）。

### Git 与提交

**不要主动 commit**，除非用户明确要求。本仓库常用于发布前 review。

## 技能目录（速查）


| 技能                      | 路径                            | 使用场景                                         |
| ----------------------- | ----------------------------- | -------------------------------------------- |
| **commit**              | `skills/commit/`              | `/commit`、撰写 message、拆分变更集、安全暂存              |
| **openclaw-plugin-dev** | `skills/openclaw-plugin-dev/` | 开发或调试 OpenClaw 原生插件（锚定 `openclaw@2026.3.28`） |
| **using-agentsmd**      | `skills/using-agentsmd/`      | 创建/维护 `AGENTS.md`（写、改、审计、init）；仅写改时加载       |
| **using-subagent**      | `skills/using-subagent/`      | subagent 决策与派发（何时派、并行/串行、模型继承）；提及 subagent 或情境需派发时加载 |


执行工作流前须加载完整技能文件 — 不可仅凭下表即兴发挥。

编写或维护本文件（`AGENTS.md`）时，加载并遵循 `using-agentsmd`（`skills/using-agentsmd/SKILL.md`）。

## 完成前自检

- [ ] 已按 **skill-creator** 完成评测迭代（若适用；小改可只做人工 sanity check）。
- [ ] 目录有变时已更新 `readme.md` 表格。
- [ ] diff 中无密钥或编辑器垃圾文件。

<important-notes>
此文件为 `AGENTS.md`。事实变更时加载 `using-agentsmd` 及时修改。
</important-notes>