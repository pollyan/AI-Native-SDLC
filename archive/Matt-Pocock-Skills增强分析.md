# Matt Pocock Skills 库增强分析

> 日期：2026-05-17
> 目的：深入研读 Matt Pocock Skills 库全部 SKILL.md，与 AI-Native-SDLC 现有框架对比，产出具体增强建议

---

## 一、Matt Pocock Skills 库的核心设计理念

### 1.1 总览

Matt Pocock Skills 库包含 **27 个 skill**，分布在 6 个子目录中：

| 分类 | 数量 | 核心定位 |
|------|------|---------|
| engineering/ | 9 | 工程实践核心：诊断、TDD、架构改进、原型、任务管理 |
| productivity/ | 4 | 效率工具：压缩沟通、拷问式审查、会话交接、skill 创作 |
| personal/ | 2 | 个人工具：文章编辑、Obsidian 笔记管理 |
| misc/ | 4 | 杂项：Git 安全钩子、pre-commit、练习脚手架、迁移工具 |
| in-progress/ | 4 | 写作工具（beats/fragments/shape）和代码评审 |
| deprecated/ | 4 | 已废弃：接口设计、QA、重构计划、统一语言 |

### 1.2 五大核心设计理念

#### 理念一：门控驱动的阶段推进（Phase Gates）

几乎每个工程 skill 都有明确的 **"不要跳过"** 边界：

- `diagnose`："Do not proceed to Phase 2 until you have a loop you believe in"、"Do not proceed until you reproduce the bug"
- `tdd`："Never refactor while RED. Get to GREEN first"
- `review`："Don't proceed until you have it"（指 fixed point）

**设计模式**：每个阶段有明确的 **进入条件** 和 **退出条件**，不满足条件不往下走。这不是"建议"，而是 hard gate。

**对我们的启示**：我们的框架 v0.3.1 有检查点概念但偏软（"建议系统提示人确认"）。Matt Pocock 的做法更极端——即使在单 agent 内部，也用硬门控强制阶段推进的质量。

#### 理念二：反馈循环是核心能力（Feedback Loops First）

`diagnose` skill 的 Phase 1 标题是 **"Build a feedback loop — This IS the skill"**。全文用了最大篇幅（占整个 skill 的 ~40%）讲如何构建反馈循环，提供了 10 种从高到低排列的循环构建方式，以及优化循环本身的 4 个维度。

这不是"调试技巧"，而是一个**方法论立场**：没有可重复的 pass/fail 信号，任何分析都是猜测。

**核心金句**：
> "If you have a fast, deterministic, agent-runnable pass/fail signal for the bug, you will find the cause. If you don't have one, no amount of staring at code will save you."

**对我们的启示**：我们的 Developer 角色有"调试能力"，但没有给调试一个系统化的方法论。`diagnose` 的反馈循环方法论可以直接嵌入。

#### 理念三：反锚定——强制多假设（Anti-Anchoring）

两个 skill 都强调了**反锚定**：

- `diagnose` Phase 3："Generate 3–5 ranked hypotheses before testing any of them. Single-hypothesis generation anchors on the first plausible idea."
- `review`：用两个**并行 sub-agent** 分别审查 Standards 和 Spec，"so they don't pollute each other's context"

**设计模式**：AI 天然有锚定倾向（抓住第一个想法不放），通过**结构化约束**（必须生成多个假设、必须并行独立审查）来对抗。

**对我们的启示**：我们的 Reviewer 角色做质量验证时，应该引入多维度独立审查而不是一次"综合检查"。

#### 理念四：领域语言一致性（Domain Language Consistency）

**几乎所有 engineering skill 都要求**：

- "use the project's domain glossary"
- "respect ADRs in the area you're touching"
- "use CONTEXT.md vocabulary for the domain"

这不是某个 skill 的特殊要求，而是**跨 skill 的基础设施约定**。`setup-matt-pocock-skills` 就是专门配置这个基础设施的 skill——配置 issue tracker、triage labels、domain doc layout。

