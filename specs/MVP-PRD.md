# MVP PRD：AI-Native SDLC 规范驱动开发引擎 v1.0

> 版本：v1.0-mvp
> 日期：2026-05-17
> 状态：待 to-issues 拆分
> 父文档：`specs/AI-Native-SDLC-Tool-PRD.md`

---

## 一、MVP 目标

**交付 4 个可加载到主流 AI Coding 工具的 SKILL.md 文件**，覆盖开发生命周期的 4 个核心阶段（Requirement Review → Plan → Execute → Review），使 FDE 能在客户现场将其安装进 Cursor / Claude Code / Windsurf 等工具，让 Developer 通过结构化的规范驱动流程完成一个完整功能的开发。

**MVP 不是一个应用程序**，它是一套 Markdown 规范包 + 配套文件模板。

---

## 二、MVP 范围

### In Scope

| 交付物 | 说明 |
|---|---|
| `requirement-review.skill.md` | 需求评审阶段完整执行规范 |
| `plan.skill.md` | 方案设计阶段完整执行规范 |
| `execute.skill.md` | 开发执行阶段完整执行规范 |
| `review.skill.md` | 评审阶段完整执行规范 |
| `templates/requirement-spec.md` | Requirement Spec 模板（含字段定义） |
| `templates/plan-spec.md` | Plan Spec 模板（含字段定义） |
| `templates/review-report.md` | Review Report 模板（含字段定义） |
| `templates/CONTEXT.md` | 项目统一语言词汇表模板 |
| 安装指南 | 覆盖 Cursor / Claude Code / Windsurf 三个宿主工具 |

### Out of Scope（MVP 明确不做）

- 独立的 Web UI 或 CLI 工具
- 技术设计（ADR）阶段独立化
- 独立测试阶段 / 上线 preflight
- 多模式输入路由（Coaching / Mixed / Batch / Fast）
- Constitution 宪法机制
- HITL/AFK 切片自动分类引擎
- 跨工具 API 集成

---

## 三、核心设计原则（MVP 必须遵守）

1. **人主控，AI 辅助**：所有阶段切换由 Developer 触发，门控必须经过 Developer 签署
2. **Spec 是唯一传递载体**：阶段间只通过 Spec 文件传递信息，禁止口头描述跨阶段
3. **统一语言**：每个 Skill 执行前必须加载 `CONTEXT.md`，术语偏差立即拦截
4. **宽进严出**：任何格式的输入均可接受，输出必须通过门控
5. **证据先于声明**：AI 不得使用"应该能行"等推测语言，完成声明必须附证据
6. **目标先于任务**：每个切片必须先有可验证目标，再列实现任务

---

## 四、4 个 Skill 的详细规格

### 4.1 SKILL.md 通用章节结构

每个 SKILL.md **必须包含以下 6 个章节**，缺少任何一个视为不合规。

**渐进式加载原则**：Spec 模板不内嵌在 Skill 文件中，而是通过 YAML frontmatter 的 `template_ref` 字段声明外部路径，仅在执行到"起草 Spec"步骤时才加载——避免每次 Skill 初始化时把模板内容占满上下文窗口。

```
---
name: <skill-name>
version: "1.0.0"
description: <一句话描述>
input:
  required:
    - <文件路径或文件类型>
  optional:
    - <文件路径或文件类型>
output:
  produces:
    - <文件路径>
dependencies: []
template_ref: templates/<phase>-spec.md  # 起草 Spec 时按需加载，不在 Skill 初始化时加载
---

## 1. Persona（角色定义）
## 2. Context Loading（上下文加载顺序）
## 3. Execution Flow（执行流程，分步骤）
## 4. Gate Checklist（门控自审核清单，Developer 签署前必须全部通过）
## 5. Anti-Patterns（禁止行为清单）
## 6. Template Reference（声明本阶段 Spec 模板的外部文件路径；模板内容不内嵌于 Skill，执行到"起草 Spec"步骤时才按需加载）
```

---

### 4.2 Requirement Review Skill 规格

**文件**：`requirement-review.skill.md`

**Persona**：需求分析师。严格区分"用户说的"和"用户真正需要的"。拒绝把输入原样转写为 Spec 的表演性认同。任何不确定项必须显式标注 `[NEEDS CLARIFICATION: 影响什么决策]`。

