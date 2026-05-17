# AI-Native SDLC — 安装指南

> 版本：1.0 | 适用工具：Cursor / Claude Code / Windsurf

本指南帮助你在 30 分钟内完成 AI-Native SDLC 框架的安装，并完成一次 Hello World 验证。

---

## 前置条件

在开始之前，请确认：

- [ ] 已安装并配置好 AI 编码工具（Cursor / Claude Code / Windsurf 其中之一）
- [ ] 已克隆本仓库到本地，或下载了 `skills/` 目录下的所有文件
- [ ] 已有一个要使用本框架的客户项目目录

---

## 安装方式一：Cursor

### 步骤 1：找到 Rules 目录

Cursor 支持项目级 Rules 文件（`.cursorrules`）或用户级 Rules（Settings → Rules for AI）。

推荐使用**项目级 Rules**，这样规则跟随项目仓库管理：

```bash
# 在你的客户项目根目录下创建 Rules 文件
touch .cursorrules
```

### 步骤 2：加载 Skill 内容

将以下 4 个 Skill 文件的内容复制到 `.cursorrules`：

```
skills/requirement-review/SKILL.md
skills/plan/SKILL.md
skills/execute/SKILL.md
skills/review/SKILL.md
```

**推荐格式：**

```markdown
# AI-Native SDLC Framework Rules

[在此粘贴 requirement-review/SKILL.md 的完整内容]

---

[在此粘贴 plan/SKILL.md 的完整内容]

---

[在此粘贴 execute/SKILL.md 的完整内容]

---

[在此粘贴 review/SKILL.md 的完整内容]
```

### 步骤 3：验证加载

在 Cursor 的 Chat 面板输入：

```
你目前加载了哪些 Skill？请列出你知道的 4 个阶段。
```

AI 应该能回答出：Requirement Review、Plan、Execute、Review 四个阶段。若回答不正确，检查 `.cursorrules` 文件格式。

---

## 安装方式二：Claude Code

### 步骤 1：创建 CLAUDE.md 文件

Claude Code 通过项目根目录的 `CLAUDE.md` 加载上下文：

```bash
# 在你的客户项目根目录下创建
touch CLAUDE.md
```

### 步骤 2：加载 Skill 内容

将 4 个 Skill 文件内容写入 `CLAUDE.md`，格式同 Cursor 的推荐格式（见上方）。

### 步骤 3：配置 Slash Commands（可选）

在项目根目录创建 `.claude/` 文件夹，为每个 Skill 创建对应的命令文件：

```bash
mkdir -p .claude/commands
```

```bash
# .claude/commands/requirement-review.md
# 内容：requirement-review/SKILL.md 的完整内容

# .claude/commands/plan.md
# .claude/commands/execute.md
# .claude/commands/review.md
```

配置后可通过 `/requirement-review`、`/plan`、`/execute`、`/review` 直接触发对应 Skill。

### 步骤 4：验证加载

在 Claude Code 对话框输入：

```
你目前加载了哪些 AI-Native SDLC 阶段？
```

---

## 安装方式三：Windsurf

### 步骤 1：创建全局规则或项目规则

Windsurf 支持：
- **全局规则**：Settings → AI → Custom Rules
- **项目规则**：项目根目录的 `.windsurfrules` 文件

推荐使用**项目规则**（`.windsurfrules`）：

```bash
touch .windsurfrules
```

### 步骤 2：加载 Skill 内容

将 4 个 Skill 文件内容写入 `.windsurfrules`，格式同 Cursor 推荐格式。

### 步骤 3：验证加载

在 Windsurf Cascade 面板输入：

```
请描述你知道的 AI-Native SDLC 的 4 个阶段。
```

---

## 项目初始化

安装完 Skill 后，在**每个新项目**开始前执行以下初始化：

### 步骤 1：创建 CONTEXT.md

`CONTEXT.md` 是项目的领域词汇表，放在客户项目根目录。

你可以：
- **方式 A**：由 Requirement Review Skill 在首次需求评审时自动创建（推荐）
- **方式 B**：手动创建，参照 `skills/requirement-review/CONTEXT-FORMAT.md` 的格式

```bash
# 手动创建时
touch CONTEXT.md
# 然后按 CONTEXT-FORMAT.md 的格式填写
```

