# Skill 规范定义

> 版本：1.0
> 日期：2026-05-17
> 状态：详细设计
> 关联决策：本文档定义 SKILL.md 的标准格式规范，是 AI-Native SDLC 框架中所有 Skill 的结构基础

---

## 一、设计目标

定义 AI-Native SDLC 框架中 SKILL.md 的标准结构，使每个 Skill 都具备：

1. **自描述性**：AI 加载后能立即理解角色定位、行为边界和执行步骤
2. **可组合性**：Skill 之间可以串联和嵌套，不影响各自的独立性
3. **可审查性**：人能快速判断一个 Skill 的覆盖范围和质量
4. **可演进性**：Skill 可以独立迭代，不需要同时修改其他 Skill

### 竞品参考与差异化

| 项目 | Skill 格式特点 | 我们取其什么 |
|------|--------------|------------|
| **gstack** | 自由格式 Markdown，无固定章节 | 无固定模板的灵活性 |
| **superpowers** | 硬编码流程 + HARD-GATE 指令 | 门控指令的强制性 |
| **bmad** | 结构化模板（Brief/Discovery/Review 分文件） | 分层结构的清晰度 |
| **spec-kit** | 声明式命令 + 模板文件分离 | 命令与模板解耦 |
| **matt-pocock** | 极简 SKILL.md（角色 + 行为规则 + 输入输出） | 极简主义的可读性 |

**我们的差异化：** 定义一个适度结构化的标准——比 gstack 更规范，比 bmad 更轻量。核心原则是"必须章节保证一致性，可选章节适应灵活性"。

---

## 二、SKILL.md 标准结构

### 2.1 章节分层

一个标准的 SKILL.md 由以下章节组成：

| # | 章节名称 | 必选/可选 | 说明 |
|---|---------|----------|------|
| 1 | **元信息**（Metadata） | 必选 | Skill 的身份标识 |
| 2 | **角色定位**（Role） | 必选 | AI 加载后的身份定义 |
| 3 | **行为规范**（Behavior Rules） | 必选 | AI 必须遵守的硬性规则 |
| 4 | **输入定义**（Input） | 必选 | 接受什么输入、格式要求 |
| 5 | **输出定义**（Output） | 必选 | 产出什么、格式要求 |
| 6 | **工作流**（Workflow） | 必选 | 核心执行步骤 |
| 7 | **门控条件**（Gate） | 必选 | 阶段通过的标准 |
| 8 | **提示词**（Prompts） | 可选 | 核心场景的提示词片段 |
| 9 | **模板引用**（Templates） | 可选 | 引用的外部模板文件 |
| 10 | **示例**（Examples） | 可选 | 典型输入输出的示例 |
| 11 | **变更记录**（Changelog） | 可选 | Skill 版本演化记录 |

### 2.2 必选章节详解

#### 1. 元信息（Metadata）

**格式：** YAML Front Matter，位于文件最开头，用 `---` 包裹。

**必须包含的字段：**

```yaml
---
name: requirement-review          # Skill 唯一标识（kebab-case）
version: "1.0"                    # 语义化版本号
description: 需求评审 Skill...     # 一句话描述
stage: requirement-review         # 所属工作流阶段
modes: [definer]                  # 使用的 AI 模式
dependencies: []                  # 依赖的其他 Skill
---
```

**字段说明：**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 是 | kebab-case 唯一标识符 |
| `version` | string | 是 | 语义化版本（major.minor） |
| `description` | string | 是 | 一句话描述 Skill 做什么 |
| `stage` | string | 是 | 所属工作流阶段 |
| `modes` | string[] | 是 | 使用的 AI 模式列表 |
| `dependencies` | string[] | 否 | 依赖的其他 Skill 名称 |

#### 2. 角色定位（Role）

**格式：** `## Role` 标题下的一段 Markdown 文本。

**必须包含：**
- 角色名称（如"需求评审助手"）
- 核心职责（2-3 句话）
- AI 模式声明（如"Definer 模式"）
- **不做的事**（明确边界）

**格式要求：**

```markdown
## Role

你是【角色名称】，以【AI 模式】模式工作。

### 核心职责
- 职责 1
- 职责 2
- 职责 3

### 不做的事
- 禁止事项 1
- 禁止事项 2
```

**设计理由：** "不做的事"是竞品 gstack 和 superpowers 都强调的模式——明确告诉 AI 不做什么，比告诉它做什么同样重要。这避免了角色边界模糊导致的职责蔓延。

#### 3. 行为规范（Behavior Rules）

**格式：** `## Behavior Rules` 标题下的编号列表。

**必须包含：**
- 交互风格规则（如 Coach 风格、反谄媚规则）
- 内容状态标注规则（✅⚠️❌🔍 四种状态）
- 质量保证规则（如禁止猜测规则）
- 边界场景处理规则

**格式要求：**

