# using-agentsmd 实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development（推荐）或 superpowers:executing-plans 按任务逐步执行。步骤使用 checkbox（`- [ ]`）跟踪。

**Goal:** 在 `skills/using-agentsmd/` 发布中文首版技能：教 Agent 在创建/维护 `AGENTS.md` 时建立正确心智模型，并以精炼 prompt 式正文 + 分离正反例落地。

**Architecture:** 遵循 Agent Skills 三层加载——`description` 管触发（仅创建/修改场景，略偏主动以防 undertrigger）；`SKILL.md` 正文承载判断流与编写纪律（目标 ≤150 行）；`references/` 按需加载示例。技能正文中文；不单独做英文版除非用户后续要求。

**Tech Stack:** Agent Skills 规范、`skill-creator` 的 `quick_validate.py`、本仓库 `readme.md` 表格。

**Spec 来源:** `docs/superpowers/specs/2026-06-29-using-agentsmd-design.md`

---

## 文件清单

| 文件 | 职责 |
|------|------|
| `skills/using-agentsmd/SKILL.md` | 触发后必读：Hard gate、心智模型、边界、工作流 |
| `skills/using-agentsmd/references/good-examples.md` | 正例（短、带标签） |
| `skills/using-agentsmd/references/bad-examples.md` | 反例（短、带标签） |
| `readme.md` | 技能目录表新增一行 |
| `docs/superpowers/specs/2026-06-29-using-agentsmd-design.md` | 状态改为「已实现」并链到计划 |

**不创建:** `scripts/`、`README.md`（技能足够轻，YAGNI）

---

## Skill 机制要点（执行前必读）

