# 竞品 Spec 模板对比分析 — Plan / Execute / Review 阶段

> 日期：2026-05-17
> 目的：对比分析 7 个 AI 编程框架 + Matt Pocock Skills 在 Plan、Execute、Review 三个阶段的文档输出规范，为 AI-Native-SDLC 对应阶段的 Spec 模板设计提供输入
> 方法：结合已有研究报告 + 在线研究各框架 GitHub 仓库的实际模板文件 + Matt Pocock Skills 本地文件

---

## 一、Plan 阶段（方案设计 + 任务拆分）

### 1.1 各框架的输出规范详解

---

#### Superpowers — writing-plans skill

**输出文件：** `docs/superpowers/plans/<filename>.md`

**模板结构：**

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

**流程特征：**
1. **Scope Check** — 先检查复杂度，简单需求可能不需要完整计划
2. **TDD 内嵌** — 每个 Task 强制"写测试→验证失败→实现→验证通过→提交"的五步循环
3. **No Placeholders** — 禁止 TBD/TODO，所有代码必须完整可执行
4. **自审核** — 写完计划后做 cross-reference 检查（函数名在 Task 3 和 Task 7 是否一致）
5. **Execution Handoff** — 保存后提供两种执行模式选择：Subagent-Driven（逐任务分发）或 Inline Execution（批量执行）

**独特字段：**
- 每个 Step 包含完整可执行代码（不是描述而是实际代码）
- 每个文件精确到行号（`Modify: path/file.py:123-145`）
- TDD 循环嵌入到每个 Task（红→绿→提交）
- 执行模式选择（Subagent vs Inline）
- 计划即 Prompt（plan 本身就是给下游 agent 的指令）

---

#### SpecKit — plan-template.md + tasks-template.md

**输出文件：** `specs/<feature>/research.md` + `data-model.md` + `contracts/` + `quickstart.md` + `tasks.md`

**Plan 模板结构：**