**配套机制**：
- `CONTEXT.md`：领域词汇表（纯术语定义，不含实现细节）
- `docs/adr/`：架构决策记录
- `CONTEXT-MAP.md`：多上下文仓库的入口索引

**对我们的启示**：我们的框架有 `config.yaml` 中的 `tech_stack` 和 `code_style`，但**没有领域词汇一致性机制**。这是 `grill-with-docs` 和 `ubiquitous-language`（已废弃但理念保留在 `grill-with-docs` 中）的核心贡献。

#### 理念五：垂直切片优于水平切片（Vertical Slices Over Horizontal）

`tdd` skill 明确反对"水平切片"——不要先写完所有测试再写所有实现：

> "DO NOT write all tests first, then all implementation. This produces CRAP TESTS."

正确做法是**垂直的 tracer bullet**：一个测试 → 一个实现 → 重复。

`to-issues` 同样强调垂直切片：每个 issue 是一个 "thin vertical slice that cuts through ALL integration layers end-to-end"。

**对我们的启示**：我们的 Developer 角色在 Execute 阶段按 Task List 逐步实现，但没有强调**切片策略**。应加入垂直切片的强制约束。

### 1.3 设计模式总结

| 模式 | 体现 | 核心机制 |
|------|------|---------|
| 阶段门控 | diagnose、tdd、review | 每阶段有明确的进入/退出条件 |
| 反馈循环优先 | diagnose Phase 1 | 先建信号再做分析 |
| 反锚定 | diagnose（多假设）、review（并行 sub-agent） | 结构化对抗锚定倾向 |
| 领域语言一致 | 所有 engineering skill | CONTEXT.md + ADRs 基础设施 |
| 垂直切片 | tdd、to-issues | Tracer bullet，不水平切片 |
| 内联文档更新 | grill-with-docs、improve-codebase-architecture | 决策产生时立即更新 CONTEXT.md/ADR |
| 可证伪假设 | diagnose Phase 3 | 每个假设必须有可证伪的预测 |
| 状态机驱动 | triage | 5 个状态角色，明确的转换规则 |
| 一次性原型 | prototype | 明确标记 throwaway，用完即删 |
| 两轴评审 | review | Standards 轴 + Spec 轴独立报告 |

---

## 二、与 AI-Native-SDLC 现有设计的对比分析

### 2.1 已覆盖（两边都有，方向一致）

| 能力 | AI-Native-SDLC | Matt Pocock Skills | 对比 |
|------|---------------|-------------------|------|
| 工作流阶段化 | Brainstorm → Plan → Execute → Review | diagnose 6 阶段、tdd 4 阶段、triage 状态机 | ✅ 方向一致，我们的工作流更宏观 |
| 门控机制 | 检查点（Plan/Review） | 阶段内 hard gate | ⚠️ 我们偏软，Matt 更硬 |
| TDD | Developer 角色含编码能力 | tdd skill 有完整红绿重构流程 | ⚠️ 我们缺 TDD 方法论的具体步骤 |
| 任务拆分 | Architect 角色含任务拆分能力 | to-issues 有垂直切片方法论 | ⚠️ 我们缺切片策略的约束 |
| 代码评审 | Reviewer 角色含质量验证 | review skill 有两轴独立审查 | ⚠️ 我们缺结构化评审框架 |
| 配置分层 | 项目层/个人层 | setup skill 配置 per-repo 基础设施 | ✅ 方向一致 |
| 上下文管理 | 上下文预算指导 | 无专门机制 | ✅ 我们领先 |

### 2.2 缺失（我们有，Matt 没有或不强调）

