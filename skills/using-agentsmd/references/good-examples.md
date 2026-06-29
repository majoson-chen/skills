# 正例

每个示例附标签。学习「为何有效」，不要复制结构当模板。

---

## 标签：定向 + 路由 + 边界（本仓库风格）

```markdown
# Agent 说明 — 个人 Agent Skills

本仓库是面向 Coding Agent 的 Agent Skills 小集合。

## 编写或编辑技能

唯一入口：加载并遵循 `skill-creator`（`.agents/skills/skill-creator/SKILL.md`）。

## 技能目录（速查）

| 技能 | 路径 | 使用场景 |
| commit | skills/commit/ | /commit、撰写 message |
| openclaw-plugin-dev | skills/openclaw-plugin-dev/ | OpenClaw 插件开发 |

执行工作流前须加载完整技能文件。

## 完成前自检

- [ ] 目录有变时已更新 readme.md 表格
- [ ] diff 中无密钥
```

**为何有效：** 几句定向；工作流用路由不贴正文；表格是索引不是教程；有边界、无理由。

---

## 标签：关键节点目录（摆放）

```markdown
## 关键目录

- `src/features/` — 功能模块；新页面放这里
- `server/routes/` — HTTP 路由
```

**为何有效：** 浅层、指导摆放，不是深层树。

---

## 标签：绿线（肯定式命令）

```markdown
## 命令

- 全量测试：`pnpm test`
- 单测：`pnpm vitest run -t "<name>"`
- 包内任务：`pnpm --filter <pkg> <script>`
```

**为何有效：** 可复制；无对比噪声；无解释段落。

---

## 标签：嵌套增量

`packages/api/AGENTS.md` 仅写：

```markdown
# API 包

## 命令

- 本包测试：`pnpm test`（在 `packages/api/` 下）

## 边界

- 不要改 `src/generated/`

此文件为 packages/api/AGENTS.md。事实变更时加载 using-agentsmd 及时修改。
```

**为何有效：** 不重复根文件的 commit 规范或全仓路由；署名用嵌套文件路径。

---

## 标签：末尾维护署名

```markdown
此文件为 AGENTS.md。事实变更时加载 using-agentsmd 及时修改。
```

`CLAUDE.md` 则将路径换成 `CLAUDE.md`；嵌套文件用该文件路径（如 `packages/api/AGENTS.md`）。

**为何有效：** 署名对应当前文件；维护提醒指向正确技能；一行、无历史堆砌。
