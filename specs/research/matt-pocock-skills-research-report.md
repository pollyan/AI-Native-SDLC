# Matt Pocock Skills 深度调研报告

> 仓库：内部 Skills 库（27 个 Skills，6 个子目录）｜ 更新时间：2026-05-17

---

## 1. 产品定位与核心哲学

**定位**：不追求全盘自动化的"乌托邦"，主张"**让开发者保持控制权（Keep developers in control）**"。将能力切碎为"离散的小技能（Composable Developer Skills）"——Unix 哲学：一个工具只做好一件事。

**27 个 Skills 分类总览**：

| 分类 | 数量 | 核心定位 |
|------|------|---------|
| engineering/ | 9 | 工程实践核心：诊断、TDD、架构改进、原型、任务管理 |
| productivity/ | 4 | 效率工具：压缩沟通、拷问式审查、会话交接、skill 创作 |
| personal/ | 2 | 个人工具：文章编辑、Obsidian 笔记管理 |
| misc/ | 4 | 杂项：Git 安全钩子、pre-commit、练习脚手架、迁移工具 |
| in-progress/ | 4 | 写作工具（beats/fragments/shape）和代码评审 |
| deprecated/ | 4 | 已废弃：接口设计、QA、重构计划、统一语言（理念已合并到其他 skills） |

**五大核心设计理念**：

1. **门控驱动的阶段推进（Phase Gates）**
2. **反馈循环是核心能力（Feedback Loops First）**
3. **反锚定——强制多假设（Anti-Anchoring）**
4. **领域语言一致性（Domain Language Consistency）**
5. **垂直切片优于水平切片（Vertical Slices Over Horizontal）**

---

## 2. 五大核心设计理念详解

### 2.1 门控驱动的阶段推进（Phase Gates）

几乎每个工程 skill 都有明确的"不要跳过"边界：
- `diagnose`："Do not proceed to Phase 2 until you have a loop you believe in"
- `tdd`："Never refactor while RED. Get to GREEN first"
- `review`："Don't proceed until you have it"（指 fixed point）

**设计模式**：每个阶段有明确的**进入条件**和**退出条件**，不满足条件不往下走。这不是"建议"，而是 hard gate。

### 2.2 反馈循环是核心能力

`diagnose` skill 的 Phase 1 标题是 **"Build a feedback loop — This IS the skill"**。

花不均衡的努力在这里。核心金句：
> "If you have a fast, deterministic, agent-runnable pass/fail signal for the bug, you will find the cause. If you don't have one, no amount of staring at code will save you."

**10 种反馈循环构建方式（按优先级从高到低）**：
1. 已有的失败测试
2. 可以快速编写的失败测试
3. 最小化的失败脚本（10-20 行）
4. 失败的 HTTP 脚本（curl/HTTPie）
5. 失败的 CLI 调用
6. 应用内的可重现失败路径
7. 一次性诊断测试套件（harness）
8. 浏览器中可重现的失败路径
9. 日志中的失败消息
10. 手动可重现的失败步骤（最后手段）

### 2.3 反锚定——强制多假设

两个 skill 都强调**反锚定**：
- `diagnose` Phase 3："Generate 3–5 ranked hypotheses before testing any of them. Single-hypothesis generation anchors on the first plausible idea."
- `review`：用两个**并行 sub-agent** 分别审查 Standards 和 Spec，"so they don't pollute each other's context"

**核心模式**：AI 天然有锚定倾向，通过**结构化约束**（必须生成多个假设、必须并行独立审查）来对抗。

每个假设必须能被证伪：
> "If you can't state a prediction, it's a feeling. Discard or sharpen it."

Tagged debug logs（`[DEBUG-a4f2]`）——清理变成一次 grep。

### 2.4 领域语言一致性

**跨 skill 的基础设施约定**——几乎所有 engineering skill 都要求：
- "use the project's domain glossary"
- "respect ADRs in the area you're touching"
- "use CONTEXT.md vocabulary for the domain"

