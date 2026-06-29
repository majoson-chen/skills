# 反例

---

## 标签：目录堆砌（检索型）

```markdown
## 项目结构

src/
  components/
    Button.tsx
    Modal.tsx
  hooks/
  utils/
  …（30 行）
```

**问题：** 为检索而写；占满上下文；Agent 自己会 `ls`。

---

## 标签：Skill 全文复制

```markdown
## 提交流程

1. 先跑 git status…
（粘贴 commit 技能 80 行）
```

**问题：** 应写 `加载 skills/commit/SKILL.md` 一行路由。

---

## 标签：对比 priming

```markdown
- 用 pnpm --filter <pkg>，不要在根目录盲跑
```

**问题：** 「根目录盲跑」反而种下多余分支。应写：`跑包内任务：pnpm --filter <pkg> <script>`。

---

## 标签：通用废话

```markdown
## 代码规范

请遵循最佳实践，保持代码整洁，编写可维护的代码。
```

**问题：** 无仓库信息；模型已知。

---

## 标签：删了又解释

```markdown
## 测试

<!-- 已删除 npm test，现改用 pnpm -->

- 全量测试：`pnpm test`
```

**问题：** 流水账；删了不必说明。

---

## 标签：凑模板空章

```markdown
## 安全考虑

（无内容）

## 部署

TBD
```

**问题：** 没内容就不写该节。

---

## 标签：嵌套抄父文件

```markdown
# API 包

## Git

- 无用户要求不要 commit
- 提交：加载 skills/commit/SKILL.md

## 命令

- 本包测试：`pnpm test`
```

**问题：** commit 规范已在根 `AGENTS.md`；嵌套只写子树增量，不重复父文件。

---

## 标签：署名写技能名

```markdown
<important-notes>
此文件来自 using-agentsmd。事实变更时请更新。
</important-notes>
```

**问题：** 块内应写文件路径（如 `packages/api/AGENTS.md`），不是技能名。

---

## 标签：未用 important-notes

```markdown
此文件为 `AGENTS.md`。事实变更时加载 `using-agentsmd` 及时修改。
```

**问题：** 裸文本优先级低；须用 `<important-notes>` 统一包裹。