| 能力 | 说明 |
|------|------|
| 上下文预算管理 | GSD 的核心创新，我们已纳入，Matt 完全没有 |
| 活跃/归档分离 | OpenSpec 的文件管理模式，我们已纳入 |
| Quick/Standard/Full 流程分层 | 我们的三种流程模式，Matt 没有（每个 skill 独立调用） |
| 角色与工作流解耦 | 我们的双层架构，Matt 是单层（skill 直接用） |
| Anti-Rationalization | 已规划（来自 agent-skills），Matt 没有显式机制 |
| 可观测性 log.jsonl | 我们的轻量日志，Matt 没有 |
| 模型路由 | 我们的模型分配策略，Matt 没有提及 |

### 2.3 可增强（我们有但 Matt 做得更深）

以下是我们现有设计可以**从 Matt Pocock 具体方法论中吸收增强**的领域：

#### 增强 1：阶段内的硬门控细化

**现状**：我们的检查点在阶段之间（Plan 完成、Execute 完成、Review 完成），但**阶段内部**没有门控。

**Matt 的做法**：`diagnose` 在单个 skill 内部就有 6 个 phase，每个 phase 都有明确的 "Do not proceed until..." 条件。例如：
- Phase 1（建反馈循环）→ 必须有可靠的 pass/fail 信号才能进入 Phase 2
- Phase 2（复现）→ 必须复现 bug 才能进入 Phase 3
- Phase 3（假设）→ 必须列出 3-5 个可证伪假设才能进入 Phase 4

#### 增强 2：Developer 角色的调试方法论

**现状**：Developer 有"调试能力"，但没有系统化方法。

**Matt 的做法**：`diagnose` 提供了完整的 6 阶段调试方法论，从"建反馈循环"到"清理+事后复盘"，每个阶段有具体步骤和决策树。

#### 增强 3：Reviewer 角色的结构化评审

**现状**：Reviewer 有"质量验证"能力，但没有定义评审维度和报告结构。

**Matt 的做法**：`review` 用两轴独立审查（Standards vs Spec），并行 sub-agent 各自输出不超过 400 字的报告。两轴分开报告，防止一个维度掩盖另一个维度的问题。

#### 增强 4：Architect 角色的拷问式方案设计

**现状**：Architect 有"方案设计"能力，但缺少与用户交互的结构化方法。

**Matt 的做法**：`grill-with-docs` 提供了完整的拷问式审查流程——逐个问题拷问、内联更新 CONTEXT.md 和 ADR、精确术语检查。`improve-codebase-architecture` 在此基础上加了"深度模块"概念和 grilling loop。

#### 增强 5：领域词汇管理基础设施

**现状**：我们有 `config.yaml` 中的 `tech_stack` 和 `code_style`，但没有领域词汇一致性机制。

**Matt 的做法**：`setup-matt-pocock-skills` 配置 `CONTEXT.md`（领域词汇表）+ `docs/adr/`（架构决策记录）。所有 engineering skill 启动时都读取这些文件，确保产出使用一致的领域语言。

---

## 三、具体增强建议（按优先级排列）

### 优先级 1：高价值、低侵入性，可立即嵌入

---

#### 建议 1：Developer 角色嵌入 diagnose 反馈循环方法论

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/diagnose/SKILL.md` |
| **增强位置** | `roles/developer.md` 的调试能力 |
| **为什么适合** | 调试是 Developer 最高频也最容易出错的能力。当前定义只说"调试"但没给方法论，开发者容易陷入"猜-改-猜"循环 |
| **具体落地** | 在 developer.md 的调试能力中加入： |
| | 1. **反馈循环优先**：遇到 bug 先建可重复的 pass/fail 信号（优先级：失败测试 > HTTP 脚本 > CLI 调用 > 丢弃式 harness） |
| | 2. **多假设反锚定**：必须生成 3-5 个可证伪假设，不能只追第一个想法 |
| | 3. **单一变量探针**：每个调试探针对应一个具体假设的预测，用 `[DEBUG-xxxx]` 标记 |
| | 4. **回归测试先于修复**：先写失败测试，再修复，再确认测试通过 |
| | 5. **事后复盘**：修复后问"什么能防止这类 bug"，必要时移交 Architect |
| **预计工作量** | 0.5 天（内容编写） |

---

#### 建议 2：Developer 角色嵌入 TDD 红绿重构方法论

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/tdd/SKILL.md` |
| **增强位置** | `roles/developer.md` 的编码能力 |
| **为什么适合** | 当前编码能力缺少"怎么写"的方法论约束。Matt 的 TDD 不是"先写测试"这么简单，而是一个完整的 tracer bullet 循环 |
| **具体落地** | 在 developer.md 的编码能力中加入： |
| | 1. **禁止水平切片**：不允许"先写完所有测试再写所有实现" |
| | 2. **Tracer bullet 循环**：一个测试 → 最小实现 → 通过 → 下一个测试 → 重构 |
| | 3. **行为测试 vs 实现测试**：测试验证公共接口的行为，不验证内部实现。如果重构后测试失败了，说明测试写错了 |
| | 4. **每个循环的 checklist**：[测试描述行为而非实现] [只用公共接口] [代码是最小的] [没有投机功能] |
| **预计工作量** | 0.5 天 |