**配套机制**：
- `CONTEXT.md`：领域词汇表（纯术语定义，不含实现细节）
- `docs/adr/`：架构决策记录
- `CONTEXT-MAP.md`：多上下文仓库的入口索引

`setup-matt-pocock-skills` 专门配置这个基础设施。

### 2.5 垂直切片优于水平切片

`tdd` skill 明确反对"水平切片"：
> "DO NOT write all tests first, then all implementation. This produces CRAP TESTS."

正确做法是**垂直的 tracer bullet**：一个测试 → 一个实现 → 重复。

`to-issues` 同样强调：每个 issue 是一个"thin vertical slice that cuts through ALL integration layers end-to-end"。

---

## 3. 核心 Skills 详解

### 3.1 diagnose（调试诊断）——6 阶段完整方法论

```
Phase 1: Build feedback loop（这整个 skill 就是构建反馈循环）
Phase 2: Reproduce（精确复现 bug）
Phase 3: Minimise（最小化复现路径）
Phase 4: Hypothesise（3-5 个可证伪假设）
Phase 5: Instrument & Test（一次只测一个假设）
Phase 6: Fix & Regression Test（修根因，不修症状）
```

**Phase 5 的单一变量原则**：
- 每个调试探针对应一个具体假设的预测
- 用 `[DEBUG-a4f2]` 标记（清理变成一次 grep）
- 一次只改一个变量
- 记录所有观察，不只是成功的

**结束条件**：回归测试通过 + 事后复盘（"什么能防止这类 bug"）

### 3.2 tdd（测试驱动开发）——红绿重构循环

```
Planning → RED → GREEN → REFACTOR → Cycle
```

**Planning 步骤（每个切片开始前）**：
1. 确认接口变更范围
2. 确认测试的行为（而非实现）
3. 识别深模块机会
4. 设计可测试接口
5. 列出行为清单
6. 用户批准

**Per-cycle checklist**：
- [ ] 测试描述行为而非实现
- [ ] 只使用公共接口
- [ ] 代码是最小的（minimum passing implementation）
- [ ] 没有投机功能（YAGNI）
- [ ] 能承受重构（如果重构后测试失败，说明测试写错了）

**关键限制**：
- RED 阶段：只允许写测试代码
- GREEN 阶段：只写让测试通过的最小实现
- REFACTOR：只在 GREEN 后进行，且不破坏已通过的测试

### 3.3 review（两轴独立评审）

**两轴设计（互不污染上下文）**：

**Standards 轴**（代码是否遵守项目规范）：
- 引用依据：AGENTS.md、CONTRIBUTING.md、docs/adr/、ESLint/Prettier 配置
- 关注：编码规范、命名约定、架构约束

**Spec 轴**（代码是否忠实实现了 Plan）：
- 引用依据：plan.md 和 spec.md
- 关注三类问题：缺失（Missing）、范围蔓延（Scope Creep）、实现错误（Wrong）

**每个发现必须**：
- 引用 spec 原文（"According to spec §3.2...")
- 引用标准文件（"According to AGENTS.md...")
- 标注严重级别（Critical/Important/Minor）
- 标注文件和行号（file:line）

**输出格式**：
```
Standards Axis (≤400 words):
[findings referencing project docs]

Spec Axis (≤400 words):
[findings referencing plan.md/spec.md]

Summary:
Standards: N findings, most severe: [issue]
Spec: M findings, most severe: [issue]
Overall verdict: [Pass / Needs fixes]
```

**核心洞察**："一个轴 pass 另一个 fail"——代码写得好但做错了事，或做对了事但代码写得烂，两种情况都会被捕捉。

### 3.4 to-issues（垂直切片拆分）

每个 issue 是一个 **thin vertical slice**：
```markdown
## [Issue Title]
**Parent**: [Parent issue if applicable]
**What to build**: [Specific feature]
**Acceptance criteria**:
- [ ] [Specific, testable criterion]
**Blocked by**: [Dependencies]
**Type**: HITL | AFK
```

**HITL/AFK 分类**：
- HITL（Human-in-the-Loop）：需要人工交互的切片
- AFK（Away From Keyboard）：可以自动执行的切片，优先排序