**输入契约**：
- Required：任意形式的需求描述（口头/Issue/PRD 片段均可）
- Required：`CONTEXT.md`（若不存在则先引导 Developer 创建）

**输出契约**：
- Produces：`specs/requirement-spec-<feature-name>.md`

**执行流程**（7 步，顺序不可跳过）：

```
Step 1: 加载 CONTEXT.md
  → 读取 Domain Glossary 和 Always/Never 约束
  → 若文件不存在：引导 Developer 从 templates/CONTEXT.md 创建

Step 2: 接收输入
  → 接受任何格式的需求描述

Step 3: 输入完整性评估
  → 对照检查清单评估缺失项：
    [ ] 目标用户是谁？
    [ ] 要解决什么问题？
    [ ] 成功标准是什么？
    [ ] 约束条件是什么（技术/时间/预算）？
  → 仅对缺失项提问，已有信息不重复追问
  → 所有缺失项补齐后进入 Step 4

Step 4: Problem Reframing（问题重诠释）
  → 基于补全后的信息，AI 主动重新诠释"你真正要解决的问题"
  → 产出格式：
    Original Request: <用户字面表述>
    Reframed Understanding: <AI 重诠释的本质问题>
  → 可以挑战用户原始表述；若理解有误，Developer 纠正后重新执行此步

Step 5: Scope Decision（范围决策）
  → 基于 Reframed Understanding，输出四选一范围决策：
    Expansion：实际问题比描述的更大，需要扩展范围
    Selective Expansion：部分扩展，有理由不全扩
    Hold Scope：用户描述的范围是准确的，直接推进
    Reduction：用户描述过于宽泛，建议聚焦到核心
  → 必须附上决策理由
  → Developer 确认后进入 Step 6

Step 6: 起草 Requirement Spec
  → 按需加载模板：templates/requirement-spec.md
  → 此时才读取模板文件，Skill 初始化时不加载（节省上下文）
  → 每个 FR 必须有验收标准
  → 每个 NFR 必须有可测量指标
  → CONTEXT.md 中的 Avoid 词汇触发 ❌ 矛盾 标记
  → 不确定项标注 [NEEDS CLARIFICATION: 影响哪个决策]

Step 7: 门控自审核
  → 按 Gate Checklist 逐项检查
  → 全部通过后将 Checklist 输出给 Developer 审阅
  → Developer 签字确认后写入文件
```

**Gate Checklist**（6 项，全部必须通过）：
```
[ ] 无 TODO / TBD / ?? 等未填 Placeholder
[ ] 所有 FR 有具体验收标准（非"系统应该支持XX"这类模糊表述）
[ ] Scope Decision 已明确输出（四选一）
[ ] 无未解决的 [NEEDS CLARIFICATION] 标注
[ ] 使用的术语与 CONTEXT.md 一致，无 Avoid 词汇
[ ] Developer 已签署：_______ 日期：_______
```

**禁止行为（Anti-Patterns）**：
- ❌ 把用户输入原样复制为 FR，没有任何重新表述
- ❌ 跳过 Problem Reframing 直接起草 Spec
- ❌ 使用"系统应该支持 XX"等无法验证的 FR 措辞
- ❌ 遇到不确定内容编造答案而非标注 NEEDS CLARIFICATION
- ❌ 门控前置条件未全部满足就声明"完成"

---

### 4.3 Plan Skill 规格

**文件**：`plan.skill.md`

**Persona**：技术架构师。将需求转化为可执行的垂直切片计划。每个切片必须贯穿所有集成层。禁止水平分层（先写所有 schema → 再写所有 API）。

**输入契约**：
- Required：`specs/requirement-spec-<feature-name>.md`（status 必须为 approved）
- Required：`CONTEXT.md`
- Optional：已有代码库结构

**输出契约**：
- Produces：`specs/plan-spec-<feature-name>.md`

**前置依赖检查**（执行 Step 1 前）：
```
[ ] requirement-spec 文件存在（路径约定：specs/requirement-spec-<feature>.md）
    → 不满足：请提供 Requirement Spec 文件路径或内容
```

**执行流程**（5 步）：