---

#### 建议 3：Reviewer 角色嵌入两轴评审框架

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/review/SKILL.md`（in-progress） |
| **增强位置** | `roles/reviewer.md` 的质量验证能力 |
| **为什么适合** | 当前 Reviewer 的质量验证没有结构化维度。两轴评审（Standards vs Spec）简单但有效——防止"代码写得好但做错了事"或"做对了事但代码写得烂"被互相掩盖 |
| **具体落地** | 在 reviewer.md 的质量验证能力中定义： |
| | 1. **Standards 轴**：代码是否遵守项目的编码规范（读取 AGENTS.md、CONTRIBUTING.md、ESLint/Prettier 配置等） |
| | 2. **Spec 轴**：代码是否忠实实现了 Plan 阶段定义的方案（读取 plan.md 和 spec.md） |
| | 3. **独立报告**：两轴分开报告，每轴不超过 400 字。不合并、不重新排序 |
| | 4. **最终汇总**：一行的总结——每轴发现总数 + 最严重的单个问题 |
| **预计工作量** | 0.5 天 |

---

#### 建议 4：Architect 角色嵌入拷问式方案审查流程

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/grill-with-docs/SKILL.md` + `engineering/improve-codebase-architecture/SKILL.md` |
| **增强位置** | `roles/architect.md` 的方案设计能力 |
| **为什么适合** | 当前 Architect 的方案设计是"输出方案"的模式，缺少与用户交互的结构化方法。grill-with-docs 提供了逐个问题拷问、内联更新文档的完整流程 |
| **具体落地** | 在 architect.md 的方案设计能力中加入： |
| | 1. **逐个决策拷问**：不走"我出一个完整方案你看行不行"，而是把设计树展开，逐个分支确认 |
| | 2. **术语一致性检查**：用户使用的术语与项目已有术语冲突时立即指出 |
| | 3. **具体场景压力测试**：讨论域关系时，构造具体场景探测边界情况 |
| | 4. **内联文档更新**：决策确定时立即更新 spec.md 中的术语定义（不等批量处理） |
| | 5. **ADR 创建条件**：只在同时满足"难回退"、"不看上下文会意外"、"有真实权衡"三个条件时才创建 ADR |
| **预计工作量** | 1 天 |

---