```markdown
# Implementation Plan: [FEATURE]
\Branch\: `[###-feature-name]` | \Date\: [DATE] | \Spec\: [link]
\Input\: Feature specification from `/specs/[###-feature-name]/spec.md`

## Summary
[Extract from feature spec: primary requirement + technical approach]

## Technical Context
\Language/Version\: [e.g., Python 3.11]
\Primary Dependencies\: [e.g., FastAPI]
\Storage\: [e.g., PostgreSQL]
\Testing\: [e.g., pytest]
\Target Platform\: [e.g., Linux server]
\Project Type\: [e.g., web-service]
\Performance Goals\: [e.g., 1000 req/s]
\Constraints\: [e.g., <200ms p95]
\Scale/Scope\: [e.g., 10k users]

## Constitution Check
GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.

## Project Structure
[实际目录树]

## Complexity Tracking
> Fill ONLY if Constitution Check has violations that must be justified
| Violation | Why Needed | Simpler Alternative Rejected Because |
```

**Tasks 模板结构：**

```markdown
# Tasks: [FEATURE]

## Path Conventions
- Source: `src/[location]/`
- Tests: `tests/[location]/`
- Config: `[config location]`

## Phase 1: Foundation
- [ ] T001 Setup project structure and build system
- [ ] T002 Configure CI/CD pipeline
- [ ] T003 Setup linting, formatting, and type checking
...
Checkpoint: Foundation ready

## Phase 3: User Story 1 - [Title] (Priority: P1) 🎯 MVP
- [ ] T015 [US1] Implement [endpoint/feature] in src/[location]/[file].py
- [ ] T016 [US1] Add validation and error handling
- [ ] T017 [US1] Add logging for user story 1 operations
...
```

**流程特征：**
1. **Phase 0: Research** — 先做技术研究，解决所有 `[NEEDS CLARIFICATION]`
2. **Phase 1: Design** — 产出 data-model.md、contracts/、quickstart.md
3. **Phase 2: Tasks** — 基于 design 产出原子化任务列表
4. **Constitution Check** — 计划和设计阶段必须通过项目宪法检查
5. **每任务有 Phase 分组** — Foundation → User Story 1 → User Story 2 → ...

**独特字段：**
- Technical Context 九元组（Language/Dependencies/Storage/Testing/Platform/Type/Performance/Constraints/Scale）
- Constitution Check 门控（计划前+设计后各检一次）
- Complexity Tracking 表（违反宪法时必须说明理由）
- Phase 分组 + Checkpoint（里程碑检查点）
- 任务标签 `[P]` = 并行可执行、`[US1]` = 归属 User Story
- research.md + data-model.md + contracts/ 多产出文件

---

#### GSD — PLAN.md (gsd-planner)

**输出文件：** `.planning/phases/<N>-<name>/PLAN.md`

**模板结构（含 XML 标签）：**

```markdown
---
phase: 2
plan: 1
title: User Registration Endpoint
dependencies: []
estimated_tasks: 4
autonomous: true
files_modified:
  - src/routes/auth.ts
  - src/models/user.ts
must_haves:
  - Working registration endpoint
  - Email validation
  - Password hashing
---

# Plan 01: User Registration Endpoint

<objective>
[What this plan accomplishes]
Purpose: [Why this matters]
Output: [Artifacts created]
</objective>

<execution_context>
@~/.claude/get-shit-done/workflows/execute-plan.md
@~/.claude/get-shit-done/templates/summary.md
</execution_context>

<context>
@.planning/PROJECT.md
@.planning/ROADMAP.md
@.planning/STATE.md
</context>

<tasks>
<task type="auto">
  <name>Task 1: [Action-oriented name]</name>
  <files>path/to/file.ext</files>
  <action>[Specific implementation]</action>
  <verify>[Command or check]</verify>
  <done>[Acceptance criteria]</done>
</task>
</tasks>

<threat_model>
## Trust Boundaries
...
</threat_model>
```

**流程特征：**
1. **Goal-Backward 方法论** — 从目标倒推 truth → artifact → wiring
2. **YAML Frontmatter** — 结构化元数据（phase/plan/dependencies/autonomous/must_haves）
3. **XML 标签任务结构** — 每个 task 有 name/files/action/verify/done 五要素
4. **Wave-based 并行** — 无依赖的 PLAN 可并行执行
5. **决策保真** — 每个任务引用决策 ID（如 D-03），锁定决策不可违反
6. **Threat Model** — 每个 PLAN 包含信任边界分析
7. **上下文预算** — 每个 PLAN 控制在 ~50% context 内

**独特字段：**
- XML 结构化任务（name/files/action/verify/done）
- YAML Frontmatter（dependencies/autonomous/must_haves/files_modified）
- Goal-Backward 验证
- 决策保真（引用 D-01, D-02 等决策 ID）
- Threat Model 信任边界
- 上下文预算控制
- 每个 Task 有 type="auto" 或 type="human-verify"
- Checkpoint 类型（human-verify/inline）

---

#### BMAD — architecture.md + epics/stories

**输出文件：** `_bmad-output/planning-artifacts/architecture.md` + `epics/` + `stories/`

**Architecture 模板结构：**

```markdown
# Architecture: [Project Name]

## High-Level Architecture
[Architecture pattern and key components]

## Tech Stack
[Language, Framework, Database, etc.]

## Data Model
[Entity relationships, key data structures]

## API Design
[Endpoint contracts, authentication]

## Component Design
[Module responsibilities, boundaries]

## Integration Points
[External services, APIs]

## Security Considerations
[Auth, authorization, data protection]

## Performance Considerations
[Caching, optimization strategies]

## DevOps & Deployment
[CI/CD, environments, monitoring]
```

**Epic 文件结构：**
```markdown
# Epic 1: User Authentication & Store Setup
## Description
## Business Value
## Stories
- Story 1.1: User Registration
- Story 1.2: Login
```

**Story 文件结构：**
```markdown
# Story 1.1: User Registration and Store Setup
**As a** [user type], **I want** [goal], **so that** [benefit]
**Priority:** P0
**Story Points:** 5
**Acceptance Criteria:**
- Given/When/Then
**Dependencies:** [Other stories]
**Implementation Notes:** [Technical guidance]
**Testing Checklist:**
- [ ] Manual test: ...
- [ ] Verify edge case: ...
```

**流程特征：**
1. **Architecture → Epics → Stories 三层拆分** — 从架构到可执行故事
2. **Implementation Readiness Check** — 进入实现前验证 PRD + UX + Architecture + Stories 一致性
3. **Story-level Testing Checklist** — 每个故事带独立的手动测试清单
4. **12 步 Architecture Workflow** — 通过微文件架构逐步引导
5. **Context Sharding** — 将大文档拆分为原子 story 文件，agent 只加载当前 story

**独特字段：**
- 三层产出（Architecture → Epic → Story）
- Implementation Readiness Check 门控
- Story-level Testing Checklist
- Context Sharding（按需加载）
- Sprint Status YAML 跟踪

---

#### OpenSpec — design.md + tasks.md

**输出文件：** `openspec/changes/<change-name>/design.md` + `tasks.md`

**design.md 结构：**
```markdown
# Design: [Change Name]

## Technical Approach
[Architecture decisions, key design patterns]

## Components
[New/modified components and their responsibilities]

## Data Flow
[How data moves through the system]

## API Changes
[New/modified endpoints or interfaces]

## Migration Strategy
[How to transition from current to new state]
```

**tasks.md 结构：**
```markdown
# Tasks: [Change Name]

- [ ] Task 1: [Description]
  - Files: `path/to/file1.ext`, `path/to/file2.ext`
  - Details: [Specific implementation guidance]
- [ ] Task 2: [Description]
  ...
```

**流程特征：**
1. **Change-based 组织** — 以变更为单位，不是以功能为单位
2. **Delta Spec 伴生** — design.md 旁边有 delta spec（ADDED/MODIFIED/REMOVED）
3. **非线性** — 随时可以 update design、archive change
4. **极简模板** — 没有 Constitution Check 等重量级门控

**独特字段：**
- 与 Delta Spec 伴生（增量规范）
- Change-based 文件组织
- 极简主义（模板最轻）

---

#### gstack — 多维评审

**输出文件：** `~/.gstack/projects/<project>/` 下多份评审文件

**Plan 阶段相关 skill：**
- `/plan-eng-review` — 工程评审（Architecture + Data Flow + ASCII Diagrams + Edge Cases + Test Matrix + Failure Modes + Security Concerns）
- `/plan-ceo-review` — CEO 评审（产品价值视角，4 种模式：Expansion / Selective Expansion / Hold Scope / Reduction）
- `/plan-design-review` — 设计评审
- `/plan-devex-review` — DX 评审
- `/autoplan` — 一键跑完四种评审

**独特字段：**
- 多维度评审（CEO / Design / Eng / DX 各视角独立评分）
- ASCII 图表（数据流图、状态机、错误路径）
- Failure Modes（失败模式分析）

---

#### specs.md — FIRE Flow

**输出文件：** `.specs-fire/intents/` + `walkthroughs/`

**FIRE Flow 结构：**
```
.fire-planner → Intent Capture + 工作项拆分
.fire-builder → 执行 + 自动生成 walkthrough
```

**design.md 结构（推断）：**
```markdown
# Design: [Feature Name]
## Overview
## Technical Approach
## Tasks
- [ ] Task 1
- [ ] Task 2
```

**独特字段：**
- 自适应检查点（0-2 基于复杂度）
- Intent 概念（而非 Feature）
- Walkthrough 自动生成

---

### 1.2 Plan 阶段横向对比表

| 字段/章节 | Superpowers | SpecKit | GSD | BMAD | OpenSpec | gstack | specs.md |
|-----------|:-----------:|:-------:|:---:|:----:|:--------:|:------:|:--------:|
| **技术上下文** | ❌ | ✅ (9 元组) | ✅ (context refs) | ✅ (Tech Stack) | ✅ (config.yaml) | ❌ | ❌ |
| **方案选项对比** | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ (Alternatives) | ❌ |
| **架构/组件设计** | ✅ (在 Task 中) | ✅ (Phase 1 产出) | ✅ (objective) | ✅ (Architecture doc) | ✅ (design.md) | ✅ (Eng Review) | ✅ |
| **数据模型** | ❌ | ✅ (data-model.md) | ❌ | ✅ | ❌ | ✅ (Data Flow) | ❌ |
| **API/接口合约** | ❌ | ✅ (contracts/) | ✅ (Interface-First) | ✅ | ✅ | ❌ | ❌ |
| **任务拆分** | ✅ (Task N) | ✅ (Phase+Task) | ✅ (XML task) | ✅ (Epic→Story) | ✅ (checkbox) | ❌ | ✅ |
| **任务包含代码** | ✅ (完整代码块) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **文件路径标注** | ✅ (精确到行号) | ✅ (Path Conventions) | ✅ (files) | ❌ | ✅ | ❌ | ❌ |
| **TDD 循环嵌入** | ✅ (5 步) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **依赖图** | ❌ | ❌ | ✅ (dependencies) | ✅ | ❌ | ❌ | ❌ |
| **验证命令** | ✅ (每步有) | ❌ | ✅ (verify) | ❌ | ❌ | ❌ | ❌ |
| **Constitution Check** | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **威胁模型** | ❌ | ❌ | ✅ | ❌ | ❌ | ✅ (Security) | ❌ |
| **复杂度追踪** | ❌ | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **YAML/结构化元数据** | ❌ | ❌ | ✅ (frontmatter) | ❌ | ✅ (.openspec.yaml) | ❌ | ✅ (state.yaml) |
| **自审核** | ✅ (cross-ref) | ❌ | ❌ | ✅ (IR Check) | ❌ | ❌ | ❌ |
| **上下文预算** | ❌ | ❌ | ✅ (~50% context) | ❌ | ❌ | ❌ | ❌ |
| **多维度评审** | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ (4 视角) | ❌ |
| **门控节点** | ❌ | ✅ (review-plan) | ❌ | ✅ (IR Check) | ❌ | ❌ | ✅ (自适应) |
| **增量/Delta** | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |

---

## 二、Execute 阶段（逐切片执行）

### 2.1 各框架的执行规范详解

---

#### Superpowers — executing-plans / subagent-driven-development

**两种执行模式：**

**模式 A：executing-plans（单 Agent 内联执行）**
1. Load and review plan — 加载计划文件，审查关键缺失
2. Execute tasks — 逐 Task 执行，每个 Task 包含：加载 → 理解 → 实现 → 运行验证 → 标记完成
3. Complete development — 全部完成后总结

**模式 B：subagent-driven-development（多 Agent 分发执行）**
1. 读取 plan 文件一次，提取所有 task 全文
2. 创建 TodoWrite 跟踪列表
3. 对每个 Task：
   - 分发独立 implementer subagent（带完整 task text + context）
   - 实现完成后，分发 spec compliance reviewer subagent
   - spec 合规后，分发 code quality reviewer subagent
   - 两阶段 review 都通过后，标记 Task 完成
4. 不停止问"是否继续"——用户要求执行就执行到底

**关键规则：**
- 每个 subagent 不继承 session context，主 agent 构造精确指令
- 只有三种停止原因：BLOCKED（无法解决）、真正的歧义、全部完成
- "Should I continue?" 提示是反模式，浪费时间

**执行状态追踪：**
- 使用 TodoWrite 工具维护任务状态列表
- 每个 task checkbox 实时更新

---

#### SpecKit — implement command

**执行流程：**
1. 加载 tasks.md
2. 按 Phase 分组执行
3. 每个 Phase 内可并行执行 `[P]` 标记的任务
4. 在 Checkpoint 处暂停验证

**执行约束：**
- Verify tests fail before implementing（验证测试先失败）
- Commit after each task or logical group
- Stop at any checkpoint to validate story independently

---

#### GSD — execute-phase

**执行流程：**
1. 读取 PLAN.md（含 YAML frontmatter + XML tasks）
2. 每个 PLAN 分发给独立 executor subagent（fresh 200k context）
3. 每个 Task 按 XML 结构执行：
   - `<action>` — 具体实现
   - `<verify>` — 运行验证命令
   - `<done>` — 确认验收标准
4. Wave-based 并行：无依赖的 PLAN 并行执行

**执行上下文管理：**
- 每个 executor subagent 获得 fresh context
- 通过 `@` 引用机制加载必要文件
- SUMMARY.md 记录执行结果供后续 phase 引用

---

#### BMAD — dev-story

**执行流程：**
1. 加载 Story 文件（sharded，只加载当前 story）
2. 实现 → 自测 → 标记 "Ready for Review"
3. 运行 Testing Checklist
4. 完成后生成 QA Report

**执行特点：**
- Context Sharding — agent 只加载当前 story 的上下文
- Testing Checklist 内嵌在 story 文件中
- Story status 状态机：In Progress → Ready for Review → Done

---

#### OpenSpec — /opsx:apply

**执行流程：**
1. 读取 tasks.md（checkbox list）
2. 逐项实现
3. 完成后归档（/opsx:archive）— 自动 merge delta specs 到 main specs

**极简主义：** 没有复杂的执行状态机，checkbox 完成即表示 done。

---

#### specs.md — FIRE Builder

**执行流程：**
1. FIRE Builder 接收 planner 的工作项
2. 逐项执行
3. 每项完成后自动生成 Walkthrough（变更文档化）
4. 自适应检查点：0-2 个检查点基于复杂度

---

### 2.2 Execute 阶段横向对比表

| 执行特征 | Superpowers | SpecKit | GSD | BMAD | OpenSpec | specs.md |
|---------|:-----------:|:-------:|:---:|:----:|:--------:|:--------:|
| **执行粒度** | Task | Task (Phase 分组) | Task (Wave 分组) | Story | Task (checkbox) | 工作项 |
| **并行支持** | ✅ (subagent) | ✅ ([P] 标记) | ✅ (Wave-based) | ❌ (串行 story) | ❌ | ❌ |
| **TDD 内嵌** | ✅ (红绿循环) | ⚠️ (建议) | ⚠️ (支持) | ❌ | ❌ | ❌ |
| **执行中 Review** | ✅ (两阶段) | ❌ | ❌ | ❌ | ❌ | ❌ |
| **状态追踪** | TodoWrite | checkbox | STATE.md | status 字段 | checkbox | state.yaml |
| **上下文管理** | 构造精确指令 | 无 | fresh 200k | Sharding | 无 | 无 |
| **验证命令** | ✅ (每步有) | ❌ | ✅ (verify) | ❌ | ❌ | ❌ |
| **自动文档化** | ❌ | ❌ | ✅ (SUMMARY.md) | ❌ | ✅ (archive) | ✅ (Walkthrough) |
| **提交策略** | 每个 Task 提交 | 每个 Task/逻辑组 | 每个 Task | 每个 Story | 灵活 | 灵活 |
| **失败处理** | 停止并问 | 继续/回退 | BLOCKED 标记 | 标记问题 | 人工介入 | 人工介入 |

---

## 三、Review 阶段（评审）

### 3.1 各框架的评审规范详解

---

#### Superpowers — requesting-code-review

**评审方式：** 分发独立 code reviewer subagent

**评审模板（code-reviewer.md）：**

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
- Sound design decisions?
- Reasonable scalability and performance?
- Security concerns?
- Integrates cleanly with surrounding code?

Testing:
- Tests verify real behavior, not mocks?
- Edge cases covered?
- Integration tests where they matter?
- All tests passing?
```

**评审输出格式：**
```
Strengths: ...
Issues:
  Critical: [issue with file:line]
  Important: [issue with file:line]
  Minor: [issue with file:line]
Recommendations: ...
Assessment: [Ready to proceed / Needs fixes]
```

**Subagent-Driven 的两阶段 Review：**
1. **Spec Compliance Review** — 检查实现是否符合 spec（缺了什么、多了什么）
2. **Code Quality Review** — 检查代码质量（架构、测试、安全等）
3. 两阶段独立运行，互不污染上下文

**评审反馈处理（receiving-code-review）：**
1. READ — 完整阅读反馈，不急于反应
2. UNDERSTAND — 用自己的话复述需求
3. VERIFY — 验证建议是否正确
4. ACT — Critical 立即修、Important 赶紧修、Minor 记下来
5. PUSH BACK — 有理有据地反驳错误建议

**关键规则：**
- 分类必须准确：不要把 nitpick 标为 Critical
- 每个问题必须有 file:line
- 必须说明 WHY 每个问题重要
- 必须给出明确 verdict

---

#### SpecKit — review-spec gate + checklist-template.md

**评审方式：** 工作流中的 gate 节点

**评审维度：**
- spec → plan 之间的门控：plan 是否完整覆盖 spec？
- Constitution Check：plan 是否违反项目宪法？

**Checklist 模板：**
```markdown
# Review Checklist: [Feature]
## Spec Coverage
- [ ] All FR-001..N have corresponding tasks
- [ ] All user stories have implementation tasks
- [ ] All success criteria are verifiable

## Constitution Compliance
- [ ] No violations of project principles
- [ ] Or violations are justified in Complexity Tracking

## Plan Quality
- [ ] Tasks are atomic and independently testable
- [ ] Dependencies are correctly ordered
- [ ] No placeholders or TBDs
```

---

#### GSD — verify-phase (gsd-verifier)

**评审方式：** 三层验证 + 人工确认

**验证流程：**
1. **Step 0: Check for Previous Verification** — 检查是否为重验，有则优化策略
2. **Step 1: Load Context** — 加载 PLAN + ROADMAP + STATE
3. **Step 2: Parse Success Criteria** — 从 ROADMAP 提取 must-pass 条件
4. **Step 3: Verify Must-Haves** — 每个必须项是否有对应实现
5. **Step 4: Verify Artifacts (Three Levels)**
   - Level 1: Exists — 文件是否存在
   - Level 2: Substantive — 内容是否充实（不只是空壳）
   - Level 3: Wired — 是否正确连接到系统中
6. **Step 5: Run Automated Checks** — `<verify><command>` 块中的命令
7. **Step 6: Human Verification** — `<verify><human-check>` 块中需要人工确认的项目
8. **Step 7: Generate VERIFICATION.md**

**VERIFICATION.md 结构：**
```markdown
---
phase: 2
status: passed | failed | partial
gaps:
  - must_have: "... "
    issue: "..."
    severity: critical | important | minor
overrides: []
---
# Verification Report: Phase 2

## Summary
[PASS/FAIL/PARTIAL] - X/Y must-haves verified

## Must-Have Verification
| Must-Have | Status | Evidence |
|-----------|--------|----------|
| ... | ✅ | ... |
| ... | ❌ | ... |

## Artifact Verification
| Artifact | Exists | Substantive | Wired |
|----------|--------|-------------|-------|
| ... | ✅ | ✅ | ✅ |

## Human Verification Items
- [ ] [test] What to do → [expected] What should happen
```

**独特特征：**
- 三层验证（Exists / Substantive / Wired）是所有框架中最细致的验证方法
- Override 机制：允许对失败项提交偏差接受说明
- 自动+人工双轨验证
- 重验优化：失败项全量验证，通过项只做回归检查

---

#### BMAD — bmad-code-review

**评审方式：** Dev Agent 完成后运行 code-review skill

**评审维度：**
- Code Quality Checklist
- Security Checklist
- Test Strategy Template

**Story 完成检查：**
```markdown
# QA Report: Story 1.1
## Test Results
- [x] Unit tests: X/X passing
- [x] Integration tests: X/X passing
- [ ] Manual testing checklist:
  - [x] Test case 1
  - [ ] Test case 2 (failed: ...)

## Code Quality
- [ ] Follows coding standards
- [ ] No security issues
- [ ] Error handling complete

## Implementation Readiness
- [ ] Story matches architecture spec
- [ ] No scope creep
```

**Implementation Readiness Check（进入实现前的门控）：**
验证 PRD + UX + Architecture + Epics/Stories 四份文档的一致性。

---

#### OpenSpec — 归档验证

**评审方式：** archive 时自动验证

**验证维度：**
- Delta specs 是否正确反映了变更
- 所有 tasks.md checkbox 是否完成
- 归档时自动 merge delta specs 到 main specs

**极简主义：** 没有独立的 review 阶段，验证嵌入在归档过程中。

---

#### gstack — /review + /qa

**评审方式：** 多个独立 review skill

**核心评审 skill：**
- `/review` — 通用代码评审
- `/design-review` — 设计评审
- `/qa` — QA 验证（含浏览器自动化）
- `/cso` — 安全审计（OWASP Top 10 + STRIDE）

**独特特征：**
- 浏览器自动化 QA — 用 Playwright 驱动真实浏览器做验证
- OWASP Top 10 + STRIDE 安全审计
- 多维度评审（Eng + Design + DX 各视角）

---

#### specs.md — 自适应检查点

**评审方式：** 内嵌在 FIRE Builder 执行流程中

**检查点策略：**
- 简单任务：0 个检查点（全自动）
- 中等任务：1 个检查点
- 复杂任务：2 个检查点

---

#### Matt Pocock Skills — review skill（两轴评审）

**评审方式：** 两个并行 subagent 独立运行

**两轴：**
1. **Standards** — 代码是否符合项目文档中的编码标准？
2. **Spec** — 代码是否忠实实现了原始 issue/PRD/spec？

**Standards subagent 检查来源：**
- CLAUDE.md / AGENTS.md
- CONTRIBUTING.md
- CONTEXT.md / CONTEXT-MAP.md
- docs/adr/（架构决策记录）
- .editorconfig, eslint.config.*, biome.json, prettier.config.*, tsconfig.json（工具强制的标准，只记录不重复检查）

**Spec subagent 检查：**
- (a) spec 要求的但缺失/部分实现的功能
- (b) diff 中出现但 spec 没要求的（范围蔓延）
- (c) 看起来实现了但实现有误的需求
- 每个发现引用 spec 原文

**输出格式：**
```markdown
## Standards
[Standards subagent 报告，每个违规引用标准文件+规则]

## Spec
[Spec subagent 报告，每个发现引用 spec 原文]

---
Summary: Standards: X findings | Spec: Y findings | Worst: [最严重问题]
```

**核心洞察：** 代码可以过一个轴但挂另一个——完全符合标准但实现错误的东西，或完全满足 spec 但破坏项目约定。分开报告防止一个轴掩盖另一个。

---

#### Matt Pocock Skills — 其他与 Review 相关的 skill

**diagnose（调试循环）：**
- Phase 1: Build feedback loop（构建反馈循环）
- Phase 2: Reproduce（确认复现）
- Phase 3: Hypothesise（生成 3-5 个可证伪假设，排序展示给用户）
- Phase 4: Instrument（单变量探测，tagged debug logs）
- Phase 5: Fix + regression test（先写回归测试再修）
- Phase 6: Cleanup + post-mortem（清理 + "什么能防止这个 bug 再出现"）

**improve-codebase-architecture（架构评审）：**
- 探索代码库，找浅模块（interface ≈ implementation 复杂度）
- 应用 Deletion Test（删掉它会怎样？复杂度消失还是分散？）
- 提出深化建议（refactor candidates）
- 用 CONTEXT.md 领域词汇和 LANGUAGE.md 架构词汇
- 交互式 grilling 确认每个候选

**grill-with-docs（拷问式审查）：**
- 领域词汇冲突检测（术语与 CONTEXT.md 矛盾立即指出）
- 模糊语言尖锐化（"account" → Customer 还是 User？）
- 具体场景压力测试
- 代码交叉引用验证
- 内联更新 CONTEXT.md + 生成 ADR

**triage（分流）：**
- Issue 状态机：needs-triage → needs-info → ready-for-agent / ready-for-human / wontfix
- Bug 先尝试复现再分级
- Agent Brief 模板（给 AFK agent 的自包含指令）

---

### 3.2 Review 阶段横向对比表

| 评审特征 | Superpowers | SpecKit | GSD | BMAD | OpenSpec | gstack | specs.md | Matt Pocock review |
|---------|:-----------:|:-------:|:---:|:----:|:--------:|:------:|:--------:|:-----------------:|
| **Spec 合规检查** | ✅ (第一阶段) | ✅ (gate) | ✅ (must-haves) | ✅ (IR Check) | ❌ | ❌ | ❌ | ✅ (Spec 轴) |
| **代码质量检查** | ✅ (第二阶段) | ❌ | ❌ | ✅ | ❌ | ✅ | ❌ | ✅ (Standards 轴) |
| **问题分级** | ✅ (C/I/M) | ❌ | ✅ (severity) | ❌ | ❌ | ❌ | ❌ | ⚠️ (区分硬违规/判断) |
| **引用 spec 原文** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **引用标准文件** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **三件验收** | ❌ | ❌ | ✅ (3 级) | ❌ | ❌ | ❌ | ❌ | ❌ |
| **范围蔓延检测** | ✅ (extra) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **安全审计** | ⚠️ | ❌ | ✅ (threat model) | ❌ | ❌ | ✅ (OWASP) | ❌ | ❌ |
| **测试质量审查** | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ | ❌ | ⚠️ |
| **并行双轴** | ✅ (两阶段) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **自动修复建议** | ⚠️ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **明确 verdict** | ✅ | ✅ (gate pass/fail) | ✅ (PASS/FAIL) | ❌ | ❌ | ❌ | ❌ | ✅ |
| **人工验证项** | ✅ | ✅ | ✅ (human-check) | ✅ (manual test) | ❌ | ✅ | ✅ (自适应) | ❌ |
| **评审反馈处理** | ✅ (receiving skill) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

---

## 四、Matt Pocock Skills 的独特贡献

### 4.1 Plan 阶段可借鉴

| Skill | 可借鉴点 | 借鉴价值 |
|-------|---------|---------- |
| **to-issues** | 垂直切片拆分（tracer bullet）——每个 issue 贯穿所有层，可独立演示/验证 | ⭐⭐⭐ 与我们的"垂直切片粒度"设计直接对齐 |
| **to-issues** | HITL/AFK 标记——区分需要人工交互和可以自动执行的切片 | ⭐⭐ 帮助规划哪些切片需要人确认 |
| **to-issues** | Blocked by 依赖声明 | ⭐⭐ 执行排序的依据 |
| **to-issues** | Issue Body 模板（Parent + What to build + Acceptance criteria + Blocked by） | ⭐⭐⭐ 标准化的切片描述格式 |
| **to-prd** | Implementation Decisions 章节——记录模块、接口、schema、API 合约 | ⭐⭐⭐ 把"做了什么设计决策"显式记录 |
| **to-prd** | Testing Decisions 章节——明确哪些模块测试、什么算好测试 | ⭐⭐⭐ 测试策略前置到 Plan 阶段 |
| **to-prd** | 深模块识别——主动寻找"小接口、大实现"的提取机会 | ⭐⭐ Plan 阶段就考虑可测试性 |
| **grill-with-docs** | CONTEXT.md 领域词汇表——所有产出使用统一术语 | ⭐⭐⭐ 与我们"统一语言"设计决策完全一致 |
| **grill-with-docs** | ADR 模板——记录不可逆、令人意外、真正权衡过的决策 | ⭐⭐⭐ 方案设计中的决策记录格式 |
| **grill-with-docs** | 拷问式审查——逐分支走设计树，逐决策确认 | ⭐⭐ Plan 阶段可以借鉴这种交互方式 |

### 4.2 Execute 阶段可借鉴

| Skill | 可借鉴点 | 借鉴价值 |
|-------|---------|---------- |
| **tdd** | 垂直切片 tracer bullet——一个测试→一个实现→重复，不是水平切片（先写所有测试再写所有实现） | ⭐⭐⭐ 直接反对"先写全量测试"的反模式 |
| **tdd** | Anti-Pattern 声明："DO NOT write all tests first, then all implementation" | ⭐⭐⭐ 对齐我们的"逐切片串行"设计 |
| **tdd** | Per-cycle checklist（测试描述行为非实现、使用公共接口、能承受重构、代码最小化、无投机功能） | ⭐⭐⭐ 每个切片的执行质量标准 |
| **tdd** | Planning 步骤：确认接口变更→确认测试行为→识别深模块→设计可测试接口→列出行为清单→用户批准 | ⭐⭐ 每个切片开始前的准备工作 |
| **diagnose** | Phase 1: Build feedback loop——"这整个 skill 就是构建反馈循环"，花不均衡的努力在这里 | ⭐⭐⭐ 调试时的核心方法论 |
| **diagnose** | Phase 3: 3-5 个可证伪假设——"如果你无法陈述预测，那就是一个感觉，丢弃或尖锐化它" | ⭐⭐ 防止单假设锚定 |
| **diagnose** | Tagged debug logs (`[DEBUG-a4f2]`) ——清理变成一次 grep | ⭐⭐ 调试中的工程规范 |

### 4.3 Review 阶段可借鉴

| Skill | 可借鉴点 | 借鉴价值 |
|-------|---------|---------- |
| **review** | 两轴评审（Standards + Spec）——互不污染的并行检查 | ⭐⭐⭐ 与我们"代码质量+需求一致性+验收标准"三维度设计高度对齐 |
| **review** | Standards 轴引用项目文档（AGENTS.md, CONTRIBUTING.md, docs/adr/） | ⭐⭐ 代码质量检查有据可依 |
| **review** | Spec 轴分三类：缺失/范围蔓延/实现错误 | ⭐⭐⭐ 精确的问题分类 |
| **review** | 每个发现引用原文——不只是说"有问题"，而是指出 spec 哪一行 | ⭐⭐⭐ 可追溯的审查 |
| **review** | "一个轴 pass 另一个 fail"的核心洞察 | ⭐⭐ 防止一个维度掩盖另一个 |
| **improve-codebase-architecture** | Deletion Test（删掉它复杂度消失还是分散？） | ⭐⭐ Review 中判断模块是否有价值的方法 |
| **improve-codebase-architecture** | 浅模块识别（interface ≈ implementation） | ⭐⭐ Review 中发现架构问题的方法 |
| **grill-with-docs** | 领域词汇冲突检测 | ⭐⭐ Review 中检查术语一致性 |
| **triage** | Issue 状态机 | ⭐ Review 发现的问题如何分流处理 |
| **Superpowers receiving-code-review** | 评审反馈处理协议（READ→UNDERSTAND→VERIFY→ACT→PUSH BACK） | ⭐⭐⭐ 我们 Review 阶段产出如何被处理

---

## 五、综合建议

### 5.1 Plan Spec 模板建议

```markdown
# Plan Spec: [Feature Name]

## 元数据（必填）
\Spec ID\: PLAN-[XXX]
\Input Spec\: SPEC-[XXX]（关联的 Requirement Spec）
\Created\: [DATE]
\Status\: Draft | Under Review | Approved
\Flow Type\: Standard | Full

## 1. 技术上下文（必填）
\语言/版本\: [e.g., TypeScript 5.4]
\主要依赖\: [e.g., Next.js 14, Prisma]
\存储\: [e.g., PostgreSQL]
\测试框架\: [e.g., Vitest + Playwright]
\目标平台\: [e.g., Vercel]
\性能目标\: [e.g., <200ms p95]
\约束\: [e.g., 兼容旧 API]
> 借鉴 SpecKit Technical Context 9 元组，精简为 7 项

## 2. 技术方案（必填）
### 2.1 架构设计
[组件/模块关系 + 数据流——借鉴 BMAD Architecture + gstack ASCII 图表]

### 2.2 数据模型
[实体关系 + 关键数据结构——借鉴 SpecKit data-model.md]

### 2.3 接口合约
[API/函数签名——借鉴 GSD Interface-First + SpecKit contracts/]

### 2.4 实现决策
[模块划分、接口变更、技术澄清——借鉴 Matt Pocock to-prd 的 Implementation Decisions]

## 3. 切片列表（必填）

### Slice 1: [Name]（推荐第一个，端到端验证路径）
\Type\: AFK | HITL（借鉴 Matt Pocock to-issues）
\Blocked by\: None
\What to build\: [端到端行为描述，非逐层实现——借鉴 Matt Pocock to-issues]
\Acceptance criteria\:
- [ ] Criterion 1
- [ ] Criterion 2
\Files\: [涉及文件列表——借鉴 Superpowers]

### Slice 2: [Name]
\Type\: AFK
\Blocked by\: Slice 1
...

> 借鉴 Matt Pocock to-issues 的垂直切片模板 + Superpowers 的精确文件标注

## 4. 测试策略（必填）
\测试方法\: TDD（推荐）| 先实现后测试
\测试覆盖范围\: [哪些模块测试、什么算好测试——借鉴 Matt Pocock to-prd Testing Decisions]
\关键测试场景\: [必须通过的端到端测试]

> TDD 是推荐但不强制（与设计决策一致）

## 5. 风险与缓解（可选，Full 流程必填）
\技术风险\: ...
\缓解方案\: ...
> 借鉴 GSD Threat Model

## 6. 术语更新（Full 流程必填）
| 术语 | 定义 | 新增/修改 |
|------|------|----------|
> 借鉴 Matt Pocock grill-with-docs CONTEXT.md 内联更新

---

## 门控检查清单
- [ ] 技术方案与 Requirement Spec 一致
- [ ] 每个切片是端到端垂直切片，可独立验证
- [ ] 切片依赖关系正确，无循环依赖
- [ ] 接口合约已定义（Interface-First）
- [ ] 测试策略已明确
- [ ] 无 placeholder 残留
- [ ] 用户确认 ✓

## Spec Self-Review（自动检查，借鉴 Superpowers）
- [ ] 每个 Requirement Spec 的 FR 都有对应切片覆盖
- [ ] 切片之间的文件引用无矛盾
- [ ] 无超出 Requirement Spec 范围的隐含需求
```

### 5.2 Execute Spec 模板建议

Execute 阶段不产出独立 Spec 文件，而是在 Plan Spec 的切片列表中追踪状态。建议在每个切片内嵌执行状态：

```markdown
### Slice 1: [Name]
\Status\: Pending | In Progress | Done | Blocked
...
\Execution Log\:（完成后自动填充）
- [x] Step 1: [What was done]
- [x] Step 2: [What was done]
- [x] Verification: [Test command + result]
```

**执行原则（从竞品提炼）：**

1. **逐切片串行**（来自设计决策 + Matt Pocock tdd 反对水平切片）
2. **推荐 TDD 但不强制**（tracer bullet 模式：一个测试→一个实现→重复）
3. **每个切片完成后标记 Done**（借鉴 Superpowers checkbox 追踪）
4. **BLOCKED 时停止并说明原因**（借鉴 Superpowers executing-plans）
5. **不问"是否继续"**——用户要求执行就执行到底（借鉴 Superpowers subagent-driven）

### 5.3 Review Spec 模板建议

```markdown
# Review Spec: [Feature Name]

## 元数据（必填）
\Spec ID\: REV-[XXX]
\Plan Spec\: PLAN-[XXX]
\Requirement Spec\: SPEC-[XXX]
\Created\: [DATE]
\Status\: In Review | Done

## 1. Standards 检查（代码质量）
> 借鉴 Matt Pocock review Standards 轴 + Superpowers code-quality review

| # | 文件:行号 | 问题 | 严重性 | 标准来源 |
|---|----------|------|--------|----------|
| S1 | src/auth.ts:42 | 缺少错误处理 | Important | AGENTS.md: Error handling |
| S2 | src/api.ts:15 | Magic number | Minor | — |

## 2. Spec 合规检查（需求一致性）
> 借鉴 Matt Pocock review Spec 轴 + Superpowers spec-compliance review

### 2.1 缺失实现
| # | Spec 引用 | 描述 |
|---|-----------|------|
| P1 | FR-003 "System MUST validate email" | 缺少邮箱格式校验 |

### 2.2 范围蔓延
| # | Spec 引用 | 描述 |
|---|-----------|------|
| P2 | — | 新增了 --json flag（spec 未要求） |

### 2.3 实现错误
| # | Spec 引用 | 描述 |
|---|-----------|------|
| P3 | FR-005 "<200ms response" | 实测 p95=350ms |

## 3. 验收标准逐条确认
> 这是我们的独特维度——竞品中只有 Superpowers 两阶段 review 隐含了这一点

| 验收标准 | 状态 | 证据 |
|---------|------|------|
| SC-001: Given X When Y Then Z | ✅ 通过 | test_auth.py::test_register_pass |
| SC-002: Given A When B Then C | ❌ 未通过 | 边界条件未处理 |

## 4. 自动修复建议
> 这是我们的差异化——竞品中基本没有这个维度

| 问题 | 修复建议 | 自动可修 |
|------|---------|----------|
| S1: 缺少错误处理 | 添加 try-catch + 自定义错误类型 | ✅ |
| P1: 缺少邮箱校验 | 添加 validateEmail() 工具函数 | ✅ |
| P3: 响应超时 | 添加 Redis 缓存层 | ❌ 需人工判断 |

## 5. 综合评审结论
\Verdict\: Pass | Conditional Pass | Fail
\Conditions\: [Conditional Pass 时需满足的条件]
\Recommended Actions\: [优先修复项]

---

## 门控检查清单
- [ ] Standards 检查完成
- [ ] Spec 合规检查完成
- [ ] 验收标准逐条确认
- [ ] 所有 Critical / Important 问题已处理
- [ ] 用户确认 ✓
```

### 5.4 字段来源追溯

#### Plan Spec 字段来源

| 模板字段 | 主要借鉴来源 | 辅助借鉴 |
|---------|-------------|---------- |
| 技术上下文（7 元组） | SpecKit Technical Context | GSD context refs |
| 架构设计 | BMAD Architecture | gstack Eng Review（ASCII 图表） |
| 数据模型 | SpecKit data-model.md | BMAD |
| 接口合约 | GSD Interface-First | SpecKit contracts/ |
| 实现决策 | Matt Pocock to-prd | — |
| 切片列表（垂直切片） | Matt Pocock to-issues | Superpowers Task N |
| 切片 Type (AFK/HITL) | Matt Pocock to-issues | — |
| 切片 Blocked by | Matt Pocock to-issues | GSD dependencies |
| 文件路径标注 | Superpowers（精确到行号） | SpecKit Path Conventions |
| 测试策略 | Matt Pocock to-prd Testing Decisions | Superpowers TDD |
| 风险与缓解 | GSD Threat Model | — |
| 门控检查清单 | 我们的设计决策 | Superpowers HARD-GATE |
| Spec Self-Review | Superpowers 自审核 | — |

#### Review Spec 字段来源

| 模板字段 | 主要借鉴来源 | 辅助借鉴 |
|---------|-------------|---------- |
| Standards 检查 | Matt Pocock review（Standards 轴） | Superpowers code-quality review |
| 严重性分级（Critical/Important/Minor） | Superpowers code-reviewer | GSD severity |
| 标准来源引用 | Matt Pocock review（引用标准文件+规则） | — |
| Spec 合规检查 | Matt Pocock review（Spec 轴） | Superpowers spec-compliance review |
| 缺失/范围蔓延/实现错误分类 | Matt Pocock review 三分类 | — |
| Spec 原文引用 | Matt Pocock review | Superpowers |
| 验收标准逐条确认 | 我们的设计决策 | Superpowers 两阶段 review |
| 自动修复建议 | 我们的设计决策（差异化） | — |
| Verdict | Superpowers Assessment | GSD PASS/FAIL |
| 评审反馈处理协议 | Superpowers receiving-code-review | — |

### 5.5 关键设计决策建议

| 决策点 | 建议 | 理由 |
|--------|------|------|
| Plan 阶段是否产出多文件？ | 单文件 Plan Spec，但可在同一目录下附加 data-model、contracts 等参考文件 | 借鉴 SpecKit 多产出但降低复杂度 |
| 切片粒度标准？ | 端到端垂直切片，可独立测试、可独立演示 | Matt Pocock to-issues 的 tracer bullet 概念 + 我们的设计决策 |
| Execute 阶段是否有独立 Spec？ | 不单独产出，在 Plan Spec 的切片中追踪状态 | 借鉴 Superpowers 的 checkbox 追踪，减少文件数量 |
| Review 是几维度？ | 三维度：代码质量 + 需求一致性 + 验收标准逐条确认 | Matt Pocock 两轴 + 我们的验收标准轴 |
| 问题分级？ | Critical / Important / Minor 三级 | Superpowers 的成熟分级体系 |
| 是否支持自动修复建议？ | 是，Review 产出中标注"自动可修"字段 | 我们的差异化功能 |
| 是否两阶段 Review？ | MVP 不做两阶段（Spec compliance + Code quality 并行），但在模板设计中预留 | Superpowers 和 Matt Pocock 都用并行模式，后续可实现 |
| 模板是否分流程？ | 是。Quick 无 Plan Spec；Standard 有 Plan 但简化；Full 完整 | 与三种流程分层对齐 |

### 5.6 特别值得注意的设计模式

1. **Matt Pocock 的垂直切片 tracer bullet** — 与我们的"逐切片串行"设计完美对齐，且明确反对"水平切片"（先写全量测试再写全量实现），这应该成为 Execute 阶段的核心原则
2. **Matt Pocock 的两轴 Review** — Standards 和 Spec 互不污染，是最干净的 Review 架构，我们加第三轴（验收标准）后形成独特的三维度评审
3. **Superpowers 的两阶段 Review** — 先检查 spec compliance 再检查 code quality，在 subagent 模式下有效防止一个问题类型掩盖另一个
4. **GSD 的三层验证（Exists/Substantive/Wired）** — 这是所有框架中最细致的验证方法，可用于 Review 阶段的验收标准检查
5. **Superpowers 的评审反馈处理协议（receiving-code-review）** — READ→UNDERSTAND→VERIFY→ACT→PUSH BACK，确保评审反馈被正确处理而非盲目执行
6. **SpecKit 的 Constitution Check** — 项目级质量标准在 Plan 和 Review 阶段都要检查，提供了跨阶段的一致性保证
7. **GSD 的决策保真** — 每个 Task 引用决策 ID（D-01, D-02），锁定决策不可违反，延迟想法不可混入，这对我们"人主控"原则有借鉴意义

---

*本分析基于各框架 GitHub 仓库的实际模板文件、在线文档、以及 Matt Pocock Skills 本地文件，结合 2026-05-09 的研究报告和 2026-05-17 的 Requirement Review 竞品分析。*