```
Step 1: 加载上下文
  → 读取 CONTEXT.md、Requirement Spec

Step 2: 方案分析
  → 若存在重大技术 tradeoff，生成对比表：
    | 方案 | 接受了什么代价 | 放弃了什么 | 推翻条件 |
  → 不存在 tradeoff 则跳过此步

Step 3: 垂直切片拆分
  → 按 FR 拆分为端到端垂直切片
  → 每个切片必须包含：
    - Slice ID（S-001, S-002...）
    - 切片名称（简短动词短语）
    - 覆盖层级（schema / API / UI / tests 各写 Y/N）
    - 验收标准（可独立验证）
    - 测试计划（最少一条测试用例描述）
    - 执行模式（HITL = 需 Developer 介入 / AFK = 可无监督执行）
    - 依赖切片（哪些切片必须先完成）
  → 禁止创建纯横向切片（如"写完所有接口再写 UI"）

Step 4: 起草 Plan Spec
  → 按需加载模板：templates/plan-spec.md
  → 此时才读取模板文件，Skill 初始化时不加载（节省上下文）

Step 5: 门控自审核
  → 按 Gate Checklist 逐项检查
  → 全部通过后输出给 Developer
  → Developer 确认后写入文件
```

**Gate Checklist**（6 项）：
```
[ ] 每个切片覆盖所有层级（至少 schema + API + tests，UI 按需）
[ ] 每个切片有独立可验证的验收标准
[ ] 每个切片有测试计划
[ ] 无未解决的 [NEEDS CLARIFICATION]
[ ] 无 TODO / TBD / ??
[ ] Developer 已签署：_______ 日期：_______
```

**禁止行为（Anti-Patterns）**：
- ❌ 创建"写完所有 DB schema"这类纯横向切片
- ❌ 验收标准写"功能正常"而非具体可验证的条件
- ❌ 切片无测试计划
- ❌ 未评估 Requirement Spec 中 NFR 对方案的影响

---
### 4.4 Execute Skill 规格

**文件**：`execute.skill.md`

**Persona**：严格的实现工程师。逐切片推进，不跳切片，不提前实现下一切片的功能。发现 bug 走四阶段调试协议，3 次修复无效必须停止并质疑架构。完成声明必须附运行证据。

**输入契约**：
- Required：`specs/plan-spec-<feature-name>.md`（status 必须为 approved）
- Required：`CONTEXT.md`
- Optional：相关源码文件、错误日志

**输出契约**：
- Produces：实现代码（按切片提交）

**前置依赖检查（Pre-Execute Readiness Gate）**，进入执行前强制扫描：
```
[ ] plan-spec 文件存在（路径约定：specs/plan-spec-<feature>.md）
[ ] Plan Spec 中所有切片都有验收标准（无"待定"项）
[ ] Plan Spec 中无未解决的 [NEEDS CLARIFICATION]
[ ] Plan Spec 中所有切片都有测试计划
→ 任意一项不满足：阻断，列出具体缺失项，提示 Developer 先修复 Plan Spec
```

**执行流程**（每切片循环）：

```
For each slice（按依赖顺序）:

  Step 1: 宣布当前切片
    → 输出：「开始执行切片 S-XXX：<切片名称>」

  Step 2: 确认理解
    → 复述切片的验收标准和测试计划
    → HITL 切片：等待 Developer 确认后继续
    → AFK 切片：直接进入 Step 3

  Step 3: 实现
    → 按切片范围逐层实现，每层实现后运行对应测试并输出结果
    → 禁止实现当前切片 Scope 之外的功能

  Step 4: 遭遇 Bug 时的四阶段调试协议
    Phase 1 — Root Cause Investigation：禁止直接提 fix，先找根因，生成 3-5 个可证伪假设
    Phase 2 — Pattern Analysis：对比可工作代码找差异
    Phase 3 — Hypothesis Test：一次只测一个假设，输出命令和结果
    Phase 4 — Implementation：修根因，不修表面症状
    → 规则：连续 3 次修复无效必须停止，输出：
      「已尝试 3 次修复，建议质疑架构层假设，请 Developer 决策」

  Step 5: 切片完成验证
    → 运行所有测试，输出完整命令和结果
    → 对照验收标准逐条确认，每条附"通过/不通过 + 证据"

  Step 6: 完成声明
    → 询问：「是否继续执行下一切片？」
```

**Gate Checklist**（每切片，Developer 确认前）：
```
[ ] 所有验收标准逐条对照并附证据
[ ] 测试命令已运行，结果已展示
[ ] 未实现 Scope 外的功能
[ ] Developer 确认：_______ 日期：_______
```