#### 建议 5：引入 CONTEXT.md 领域词汇表机制

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/grill-with-docs/SKILL.md` + `engineering/setup-matt-pocock-skills/SKILL.md` + `deprecated/ubiquitous-language/SKILL.md` |
| **增强位置** | 新增 `.agents/context.md` + 所有角色定义文件 |
| **为什么适合** | 所有 Matt Pocock engineering skill 都要求"use the project's domain glossary"。没有领域词汇一致性，不同角色/不同阶段的产出会使用不同的术语，积累成沟通混乱 |
| **具体落地** | |
| | 1. 新增 `.agents/context.md`：项目领域词汇表，纯术语定义（不含实现细节） |
| | 2. 多上下文仓库支持：如果项目有多个独立域（如 monorepo），用 `context-map.md` 索引 |
| | 3. 所有角色启动时读取 `context.md`：产出必须使用一致的领域语言 |
| | 4. 在 Brainstorm 阶段（Analyst 澄清能力）中，发现新术语时立即更新 `context.md` |
| | 5. 在 Plan 阶段（Architect 方案设计能力）中，术语冲突时立即指出并要求确认 |
| **预计工作量** | 1 天（定义格式 + 更新角色文件 + 写示例） |

---

#### 建议 6：to-issues 垂直切片方法论嵌入任务拆分

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/to-issues/SKILL.md` |
| **增强位置** | `roles/architect.md` 的任务拆分能力 |
| **为什么适合** | 当前任务拆分只说"输出 Task List"，没有切片策略约束。to-issues 的垂直切片规则可以防止产出"先写 schema → 再写 API → 再写 UI"的水平切片 |
| **具体落地** | 在 architect.md 的任务拆分能力中加入： |
| | 1. **垂直切片强制**：每个 task 必须是一个端到端的垂直切片（schema + API + UI + tests），不是单个层的水平切片 |
| | 2. **切片分类**：每个 task 标记为 HITL（需要人交互）或 AFK（可独立完成），优先 AFK |
| | 3. **验收标准**：每个 task 有明确的 acceptance criteria（checkbox 列表） |
| | 4. **依赖关系**：标注 task 间的阻塞关系，无依赖的可以并行 |
| | 5. **粒度检查**：与用户确认粒度是否合适（太粗/太细/需要合并/需要拆分） |
| **预计工作量** | 0.5 天 |

---

### 优先级 2：中等价值，可在 Phase 2 落地

---

#### 建议 7：引入 triage 状态机管理 issue

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/triage/SKILL.md` |
| **增强位置** | 新增 `workflows/bugfix.md` + `roles/analyst.md` 增加 issue 评审能力 |
| **为什么适合** | 我们的框架有 bugfix 工作流（已规划），但缺 issue 管理的方法论。triage 的状态机（needs-triage → needs-info → ready-for-agent/ready-for-human → done/wontfix）可以直接用 |
| **具体落地** | |
| | 1. 在 bugfix 工作流中定义 5 个状态标签 |
| | 2. Analyst 角色增加 issue triage 能力：评估 → 复现（bug）→ 拷问（如需）→ 标记状态 |
| | 3. 引入 agent brief 模板：ready-for-agent 状态的 issue 需要包含完整的上下文，让 Developer 无需人交互就能工作 |
| | 4. 引入 out-of-scope 知识库：won't fix 的 issue 记录原因，防止未来重复提交 |
| **预计工作量** | 1.5 天 |

---

#### 建议 8：引入 prototype 一次性原型方法论

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/prototype/SKILL.md` |
| **增强位置** | `roles/architect.md` 的方案设计能力（可选子能力）+ `workflows/feature.md` |
| **为什么适合** | 复杂功能在 Plan 阶段可能需要验证设计。当前框架没有"轻量验证"的机制——要么不做验证直接实现，要么做完整实现 |
| **具体落地** | |
| | 1. Architect 在方案设计时，对不确定的设计点可以建议原型验证 |
| | 2. 原型分两种分支：逻辑原型（终端交互验证状态模型）和 UI 原型（多个可切换的 UI 变体） |
| | 3. 原型规则：从第一天标记为 throwaway、一条命令运行、无持久化、跳过抛光 |
| | 4. 原型产出只是"答案"——记录到 ADR 或 issue 中，然后删除原型代码 |
| **预计工作量** | 0.5 天 |

---

#### 建议 9：引入 handoff 会话交接文档

