# Superpowers 深度调研报告

> 仓库：https://github.com/obra/superpowers  
> Stars：183K（最高）  
> 更新时间：2026-05-17

---

## 1. 产品定位与核心哲学

**定位**：当前市场上对 AI 编程行为约束最严格、流程最完整的规范驱动框架。核心立场鲜明：**"在失控的 AI 编码浪潮中，用严格的方法论建立秩序"**。

**对 AI 能力的判断**：Superpowers 并不迷信 AI，将当前 AI 定义为 "一个充满热情但缺乏品味、没有判断力、且极其讨厌写测试的初级程序员"。因此其整体设计基调是极度的约束，而非赋权。

**独特的市场锚点**：
- 唯一内建 **Spec 自审核循环**的框架（写完 spec 后自动做 placeholder 扫描、一致性检查、scope 检查，发现问题直接修复）
- 唯一将 **TDD 强制化**（而非建议）的框架，包含无测试代码自动销毁机制
- 唯一将 **方案对比强制化**（2-3 个方案含 trade-off + 推荐标记）的框架

---

## 2. 全生命周期工作流设计

### 2.1 Requirement Review 阶段（brainstorming skill）

**产出文件路径**：`docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`

**模板结构（从 SKILL.md 流程推断）**：
```markdown
# [Topic] Design

## Background / Problem Statement
[项目上下文理解 + 用户意图理解]

## Design Goals
[从对话中提炼的设计目标]

## Approach Options
### Option A: [Name] (Recommended)
- Pros: ...
- Cons: ...
### Option B: [Name]
- Pros: ...
- Cons: ...
### Option C: [Name]
- Pros: ...
- Cons: ...

## Proposed Design
### [Section 1 — scaled to complexity]
### [Section 2]

## Scope Boundaries
[明确范围内/范围外]

## Open Questions
[未解决的问题]
```

**流程控制规则**：
1. 探索项目上下文 → 逐个提问澄清 → 提出 2-3 个方案（含 trade-off）
2. 逐段展示设计获用户批准
3. 写入设计文档 → **自审核**（placeholder scan + 一致性检查 + scope 检查）
4. 用户审核文档
5. **HARD-GATE**：在用户批准设计之前，禁止调用任何实现 skill
6. Anti-Pattern 声明："This Is Too Simple To Need A Design"——即使最简单的项目也必须过设计流程
7. 终态：调用 writing-plans skill（不是直接实现）

**独特设计点**：
- 方案对比强制（2-3 approaches，每个含 trade-off，明确标注推荐方案）
- Spec Self-Review 自审核循环（行业唯一）
- "Reflection on how you think"——AI 对用户思考方式的观察记录

---

### 2.2 Plan 阶段（writing-plans skill）

**产出文件路径**：`docs/superpowers/plans/<filename>.md`

**模板结构（精确到代码级别）**：
```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement 
> this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

### Task N: [Component Name]
**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**
```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**
Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL.

**Step 3: Implement the minimal code to make the test pass**
[code block with exact implementation]

**Step 4: Run tests and verify they pass**
Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS.

**Step 5: Commit**
```

**计划阶段的关键设计原则**：
1. **Scope Check** — 先检查复杂度，简单需求可能不需要完整计划
2. **TDD 内嵌** — 每个 Task 强制"写测试→验证失败→实现→验证通过→提交"五步循环
3. **No Placeholders** — 禁止 TBD/TODO，所有代码必须完整可执行
4. **自审核** — 写完计划后做 cross-reference 检查（函数名在 Task 3 和 Task 7 是否一致）
5. **Execution Handoff** — 保存后提供两种执行模式选择

**极端精确性设计**：
- 每个 Step 包含完整可执行代码（不是描述，是实际代码）
- 每个文件精确到行号（`Modify: path/file.py:123-145`）
- 计划即 Prompt——plan 本身就是给下游 agent 的指令

---

### 2.3 Execute 阶段（两种执行模式）

#### 模式 A：executing-plans（单 Agent 内联执行）
1. Load and review plan — 加载计划文件，审查关键缺失
2. Execute tasks — 逐 Task 执行，每个 Task 包含：加载 → 理解 → 实现 → 运行验证 → 标记完成
3. Complete development — 全部完成后总结

#### 模式 B：subagent-driven-development（多 Agent 分发执行）
1. 读取 plan 文件一次，提取所有 task 全文
2. 创建 TodoWrite 跟踪列表
3. 对每个 Task：
   - 分发独立 implementer subagent（带完整 task text + context）
   - 实现完成后，分发 **spec compliance reviewer subagent**（第一阶段）
   - spec 合规后，分发 **code quality reviewer subagent**（第二阶段）
   - 两阶段 review 都通过后，标记 Task 完成
4. **反"Should I continue"原则**：用户要求执行就执行到底，不停止问"是否继续"

**执行约束**：
- 每个 subagent 不继承 session context，主 agent 构造精确指令
- 只有三种停止原因：BLOCKED（无法解决）、真正的歧义、全部完成
- 使用 TodoWrite 工具维护任务状态列表，checkbox 实时更新

---

### 2.4 Review 阶段（两阶段双轴评审）

**评审模板（code-reviewer.md）**：
```markdown
## What Was Implemented
{DESCRIPTION}

## Requirements / Plan
{PLAN_OR_REQUIREMENTS}

## Git Range to Review
Base: {BASE_SHA}
Head: {HEAD_SHA}

## What to Check
Plan alignment:
- Does the implementation match the plan / requirements?
- Are deviations justified improvements, or problematic departures?
- Is all planned functionality present?

Code quality:
- Clean separation of concerns?
- Proper error handling?
- Type safety where applicable?
- DRY without premature abstraction?
- Edge cases handled?