**禁止行为（Anti-Patterns）**：
- ❌ 一次性实现多个切片
- ❌ 完成声明不附运行证据
- ❌ 使用"应该能行""看起来对"等推测性语言
- ❌ Bug 调试时直接猜 fix，不走四阶段协议
- ❌ 3 次修复无效后继续猜测而非停止上报

---

### 4.5 Review Skill 规格

**文件**：`review.skill.md`

**Persona**：独立评审员。出发点是"假设它是错的，找哪里错"。每个发现必须有 file:line 定位和严重级别标注。不受实现者意图影响。

**输入契约**：
- Required：`specs/requirement-spec-<feature-name>.md`
- Required：`specs/plan-spec-<feature-name>.md`
- Required：本次实现的代码变更（git diff 或文件列表）
- Required：`CONTEXT.md`

**输出契约**：
- Produces：`specs/review-report-<feature-name>.md`

**前置依赖检查**：
```
[ ] 确认 Plan Spec 中所有切片均已实现并验证完成
[ ] requirement-spec 和 plan-spec 文件均存在
→ 不满足：阻断，提示 Developer 先完成所有切片
```

**执行流程**（三轴独立审查）：

```
⚠️ 三轴审查必须独立执行：每轴重新加载上下文，不参考其他轴结论。

Axis 1 — 代码质量轴（独立上下文）：
  → 从代码本身出发（不参考 Requirement Spec）
  → 覆盖 5 个维度：正确性 / 可读性 / 架构 / 安全 / 性能
  → 每个发现：| 严重级别 | 文件:行号 | 描述 | 建议修复 |
  → Critical = 必须修复 / Important = 强烈建议 / Minor = 可选

Axis 2 — 需求一致性轴（独立上下文）：
  → 逐条 FR 对照代码实现
  → 格式：| FR-ID | 需求描述 | 实现状态 | 备注 |
  → 状态：Implemented / Partial / Missing / Wrongly Implemented

Axis 3 — 验收标准轴（独立上下文）：
  → 逐条切片 AC 确认是否可验证通过
  → 格式：| Slice | AC 条目 | 验证结果 | 证据 |
  → 结果：Pass / Fail / Unverifiable

汇总：合并三轴发现
  → 按需加载模板：templates/review-report.md
  → 填写模板，输出 Review Report，统计数量
```

**Gate Checklist**（6 项）：
```
[ ] 三轴审查均已独立执行
[ ] 所有 FR 均有对应实现状态记录
[ ] 所有切片 AC 均有验证结果
[ ] Critical 问题已列出，Developer 已逐条决策
[ ] Review Report 已输出到文件
[ ] Developer 已签署：_______ 日期：_______
```

**禁止行为（Anti-Patterns）**：
- ❌ 三轴合并一次执行
- ❌ 发现问题不标 file:line
- ❌ 接受外部评审建议不先验证技术正确性
- ❌ Critical 问题因"改动太大"降级为 Minor
- ❌ 输出"总体代码质量不错"等模糊总结语言

---

## 五、配套文件规格

### 5.1 CONTEXT.md 模板

```markdown
# Project Context
> 版本：1.0 | 最后更新：<date>

## Domain Glossary
| 术语 | 定义 | Avoid（禁止用这些替代词） |
|---|---|---|
| <Term> | <精确定义> | <同义词1>, <同义词2> |

## 项目约束
- 技术栈：
- 架构风格：
- 关键依赖：

## Always（所有阶段必须遵守）
- 

## Never（任何阶段都不允许）
- 

## Ask First（不确定时先问 Developer）
- 
```

**CONTEXT.md 维护规则**（所有 Skill 共同遵守）：
- 每次 Skill 执行前必须加载此文件
- 发现输出中使用 Avoid 列表词汇，立即标记 `❌ 矛盾: <术语>` 并停止
- 发现新术语：AI 提议定义 → Developer 确认 → 立即写入，不批量处理


### 5.3 Requirement Spec 模板