### 3.5 grill-with-docs（拷问式方案审查）

逐分支走设计树的审查方法：
1. 逐个决策拷问——不是"我出方案你看行不行"，而是把设计树展开逐个分支确认
2. 术语一致性检查——用户使用的术语与 CONTEXT.md 冲突时立即指出
3. 具体场景压力测试——构造具体场景探测边界情况
4. 内联文档更新——决策确定时立即更新 CONTEXT.md（不等批量处理）
5. ADR 创建条件——只在同时满足"难回退"、"不看上下文会意外"、"有真实权衡"时才创建

### 3.6 improve-codebase-architecture（架构改进）

**"深度模块"诊断**：

> 删除测试：想象删除一个模块，如果复杂度消失了说明它是 pass-through（浅模块）；如果复杂度分散到 N 个调用者说明它在发挥作用（深模块）。

**浅模块识别（interface ≈ implementation）**：
- 模块的接口和实现一样复杂，说明没有封装价值
- 深模块标准：小接口 + 大实现

**Grilling Loop**：
1. 读取代码库 + CONTEXT.md + ADRs
2. 识别最深的耦合点
3. 提问——逐个质询设计决策
4. 更新文档
5. 重复

---

## 4. 工程架构设计

### 4.1 领域语言统一基础设施
所有 engineering skill 依赖的通用约定：
- `CONTEXT.md`——纯术语定义（不含实现细节）
- `docs/adr/`——架构决策记录
- `setup-matt-pocock-skills`——配置 per-repo 基础设施

### 4.2 轻量化组合架构
主要由独立的 Markdown 提示词块组成，通过宿主命令注册机制载入。无常驻进程，无数据库，高度组合性。

### 4.3 TDD 的严格闭环验证结构
设计了无法绕过的红绿重构环验证结构：
- 编写测试 → 提供测试失败证据（截图或执行报错日志）
- 只补充最小范围实现代码 → 提供测试通过证明
- 提示可选的重构
- 对抗"AI 直接大面积乱写并产生回归问题"的最强防线

---

## 5. Skills 对各阶段的贡献矩阵

| 阶段 | 最佳 Skill | 核心借鉴点 |
|------|-----------|---------|
| Requirement Review | `grill-with-docs`、`to-prd` | 拷问式审查、CONTEXT.md 术语一致、Implementation Decisions |
| Plan | `to-issues`、`grill-with-docs` | 垂直切片、HITL/AFK 分类、ADR 创建条件 |
| Execute | `tdd`、`diagnose` | TDD 红绿循环、6 阶段调试方法论、反锚定多假设 |
| Review | `review`、`improve-codebase-architecture` | 两轴独立评审、深度模块识别、范围蔓延检测 |

---

## 6. 对我们框架的借鉴价值

| 借鉴点 | 优先级 | 对应我们的设计 |
|-------|--------|--------------|
| CONTEXT.md 领域词汇表 | ✅ 已纳入 | §二原则 #3 统一语言机制 |
| 两轴独立评审 | ✅ 已纳入 | Review Skill 三轴审查 |
| 垂直切片 tracer bullet | ✅ 已纳入 | §二原则 #4 垂直切片驱动 |
| 阶段内 hard gate | ⭐⭐⭐ 高优 | 现有门控偏软，需要参考 Matt 的硬门控 |
| diagnose 6 阶段方法论 | ⭐⭐⭐ 高优 | Execute 阶段调试协议（已在 AGENTS.md 体现） |
| 3-5 可证伪假设 | ✅ 已纳入 | AGENTS.md 调试约束 |
| TDD per-cycle checklist | ⭐⭐⭐ 高优 | Execute Skill 中每切片质量标准 |
| 两轴评审输出格式（≤400字/轴） | ⭐⭐⭐ 高优 | Review Skill 输出格式设计 |
| grill-with-docs 拷问式审查 | ⭐⭐ 中优 | Plan 阶段方案设计交互模式 |
| handoff 会话交接文档 | ⭐⭐ P3-x | 上下文切换时的交接协议 |