Architecture:
- Sound design decisions? Reasonable scalability and performance?
- Security concerns?
- Integrates cleanly with surrounding code?

Testing:
- Tests verify real behavior, not mocks?
- Edge cases covered? Integration tests where they matter?
- All tests passing?
```

**评审输出格式**：
```
Strengths: ...
Issues:
  Critical: [issue with file:line]
  Important: [issue with file:line]
  Minor: [issue with file:line]
Recommendations: ...
Assessment: [Ready to proceed / Needs fixes]
```

**两阶段独立 Review（Subagent-Driven）**：
1. **Spec Compliance Review** — 检查实现是否符合 spec（缺了什么、多了什么）
2. **Code Quality Review** — 检查代码质量（架构、测试、安全等）
3. 两阶段独立运行，互不污染上下文

**评审反馈接收协议（receiving-code-review）**：
1. READ — 完整阅读反馈，不急于反应
2. UNDERSTAND — 用自己的话复述需求
3. VERIFY — 验证建议是否正确（先 grep 代码库，而非默认接受）
4. ACT — Critical 立即修、Important 赶紧修、Minor 记下来
5. PUSH BACK — 有理有据地反驳错误建议

**关键规则**：
- 分类必须准确：不要把 nitpick 标为 Critical
- 每个问题必须有 file:line 定位
- 必须说明 WHY 每个问题重要
- 必须给出明确 verdict（Ready / Needs fixes）

---

## 3. 工程架构设计

### 3.1 基于 Git Worktrees 的物理沙箱
最突出的工程亮点。技能组中包含核心的 `using-git-worktrees`，在任务执行前，自动通过 Git 创建独立物理目录作为 Worktree：
- **保证主线永远干净**：baseline 始终在通过测试的状态
- **零风险沙箱**：AI 把当前 Worktree 改坏了，直接废弃，不污染主干

### 3.2 批量下发与检查点并行策略
在 `executing-plans` 模块下，允许按批次派发多个子 Agent。但工程上强制注入"断点（Checkpoints）"，每一个子系统完成一个小闭环，必须在特定关卡等待主验证循环或人类放行信号。

### 3.3 两阶段审查制
实现 `subagent-driven-development` 的核心秘密：
- **第一阶段**：调用廉价高速模型跑"Spec Compliance 检查"，只检验是否漏了需求
- **第二阶段**：调用高智力模型和静态规则跑"Code Quality 检查"
- 任何阶段异常都会冻结流水线，杜绝烂代码越积越多

---

## 4. 核心实现技巧

### 4.1 无测试代码自动焚毁机制
异常严苛的自动纠偏逻辑：如果监控日志发现模型在未生成对应单元测试前就写了业务实现代码，利用 Git 还原或直接删除这些"越界"的业务代码，强行倒逼模型必须优先写测试（TDD 强制化）。

### 4.2 四步系统级排障法则（systematic-debugging）
固化了 4 步诊断法则：
1. **根因追踪**（root-cause-tracing）
2. **构建纵深防御**（defense-in-depth）
3. **基于条件的等待注入**（condition-based-waiting）
4. **修复确认**（verification-before-completion）

不是泛泛的 Prompt，而是包含大量针对竞态条件、死锁、状态不一致的诊断探测脚本集成。

### 4.3 "证据先于声明"原则（Evidence Before Claims）
任何"完成了""通过了"的声明，必须附有刚刚运行过的命令输出作为证据。AI 不得使用"应该能行""看起来对了"等推测性语言。无验证证据的完成声明视为无效输出。

### 4.4 Anti-Rationalization 机制
在关键决策点预设 AI 常见偷懒借口和反驳表，防止 agent 跳过关键步骤（如绕过 TDD、绕过 spec review 等）。

### 4.5 评审接收防表演性认同协议
外部评审建议视为"待验证"而非命令：
- 先 grep 代码库确认是否真的被使用（YAGNI 检查）
- 不清楚的条目必须全部澄清后才开始实现
- 实现前独立验证技术正确性
- 禁止"你说得对，我马上改"式的表演性认同回应

---

## 5. Spec 字段对比优势

在各框架横向对比中，Superpowers 的独特性体现在：
| 能力 | Superpowers | 其他框架 |
|------|:-----------:|:-------:|
| Spec 自审核 | ✅（唯一内建） | 全部没有 |
| 方案对比强制 | ✅（2-3 个） | 仅 gstack 有 |
| 范围蔓延检测 | ✅ | 其他仅部分有 |
| 两阶段并行 Review | ✅ | 仅 Matt Pocock review 有 |
| 评审反馈处理协议 | ✅（唯一） | 全部没有 |
| TDD 强制循环 | ✅（强制，违反则删代码） | 其他是建议 |

---

## 6. 对我们框架的借鉴价值

| 借鉴点 | 优先级 | 对应我们的设计 |
|-------|--------|--------------|
| HARD-GATE 门控机制 | ✅ 已纳入 | §二核心原则 + 双层阶段门控 |
| Spec 自审核循环 | ✅ 已纳入 | Review 阶段门控自审核 6 项检查 |
| 两阶段独立 Review | ⭐⭐⭐ 高优 | Review Skill 三轴审查可借鉴双轴隔离 |
| 评审接收协议 | ⭐⭐⭐ 高优 | P3-11 评审接收协议（已规划） |
| 系统性调试四阶段 | ⭐⭐⭐ 高优 | P2-7 系统性调试协议（已规划） |
| 证据先于声明 | ✅ 已纳入 | §二核心原则 #7 |
| Plan 精确到行号 | ⭐⭐ 中优 | Execute Skill 切片文件引用格式 |
| Git Worktree 沙箱 | ⭐ 长期 | 执行沙箱隔离（v2.0+） |