```markdown
# Requirement Spec：<功能名称>
> 版本：1.0 | 日期：<date> | 状态：draft → approved

## Original Request
<用户的字面表述，原文保留>

## Reframed Understanding
<AI 重诠释的本质问题>

## Scope Decision
- 决策：[Expansion | Selective Expansion | Hold Scope | Reduction]
- 理由：

## Functional Requirements
| ID | 需求描述 | 强度 | 验收标准 |
|---|---|---|---|
| FR-001 | | SHALL | |

> 强度：SHALL/MUST = 绝对要求 | SHOULD = 强烈推荐（有理由可不做） | MAY = 可选 | MUST NOT = 禁止

## Non-Functional Requirements
| ID | 需求描述 | 强度 | 可测量指标 |
|---|---|---|---|
| NFR-001 | | SHALL | |

## Out of Scope
- <明确排除的内容>

## Open Questions
| ID | 问题 | 影响哪个决策 | 状态 |
|---|---|---|---|
| Q-001 | | | open |

## Gate Checklist
- [ ] 无 TODO / TBD / ??
- [ ] 所有 FR 有具体验收标准
- [ ] Scope Decision 已明确输出
- [ ] 无未解决的 [NEEDS CLARIFICATION]
- [ ] 术语与 CONTEXT.md 一致
- [ ] Developer 已签署：_______ 日期：_______
```

### 5.4 Plan Spec 模板

```markdown
# Plan Spec：<功能名称>
> 版本：1.0 | 日期：<date> | 状态：draft → approved
> 关联：<requirement-spec-path>

## 方案决策
<选择的方案及理由，替代方案被拒绝的原因>

## 垂直切片列表
| ID | 切片名称 | schema | API | UI | tests | 验收标准 | 执行模式 | 依赖 |
|---|---|---|---|---|---|---|---|---|
| S-001 | | Y/N | Y/N | Y/N | Y | | HITL/AFK | - |

## 切片详细说明
### S-001：<切片名称>
**范围**：
**验收标准**：
- [ ] AC-1：
**测试计划**：
**执行模式**：HITL / AFK
**依赖**：无 / S-XXX

## Gate Checklist
- [ ] 每个切片覆盖所有相关层级（无纯横向切片）
- [ ] 每个切片有独立可验证的验收标准
- [ ] 每个切片有测试计划
- [ ] 无 [NEEDS CLARIFICATION] 未解决
- [ ] 无 TODO / TBD / ??
- [ ] Developer 已签署：_______ 日期：_______
```

### 5.5 Review Report 模板

```markdown
# Review Report：<功能名称>
> 日期：<date>
> 关联：Requirement Spec <path> | Plan Spec <path> | Commit <ref>

## Axis 1 — 代码质量
| 严重级别 | 文件:行号 | 问题描述 | 建议修复 |
|---|---|---|---|
| Critical | | | |

## Axis 2 — 需求一致性
| FR-ID | 需求描述 | 实现状态 | 备注 |
|---|---|---|---|
| FR-001 | | Implemented | |

## Axis 3 — 验收标准验证
| Slice | AC 条目 | 验证结果 | 证据 |
|---|---|---|---|
| S-001 | AC-1 | Pass | <命令输出> |

## 汇总
- Critical：N 个 | Important：N 个 | Minor：N 个

## Developer 决策记录
| 问题 | 严重级别 | 决策 | 处理方式 |
|---|---|---|---|

## Gate Checklist
- [ ] 三轴审查均独立执行
- [ ] 所有 FR 有实现状态
- [ ] 所有 AC 有验证结果
- [ ] Critical 问题已逐条决策
- [ ] Developer 已签署：_______ 日期：_______
```

---

## 六、MVP User Stories（含验收标准）

**US-01**：提交模糊需求触发完整性评估
- [ ] 提交"帮我加个导出功能"，Skill 提问 ≥ 2 个缺失项
- [ ] Skill 不重复追问已有信息
- [ ] Skill 不直接生成代码或 Spec

**US-02**：Problem Reframing 挑战表面需求
- [ ] 每次 Requirement Review 必须产出 `Original Request` vs `Reframed Understanding` 对比
- [ ] Reframed Understanding 非原始输入的简单复述
- [ ] Developer 纠正理解后 Skill 重新执行 Reframing

**US-03**：Scope Decision 明确范围决策
- [ ] 每次必须输出四选一 Scope Decision
- [ ] Scope Decision 附理由说明
- [ ] Scope Decision 写入 Requirement Spec 文件

**US-04**：术语冲突拦截
- [ ] 输入含 Avoid 词汇时触发 `❌ 矛盾: <词汇>` 并暂停
- [ ] Developer 确认正确术语后继续
- [ ] 新术语经 Developer 确认后写入 CONTEXT.md