```markdown
## Behavior Rules

### 交互风格
1. [具体规则 1]
2. [具体规则 2]

### 内容状态标注
对每个需求点，必须给出以下四种状态之一：
- ✅ 清晰：...
- ⚠️ 模糊：...
- ❌ 矛盾：...
- 🔍 缺失：...

### 质量保证
1. 禁止猜测规则：...

### 边界处理
1. [具体规则]
```

**设计理由：** 引用设计决策 Q4（Coach 风格 + 反谄媚规则）和 Q6（四层防护）。行为规范是 AI 提示词中最核心的部分，必须明确到可以直接执行的程度。

#### 4. 输入定义（Input）

**格式：** `## Input` 标题下的结构化描述。

**必须包含：**
- 接受的输入类型列表
- 每种输入类型的格式要求
- 最小输入要求（低于此要求拒绝处理或降级处理）

**格式要求：**

```markdown
## Input

### 接受的输入类型
| 类型 | 格式 | 示例 |
|------|------|------|
| 完整 PRD | Markdown 文档 | 链接或文件路径 |
| Issue | 文本 + 元数据 | Issue URL |
| 口头描述 | 自然语言 | "帮我加个导出功能" |

### 最小输入要求
- [最低要求描述]
```

#### 5. 输出定义（Output）

**格式：** `## Output` 标题下的结构化描述。

**必须包含：**
- 产出的文件类型和路径
- 产出的结构要求（引用模板）
- 产出质量标准

**格式要求：**

```markdown
## Output

### 产出文件
- 文件路径：`.agents/active/{task-name}/requirement-spec.md`
- 文件格式：Markdown（符合 Spec 模板）

### 产出结构
严格遵循 `Requirement Review Spec` 模板（参见 Templates 章节）。

### 质量标准
1. 所有必填字段完整
2. 无 TBD/TODO/待定/?? 占位符
3. 所有验收标准可证伪（Pass/Fail）
```

#### 6. 工作流（Workflow）

**格式：** `## Workflow` 标题下的步骤序列。

**必须包含：**
- 按顺序排列的步骤列表
- 每个步骤的：输入、处理逻辑、输出、失败处理

**格式要求：**

```markdown
## Workflow

### Step 0: 输入质量评估
- **输入**：用户的原始需求输入
- **处理**：评估输入质量，推荐路径
- **输出**：质量等级 + 推荐路径
- **失败**：输入为空时要求补充

### Step 1: Context Gathering
...

（每个 Step 按相同格式展开）
```

**设计理由：** 引用设计决策 Q1（混合自适应模式）和 Q5（AI 自动判断输入质量 + 推荐路径）。工作流章节是 Skill 的执行骨架。

#### 7. 门控条件（Gate）

**格式：** `## Gate` 标题下的检查列表。

**必须包含：**
- 检查项列表（与设计决策 Q3 的 6 项自审核对应）
- 通过标准
- 失败处理方式

**格式要求：**

```markdown
## Gate

### 自审核检查
- [ ] 问题定义清晰
- [ ] 范围已界定
- [ ] 验收标准已列出
- [ ] 关键术语已定义
- [ ] 无 placeholder/TBD 残留
- [ ] 内部一致性（Spec 各章节之间无矛盾）

### 通过标准
所有检查项通过 + Developer 确认。

### 失败处理
标记未通过的检查项，返回对应步骤修复。
```

### 2.3 可选章节详解

#### 8. 提示词（Prompts）

**用途：** 存放核心场景的 AI 提示词片段。如果提示词较短（< 200 字），直接写在 Skill 中；如果较长，引用外部文件。

**格式要求：**

```markdown
## Prompts

### 系统提示词
> 你是需求评审助手，以 Definer 模式工作...

### 输入质量评估
> 评估以下输入的质量等级...

（或引用外部文件）
### 详细提示词
参见 `prompts/requirement-review.md`
```

#### 9. 模板引用（Templates）

**用途：** 列出 Skill 使用的外部模板文件。

**格式要求：**

```markdown
## Templates

| 模板文件 | 用途 | 路径 |
|---------|------|------|
| Requirement Spec 模板 | Spec 产出格式 | `templates/requirement-spec.md` |
| 澄清记录模板 | Q&A 记录格式 | 内嵌于 Spec 模板 |
```

#### 10. 示例（Examples）

**用途：** 提供 1-2 个典型场景的输入输出示例，帮助理解 Skill 的行为。

**格式要求：**

```markdown
## Examples

### 示例 1：完整 PRD 输入
**输入：** PRD 文档链接
**处理：** 批量处理 + NEEDS CLARIFICATION 标记
**输出：** 完整的 Requirement Spec

### 示例 2：口头描述输入
**输入：** "帮我加个导出功能"
**处理：** 一次一问，逐步澄清
**输出：** 结构化的 Requirement Spec
```

#### 11. 变更记录（Changelog）

**用途：** 记录 Skill 版本的演化历史。

**格式要求：**

```markdown
## Changelog

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| 1.0 | 2026-05-17 | 初始版本 |
```

---

## 三、SKILL.md 骨架模板