### 步骤 2：创建 specs/ 目录

```bash
mkdir -p specs/
```

### 安装完成检查清单

```
[ ] AI 工具已加载 4 个 Skill
[ ] 客户项目根目录存在（或已计划创建）CONTEXT.md
[ ] 客户项目根目录存在 specs/ 目录
```

---

## 第一次使用（Hello World）

完成安装后，用以下步骤做一次端到端验证：

### 触发 Requirement Review

在 AI 工具中输入：

```
我想实现一个功能：用户可以通过邮箱和密码登录系统。
请开始需求评审（Requirement Review）阶段。
```

**预期行为：**
1. AI 读取 CONTEXT.md（若存在）
2. AI 评估输入的完整性，针对缺失项提问
3. AI 产出 Original Request vs Reframed Understanding 对比
4. AI 给出 Scope Decision
5. AI 起草 Requirement Spec 草稿，并在写每条 FR 时触发相关的澄清问题
6. AI 执行 Gate 自审核，完成后请求 Developer 签署

### 首次运行检查清单

```
[ ] AI 询问了完整性检查问题（目标用户/问题/成功标准/约束之一）
[ ] AI 产出了 Original Request vs Reframed Understanding 的对比内容
[ ] AI 给出了四选一的 Scope Decision 并附理由
[ ] AI 在起草 FR 时至少问了一个边界条件或澄清问题
[ ] AI 在输出前执行了 Gate 自审核
```

---

## 故障排查

### 问题 1：AI 不知道 Skill 是什么

**症状：** 输入"开始需求评审"后，AI 像普通对话一样回答，没有按 Skill 流程执行。

**解决：**
- 检查 Rules 文件是否正确保存
- 确认 AI 工具已重启或重新加载规则
- 尝试明确说「按 requirement-review Skill 的 Execution Flow 执行」

### 问题 2：AI 跳过了某些步骤

**症状：** AI 直接输出了 Requirement Spec，没有先做 Problem Reframing 或 Scope Decision。

**解决：**
- 明确提示：「请按 Requirement Review Skill 的 7 个步骤顺序执行，不要跳步」
- 检查 Skill 文件是否完整加载（部分工具有字符数限制）

### 问题 3：模板文件无法按需加载

**症状：** AI 说找不到 `templates/requirement-spec.md`。

**解决：**
- 确认模板文件放在了正确路径：`skills/requirement-review/templates/requirement-spec.md`
- 在提示中直接提供模板内容：「使用以下模板格式起草 Spec：[粘贴模板内容]」

### 问题 4：CONTEXT.md 未被正确加载

**症状：** AI 使用了 Avoid 词汇，但没有触发 `❌ 矛盾` 警告。

**解决：**
- 确认 `CONTEXT.md` 在客户**项目根目录**（不是框架仓库）
- 明确提示：「请先读取项目根目录的 CONTEXT.md」
- 检查 CONTEXT.md 的 Avoid 词汇格式是否正确（参照 CONTEXT-FORMAT.md）

### 问题 5：Gate Checklist 被跳过

**症状：** AI 直接输出了 Spec，没有执行 Gate 自审核。

**解决：**
- 明确提示：「在输出 Spec 前，请先执行 Gate Checklist 的逐项自审核」
- 可以在项目的 Rules 文件末尾添加全局提醒：「任何阶段完成前必须执行对应的 Gate Checklist」

### 问题 6：上下文长度超限导致 Skill 行为退化

**症状：** 长对话后，AI 开始忽略 Skill 规则，行为变得随意。

**解决：**
- 开启新对话，重新加载 Skill
- 在新对话开始时告知：「我们正在进行 [功能名] 的 [阶段名] 阶段，请加载对应 Skill」
- 将 Spec 文件的路径告诉 AI，让它重新读取上下文

---

## 注意事项

- 每个阶段完成后，Spec 文件由 Developer 签署后才能进入下一阶段
- `CONTEXT.md` 是项目特有的词汇表，不同项目之间不共享
- Skill 文件本身不需要放在客户项目中，只需加载到 AI 工具的 Rules/Prompt 中
- 遇到 AI 行为不符合预期时，优先检查是否有上下文超限的问题