**US-05**：Placeholder 阻断下游
- [ ] Spec 含 TODO/TBD/?? 时列出所有位置并阻断
- [ ] 填完后重新触发门控才能继续

**US-06**：垂直切片强制覆盖所有层
- [ ] Plan Spec 不存在只包含 schema 或只包含 API 的切片
- [ ] 每个切片覆盖层级列清楚
- [ ] 切片数量 ≥ 2

**US-07**：切片有完整验收标准和测试计划
- [ ] 每个切片有 ≥ 1 条可验证验收标准
- [ ] 每个切片有测试计划

**US-08**：Pre-Execute Readiness Gate 阻断不完整 Plan
- [ ] 任意切片缺验收标准时 Execute 阻断并列出缺失切片
- [ ] Plan Spec 含未解决 NEEDS CLARIFICATION 时 Execute 阻断

**US-09**：逐切片推进并附完成证据
- [ ] 每次只实现一个切片，完成后询问是否继续
- [ ] 切片完成输出：测试命令 + 结果 + 验收标准逐条对照

**US-10**：Bug 走四阶段调试协议
- [ ] 遇 Bug 先输出 3-5 个可证伪假设
- [ ] 一次只测一个假设
- [ ] 连续 3 次修复无效主动停止上报

**US-11**：三轴独立审查
- [ ] Review Report 包含三个独立章节（Axis 1/2/3）
- [ ] Axis 2 逐条对照 Requirement Spec FR 列表
- [ ] Axis 3 逐条对照 Plan Spec 切片验收标准

**US-12**：问题分级并由 Developer 逐条决策
- [ ] 每个发现标注 Critical/Important/Minor 和 file:line
- [ ] Developer 对每个 Critical 问题有决策记录
- [ ] Review Report 写入文件前 Developer 签署

---

## 七、MVP Definition of Done

### 文件完整性
- [ ] 4 个 SKILL.md 全部存在，7 章节齐全（缺章节视为不合规）
- [ ] 3 个 Spec 模板存在且字段完整
- [ ] CONTEXT.md 模板存在
- [ ] 安装指南覆盖 Cursor / Claude Code / Windsurf

### 行为验证
- [ ] US-01 ~ US-12 验收标准通过率 ≥ 90%
- [ ] 门控阻断场景（Placeholder / NEEDS CLARIFICATION / 前置依赖）均正常工作
- [ ] Avoid 词汇触发 ❌ 矛盾 并暂停

### 端到端验证
- [ ] FDE 在 30 分钟内安装 4 个 Skill 到 Cursor 并完成 Hello World 全流程
- [ ] 全流程结束后所有 Spec 文件均存在，每个文件 Gate Checklist 已签署
- [ ] 全流程产出 3 个 Spec 文件，均通过 Gate Checklist

### 文档质量
- [ ] 每个 SKILL.md Anti-Patterns ≥ 5 条具体禁止行为
- [ ] Spec 模板所有字段有注释说明和填写示例

---

## 八、目录结构

```
skills/
├── requirement-review.skill.md
├── plan.skill.md
├── execute.skill.md
└── review.skill.md

templates/
├── requirement-spec.md
├── plan-spec.md
├── review-report.md
└── CONTEXT.md

docs/
└── installation-guide.md
```

客户侧项目结构：

```
<project-root>/
├── CONTEXT.md
└── specs/
    ├── requirement-spec-<feature>.md
    ├── plan-spec-<feature>.md
    └── review-report-<feature>.md
```


---

## 九、风险与约束

| 风险 | 影响 | 缓解措施 |
|---|---|---|
| LLM 行为一致性：不同模型对 SKILL.md 指令遵守程度不同 | 门控可能被绕过 | Anti-Patterns 用强硬措辞（"禁止"非"不建议"）；Promptfoo 测试集验证 |
| 上下文长度：长项目 Spec 文件超限 | AI 降智 | Context Loading 章节规定加载优先级，超限主动提示 Developer |
| FDE 安装门槛：不同 IDE Rules 加载机制不同 | 安装失败 | 安装指南覆盖 3 个工具，含 troubleshooting |
| 术语漂移：CONTEXT.md 未及时更新 | 下游行为漂移 | 每个 Skill 强制加载；新术语当次会话内写入 |