| 维度 | 内容 |
|------|------|
| **来源 skill** | `productivity/handoff/SKILL.md` |
| **增强位置** | 跨角色的通用机制 |
| **为什么适合** | 我们的框架用对话上下文作为第一信息源，但在**上下文接近预算上限需要开新对话**时，缺少结构化的交接机制。GSD 的上下文质量管理 + Matt 的 handoff 结合，正好解决"换对话继续"的问题 |
| **具体落地** | |
| | 1. 在上下文预算管理中（§8），当对话接近 70% 时，触发 handoff 流程 |
| | 2. Handoff 文档包含：当前阶段、已完成的工作、待办事项、关键决策、下一阶段需要加载的角色 |
| | 3. 不重复已有文件（spec.md、plan.md 等）中的内容，而是引用它们 |
| | 4. Handoff 文档保存到 `.agents/active/{task-name}/handoff.md` |
| **预计工作量** | 0.5 天 |

---

#### 建议 10：improve-codebase-architecture 的"深度模块"概念

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/improve-codebase-architecture/SKILL.md` |
| **增强位置** | `roles/architect.md` 的方案设计能力 |
| **为什么适合** | Architect 做方案设计时需要一个评判模块质量的框架。"深度模块"（小接口、大实现）vs "浅模块"（接口和实现一样复杂）的概念简单但有力 |
| **具体落地** | |
| | 1. 在 architect.md 中引入"删除测试"：想象删除一个模块，如果复杂度消失了说明它是 pass-through；如果复杂度分散到 N 个调用者说明它在发挥作用 |
| | 2. 方案评审时检查：新模块是 deep 还是 shallow？接口是否足够简单？ |
| | 3. 用项目领域语言（来自 context.md）命名模块，而不是用技术术语 |
| **预计工作量** | 0.5 天 |

---

### 优先级 3：长期借鉴

---

#### 建议 11：grill-me 拷问式自我审查

| 维度 | 内容 |
|------|------|
| **来源 skill** | `productivity/grill-me/SKILL.md` |
| **增强位置** | 可作为任何角色的通用辅助能力 |
| **为什么有价值** | 当用户自己有一个模糊的想法，不需要走完整的 Brainstorm 流程，只需要"被拷问一下"来澄清 |
| **落地方式** | 作为 Analyst 角色的一个轻量触发模式——用户说"帮我理清这个想法"时进入 grill-me 模式 |

#### 建议 12：write-a-skill 角色定义创作方法论

| 维度 | 内容 |
|------|------|
| **来源 skill** | `productivity/write-a-skill/SKILL.md` |
| **增强位置** | 框架的自演进/扩展机制 |
| **为什么有价值** | 当用户需要创建新角色或新工作流时，有结构化的创作方法论。Matt 的 skill 结构要求（SKILL.md < 100 行、description 含触发词、超 500 行拆文件）对我们的角色定义文件同样适用 |
| **落地方式** | 在框架文档中增加"如何创建新角色"指南，参考 write-a-skill 的结构 |

#### 建议 13：zoom-out 模块关系图能力

| 维度 | 内容 |
|------|------|
| **来源 skill** | `engineering/zoom-out/SKILL.md` |
| **增强位置** | Developer 角色的理解代码能力 |
| **为什么有价值** | 当 Developer 进入 Execute 阶段遇到不熟悉的代码区域时，需要一个快速"拉远"看全局的能力。zoom-out 虽然只有两行（"Go up a layer of abstraction. Give me a map of all the relevant modules and callers"），但理念清晰 |

---

## 四、不建议借鉴的部分（及理由）

### 4.1 不建议借鉴：caveman 压缩沟通模式

**理由**：caveman 模式通过砍掉冠词、填充词、寒暄来降低 token 消耗约 75%。但在我们的**对话式工作流**中，人与 AI 的对话本身就是核心交互模式。过度压缩会降低可读性和交互体验。我们的框架定位是"辅助角色、人为主"，沟通质量比 token 效率更重要。

**例外**：如果用户主动要求简洁模式，可以作为 Analyst/Developer 角色的可选开关。

### 4.2 不建议借鉴：git-guardrails（Claude Code 专属）

**理由**：这是 Claude Code 的 PreToolUse hook 机制，依赖特定的工具链。我们的框架是工具无关的（"对话 + 文件驱动"），安全防护应该放在 config.yaml 的 Constitution 机制中，而不是工具链级别的 hook。

### 4.3 不建议借鉴：migrate-to-shoehorn

**理由**：这是 TypeScript 测试中 `as` 类型断言的迁移工具，是特定技术栈的特定问题。我们的框架是语言/技术栈无关的。

### 4.4 不建议借鉴：scaffold-exercises

**理由**：这是课程练习的脚手架工具，属于教育培训场景，与我们的开发工作流框架无关。

### 4.5 不建议借鉴：obsidian-vault

**理由**：个人笔记管理工具，与框架设计无关。

### 4.6 不建议借鉴：设计即废的 skill（design-an-interface、request-refactor-plan）

**理由**：这些已 deprecated。`design-an-interface` 的"Design It Twice"理念（生成 3+ 个完全不同的接口设计然后比较）有启发性，但实现上依赖并行 sub-agent，与我们当前的单对话上下文设计不匹配。如果未来支持 sub-agent 可以重新评估。

### 4.7 不建议借鉴：writing-beats / writing-fragments / writing-shape

**理由**：这些是文章写作工具，属于内容创作领域，与软件开发工作流无关。但其中的**逐段构建、逐步确认**的交互模式值得注意——如果未来框架扩展到文档撰写工作流，可以参考。

### 4.8 不建议借鉴：单层 skill 调用模式

**理由**：Matt Pocock Skills 是**扁平的 skill 列表**，用户按需调用单个 skill。没有"工作流"的概念——没有 skill 之间的衔接、没有阶段推进、没有状态管理。我们的双层架构（工作流 × 角色能力）是刻意的设计选择，提供更好的用户引导和端到端体验。不应该退回到扁平的 skill 列表。

---

## 五、实施路线建议

### 立即可做（Phase 2 范围内）

| # | 建议 | 工作量 | 依赖 |
|---|------|--------|------|
| 1 | Developer 嵌入 diagnose 反馈循环 | 0.5 天 | 无 |
| 2 | Developer 嵌入 TDD 红绿重构 | 0.5 天 | 无 |
| 3 | Reviewer 嵌入两轴评审 | 0.5 天 | 无 |
| 4 | Architect 嵌入拷问式方案审查 | 1 天 | 建议 5（context.md） |
| 5 | 引入 CONTEXT.md 领域词汇表 | 1 天 | 无 |
| 6 | Architect 嵌入垂直切片方法论 | 0.5 天 | 无 |

**合计**：约 4 天工作量，可在 Phase 2 中并行推进。

### 中期可做（Phase 3 范围内）

| # | 建议 | 工作量 |
|---|------|--------|
| 7 | Triage 状态机 + bugfix 工作流 | 1.5 天 |
| 8 | Prototype 一次性原型方法论 | 0.5 天 |
| 9 | Handoff 会话交接文档 | 0.5 天 |
| 10 | "深度模块" 概念 | 0.5 天 |

---

## 六、核心判断

Matt Pocock Skills 库最大的价值不在于单个 skill 的功能，而在于**它展示了一种"怎么把工程方法论编码成 AI 可执行的流程"的方式**：

1. **每个方法论都有步骤、有门控、有退出条件**——不是"确保代码质量好"这种空话，而是"运行测试并验证所有测试通过"
2. **反锚定机制贯穿始终**——无论是调试还是评审，都强制 AI 做多假设/多维度分析
3. **领域语言一致性是基础设施**——所有 skill 都依赖 CONTEXT.md 和 ADRs，确保术语一致
4. **反馈循环优先于分析**——先建可重复信号再做猜测

这些设计理念可以直接嵌入我们现有的角色定义文件中，不需要改变框架的架构（双层结构、对话式工作流、文件快照等核心设计保持不变）。增强发生在**能力定义的深度**层面，而不是架构层面。