1. **description（frontmatter）** — 始终在技能列表元数据中；决定**是否加载**本技能。所有「何时用」写在这里，不写进正文重复。
2. **SKILL.md body** — 加载后进入上下文；保持精炼，避免与 description 重复长段触发说明。
3. **references/** — 正文用一句话指向；写/审计示例时再读，不默认全量加载。
4. **验证** — `python .agents/skills/skill-creator/scripts/quick_validate.py skills/using-agentsmd`

---

### Task 1: 创建目录

**Files:**
- Create: `skills/using-agentsmd/`
- Create: `skills/using-agentsmd/references/`

- [ ] **Step 1: 创建目录**

```bash
mkdir -p skills/using-agentsmd/references
```

---

### Task 2: 写入 SKILL.md（中文首版）

**Files:**
- Create: `skills/using-agentsmd/SKILL.md`

- [ ] **Step 2: 创建文件，内容如下（逐字使用，审阅后再改）**

```markdown
---
name: using-agentsmd
description: 创建或维护 AGENTS.md 时必须先加载。适用于用户要求写、改、审计、init 生成 AGENTS.md，或你即将 create、edit、append 任何根/嵌套 AGENTS.md。日常仅遵循已注入的 AGENTS.md 干活时不加载。涵盖 prompt 式编写、红绿线、归属与路由、嵌套、审计删减。不用于一般编码任务。
---

# using-agentsmd

在动手创建或修改任何 `AGENTS.md` 之前，遵循本技能。

## Hard gate

1. 即将 `create` / `edit` / `append` 根或嵌套 `AGENTS.md` → **必须先加载本技能**
2. 无用户提示不主动改；要改须用户明确要求，或先征得许可
3. 用户说「写进 AGENTS.md」→ 先做归属判断（见下）；用户坚持后再写入
4. 日常按已注入的 `AGENTS.md` 执行其他任务 → **不加载本技能**

## 是什么

`AGENTS.md` = 仓库级、被动注入的**高信号 prompt 片段**。

- 写好可：少犯错、走快路、少重复沟通
- 不是：给人看的文档、百科全书、变更日志、Skill/Rule/README 的副本

## 提示词纪律

按 **写 prompt** 的标准写，不按写 README 的标准写。

- **肯定式**：说做什么、用什么；不列举未发生的错误路径
- ❌ `用 pnpm --filter <pkg>，不要根目录盲跑` → ✅ `跑包内任务：pnpm --filter <pkg> <script>`
- 一条一动作；命令与路径写全
- **只写限制和做法，不写理由**
- 无元叙事（「本节说明…」）、无人话 filler

**红线**（不要 / 禁止）：仅当该误行高频且不说就会犯。否则不提。

**绿线**（优先 / 用 X / 放这里）：本仓最高效、最正确的做法。

## 写什么 / 不写什么

| 写 | 不写 |
|----|------|
| 2–4 句定向（是什么仓、边界） | 长篇背景、愿景、市场定位 |
| 关键节点目录（浅层，为**摆放文件**） | 深层目录树、逐文件说明 |
| 非显而易见的完整命令 | manifest/README 已有的命令 |
| 绿线、必要红线 | 通用最佳实践、user rules 复述 |
| 路由指针（Skill / docs / 嵌套 AGENTS.md） | 工作流全文、架构/API 长文 |

**判断：** 换一个人、换一个 Agent 工具，这条仍应成立吗？否 → 不进 `AGENTS.md`（单人维护仓可务实处理个人偏好）。

**引用 ≠ 复制：** 链到 Skill/docs，禁止把正文抄进来。工作流须在 `AGENTS.md` 留路由，否则不会自动触发。

## 与其他载体

| 载体 | 放什么 |
|------|--------|
| `AGENTS.md` | 跨工具常驻：定向、命令、红绿线、路由 |
| Skills | 完整工作流 |
| `.cursor/rules` | Cursor 专属、可按 glob 生效 |
| `docs/` | 深度材料；`AGENTS.md` 只链接 |

同一事实只维护一处。

## 嵌套与体积

- 根文件：全仓约定
- 嵌套 `AGENTS.md`：子树**增量**；不抄父文件；善于主动拆分
- 无固定章节模板；没内容不凑章
- 「极短」是纪律（信号密度），不是行数 KPI；真写长了应嵌套或迁出
- 维护时优先改原段落，不轻易加新一级标题

## 工作流

**新建 / init：** 搜集仓库事实（package.json、CI、目录）→ 对话对齐（有 brainstorming 插件则用）→ 再落笔；禁止一键吐全文。

**归属判断（写入前）：**

```
这条信息属于哪里？
  → Skill 工作流？     写路由，不改 AGENTS.md 正文
  → docs / Rule？      链过去
  → 子树特有？         嵌套 AGENTS.md
  → 全仓、非可推断？   写入根 AGENTS.md
```

**编写：** 对照仓库写真事实；不猜、不抄别仓。

**审计：** 编辑器，不是扩写器——先删重复/过时/废话 → 再迁错放内容 → 最后才补。删就是删，不附「此处已删除…」。不当流水账。

**执行已有 AGENTS.md（其他任务）：** 直接照做；不为校验再读一遍。

## 正反例

需要对照时读取：

- `references/good-examples.md`
- `references/bad-examples.md`
```

- [ ] **Step 3: 验证行数**

```bash
wc -l skills/using-agentsmd/SKILL.md
```

Expected: 正文约 100–120 行（含 frontmatter），显著低于 500 行上限。

---

### Task 3: 写入 good-examples.md

**Files:**
- Create: `skills/using-agentsmd/references/good-examples.md`

- [ ] **Step 4: 创建文件，内容如下**

```markdown
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
```

**为何有效：** 不重复根文件的 commit 规范或全仓路由。
```

---

### Task 4: 写入 bad-examples.md

**Files:**
- Create: `skills/using-agentsmd/references/bad-examples.md`

- [ ] **Step 5: 创建文件，内容如下**

```markdown
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
```

---

### Task 5: 更新 readme.md

**Files:**
- Modify: `readme.md`

- [ ] **Step 6: 在 Skills 表格追加一行（位于 openclaw-plugin-dev 之后）**

```markdown
| **using-agentsmd** | [`using-agentsmd`](skills/using-agentsmd) | 创建/维护 AGENTS.md：prompt 式编写、红绿线、归属路由、嵌套、审计删减。仅写改时加载。 |
```

---

### Task 6: 验证与人工抽检

**Files:**
- Test: `skills/using-agentsmd/SKILL.md`

- [ ] **Step 7: 运行 quick_validate**

```bash
python3 .agents/skills/skill-creator/scripts/quick_validate.py skills/using-agentsmd
```

Expected: `Skill is valid!`

- [ ] **Step 8: 人工抽检（无需自动化测试）**

用以下 3 个场景在脑中或实际对话走读技能是否给出正确行为：

| 场景 | 期望 |
|------|------|
| 用户：「帮我把这个功能实现了」 | 不加载 using-agentsmd |
| 用户：「帮我写 AGENTS.md」 | 加载；先对齐再写；肯定式；不吐 overview |
| 用户：「把 commit 流程贴进 AGENTS.md」 | 加载；建议路由到 skills/commit；用户坚持再写 |

- [ ] **Step 9: 更新 spec 状态**

在 `docs/superpowers/specs/2026-06-29-using-agentsmd-design.md` 顶部改为：

```markdown
> 状态：已实现。计划：`docs/superpowers/plans/2026-06-29-using-agentsmd.md`
```

---

### Task 7: 提交（仅当用户要求时）

- [ ] **Step 10: 不主动 commit**（仓库惯例：用户明确要求后再提交）

---

## Spec 覆盖自检

| Spec 章节 | 计划任务 |
|-----------|----------|
| 触发：仅创建/修改 Hard gate | Task 2 description + Hard gate 节 |
| prompt 式 / 红绿线 | Task 2 + Task 4 反例 |
| 写什么/不写什么、分层 | Task 2 |
| 嵌套、体积纪律 | Task 2 |
| 工作流、归属、审计 | Task 2 |
| 正反例 | Task 3、4 |
| readme 集成 | Task 5 |
| 不写安全专题、注入行为 | 刻意省略 ✓ |
| 中文首版 | Task 2–4 ✓ |

## 占位符扫描

无 TBD/TODO/「稍后补充」。

---

## 执行方式

计划已保存。可选：

1. **Subagent-Driven（推荐）** — 每任务派生子代理，任务间 review  
2. **Inline Execution** — 本会话按 Task 1→7 连续执行，检查点处请你审阅 `SKILL.md`

你选哪种？