以下是一个空的 SKILL.md 骨架，可直接复制使用：

```markdown
---
name: skill-name
version: "1.0"
description: 一句话描述这个 Skill 做什么
stage: [requirement-review | plan | execute | review]
modes: [definer | builder | verifier]
dependencies: []
---

# Skill: [Skill 名称]

## Role

你是【角色名称】，以【AI 模式】模式工作。

### 核心职责
- 

### 不做的事
- 

## Behavior Rules

### 交互风格
1. 

### 内容状态标注
- ✅ 清晰：理解到位，直接写入 Spec
- ⚠️ 模糊：理解不完全，标记 [NEEDS CLARIFICATION] + 具体缺什么
- ❌ 矛盾：和已有信息冲突，指出冲突点 + 要求澄清
- 🔍 缺失：完全没有信息，提出问题 + 建议默认值

### 质量保证
1. 

### 边界处理
1. 

## Input

### 接受的输入类型
| 类型 | 格式 | 示例 |
|------|------|------|
|  |  |  |

### 最小输入要求
- 

## Output

### 产出文件
- 文件路径：
- 文件格式：

### 产出结构
遵循 [模板名称] 模板。

### 质量标准
1. 

## Workflow

### Step 0: [步骤名称]
- **输入**：
- **处理**：
- **输出**：
- **失败**：

### Step 1: [步骤名称]
- **输入**：
- **处理**：
- **输出**：
- **失败**：

（继续添加步骤...）

## Gate

### 自审核检查
- [ ] 

### 通过标准
所有检查项通过 + Developer 确认。

### 失败处理
标记未通过的检查项，返回对应步骤修复。

---

## Prompts（可选）

### [场景名称]
> 提示词内容...

## Templates（可选）

| 模板文件 | 用途 | 路径 |
|---------|------|------|
|  |  |  |

## Examples（可选）

### 示例 1：[场景名称]
**输入**：
**处理**：
**输出**：

## Changelog（可选）

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| 1.0 | YYYY-MM-DD | 初始版本 |
```

---

## 四、格式规则

### 4.1 通用格式

| 规则 | 说明 |
|------|------|
| 编码 | UTF-8 |
| 格式 | Markdown |
| 标题层级 | 从 `#` 开始（文档标题），子章节用 `##`，子项用 `###` |
| 列表 | 用 `- ` 无序列表或 `1.` 有序列表 |
| 表格 | 标准 Markdown 表格（`\| ... \|`） |
| 引用 | 提示词内容用 `>` blockquote |
| 代码 | 路径和代码片段用反引号 |
| YAML | 元信息用 YAML Front Matter（`---` 包裹） |

### 4.2 命名规范

| 对象 | 规范 | 示例 |
|------|------|------|
| Skill 文件名 | kebab-case，以 `-review`/`-plan`/`-execute` 等后缀区分阶段 | `requirement-review.md` |
| Skill name 字段 | 与文件名一致 | `requirement-review` |
| 模板文件名 | kebab-case | `requirement-spec-template.md` |
| 产出文件名 | kebab-case + 描述性名称 | `requirement-spec.md` |
| 步骤名称 | 动词开头 | `Context Gathering`、`Spec Generation` |

### 4.3 文件组织

```
.agents/
├── workflows/
│   └── requirement-review/
│       ├── SKILL.md                          # Skill 定义文件
│       ├── prompts/
│       │   ├── system.md                     # 系统提示词
│       │   ├── input-assessment.md           # 输入质量评估提示词
│       │   ├── requirement-understanding.md  # 需求理解提示词
│       │   ├── spec-generation.md            # Spec 生成提示词
│       │   └── gate-check.md                 # 门控检查提示词
│       └── templates/
│           └── requirement-spec.md           # Spec 模板
```

---

## 五、质量检查清单

一个合格的 SKILL.md 必须通过以下检查：

- [ ] 所有 7 个必选章节完整
- [ ] 元信息 YAML 格式正确
- [ ] 角色定位包含"不做的事"
- [ ] 行为规范包含四种状态标注规则
- [ ] 输入定义包含最小输入要求
- [ ] 输出定义引用了模板
- [ ] 工作流步骤有明确的输入/输出/失败处理
- [ ] 门控条件可验证（不是"检查质量"这种模糊描述）
- [ ] 文件名符合 kebab-case 规范
- [ ] 无 placeholder/TBD 残留

---

## 六、与其他 Skill 的关系

本规范定义的格式是 AI-Native SDLC 框架中所有 Skill 的通用格式。当前已规划的 Skill：

| Skill | 阶段 | AI 模式 | 状态 |
|-------|------|---------|------|
| `requirement-review` | Requirement Review | Definer | 设计中 |
| `plan` | Plan | Builder | 待设计 |
| `execute` | Execute | Builder | 待设计 |
| `review` | Review | Verifier | 待设计 |

每个 Skill 都遵循本规范定义的标准结构，确保风格一致、可组合、可独立迭代。
