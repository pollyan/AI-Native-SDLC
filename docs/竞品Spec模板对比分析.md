# 竞品 Spec 模板对比分析

> 日期：2026-05-17
> 目的：对比分析 7 个 AI 编程框架的文档输出规范，为 AI-Native-SDLC 的 Requirement Review Spec 模板设计提供输入
> 方法：结合已有研究报告 + 在线研究各框架 GitHub 仓库的实际模板文件

---

## 一、各框架的输出规范详解

---

### 1. Superpowers（obra/superpowers）⭐ 183K

#### 需求/规范阶段：brainstorming skill

**输出文件：** `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`

**模板结构（从 SKILL.md 流程推断）：**

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
### [Section N]

## Scope Boundaries
[明确范围内/范围外]

## Open Questions
[未解决的问题]
```

**流程特征：**
1. 探索项目上下文 → 逐个提问澄清 → 提出 2-3 个方案（含 trade-off） → 逐段展示设计获用户批准 → 写入设计文档 → 自审核（placeholder scan + 一致性检查 + scope 检查）→ 用户审核文档
2. **HARD-GATE**：在用户批准设计之前，禁止调用任何实现 skill
3. **Anti-Pattern**：强制声明"This Is Too Simple To Need A Design"——即使最简单的项目也必须过设计流程
4. 终态：调用 writing-plans skill（不是直接实现）

**独特字段：**
- 方案对比（2-3 approaches with trade-offs）
- 推荐方案标记
- Spec Self-Review（自审核循环）
- "Reflection on how you think"（gstack 也借鉴了这个）

---

### 2. SpecKit（github/spec-kit）⭐ 94K

#### 需求/规范阶段：specify → review-spec

**输出文件：** `specs/<feature-name>/spec.md`

**实际模板内容（从 `templates/spec-template.md` 提取）：**

```markdown
# Feature Specification: [FEATURE NAME]
\Feature Branch\: `[###-feature-name]`
\Created\: [DATE]
\Status\: Draft
\Input\: User description: "$ARGUMENTS"

## User Scenarios & Testing (mandatory)
### User Story 1 - [Brief Title] (Priority: P1)
[Describe this user journey in plain language]
\Why this priority\: [Explain the value and why it has this priority level]
\Independent Test\: [Describe how this can be tested independently]
\Acceptance Scenarios\:
1. \Given\ [initial state], \When\ [action], \Then\ [expected outcome]
2. \Given\ [initial state], \When\ [action], \Then\ [expected outcome]

[Add more user stories as needed, each with an assigned priority]

### Edge Cases
- What happens when [boundary condition]?
- How does system handle [error scenario]?

## Requirements (mandatory)
### Functional Requirements
- \FR-001\: System MUST [specific capability]
- \FR-002\: System MUST [specific capability]
- \FR-003\: Users MUST be able to [key interaction]
- \FR-004\: System MUST [data requirement]
- \FR-005\: System MUST [behavior]

### Key Entities (include if feature involves data)
- \[Entity 1]\: [What it represents, key attributes without implementation]
- \[Entity 2]\: [What it represents, relationships to other entities]

## Success Criteria (mandatory)
### Measurable Outcomes
- \SC-001\: [Measurable metric]
- \SC-002\: [Measurable metric]
- \SC-003\: [User satisfaction metric]
- \SC-004\: [Business metric]

## Assumptions
- [Assumption about target users]
- [Assumption about scope boundaries]
- [Assumption about data/environment]
- [Dependency on existing system/service]
```

**配套文件：**
- `constitution-template.md` — 项目宪法（质量门控）
- `plan-template.md` — 实现计划模板
- `tasks-template.md` — 任务模板
- `checklist-template.md` — 检查清单模板

**Plan 模板关键字段（从 `plan-template.md` 提取）：**
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

**独特字段：**
- `[NEEDS CLARIFICATION: ...]` 标记机制
- User Story 含独立可测试性声明（Independent Test）
- Edge Cases 独立章节
- Key Entities 数据建模预览
- Constitution Check（宪法检查门控）
- Complexity Tracking（复杂度追踪表）

---

### 3. gstack（garrytan/gstack）⭐ 92K

#### 需求/规范阶段：/office-hours → /plan-ceo-review → /plan-eng-review

**输出文件：** `~/.gstack/projects/<project>/design-doc.md`

**模板结构（从 skill 描述推断）：**

```markdown
# Design Doc: [Project Name]
\Mode\: Startup | Builder
\Date\: [DATE]

## Problem Framing
[Six forcing questions 的回答]
1. What is the real problem people have?
2. What is the status quo workaround?
3. Who specifically has this problem?
4. What is the smallest wedge that's valuable?
5. What have you observed users doing?
6. Does this fit where the market is going?

## Original Request
[用户的原始描述]

## Reframed Understanding
[AI 重新理解后的真实需求]

## Implementation Alternatives
[2-3 个实现方案]

## Recommended Approach
[推荐方案]

## Scope Decision
[Expansion | Selective Expansion | Hold Scope | Reduction]

## Reflection on How You Think
[AI 对用户思考方式的观察——非泛泛的赞扬，而是具体的回调]
```

**多维度评审（/plan-ceo-review, /plan-eng-review, /plan-design-review, /plan-devex-review）：**

CEO Review 四种模式：
- Expansion：扩展范围找更大价值
- Selective Expansion：选择部分扩展
- Hold Scope：保持范围
- Reduction：削减范围

Eng Review 关键字段：
- Architecture + Data Flow
- ASCII Diagrams（数据流图、状态机、错误路径）
- Edge Cases
- Test Matrix
- Failure Modes
- Security Concerns

**独特字段：**
- Six Forcing Questions（YC 风格的六个追问）
- Problem Reframing（问题重构）
- Scope Decision 模式选择
- "Reflection on How You Think"（对思考方式的反思）
- 多维度评审（CEO/Design/Eng/DX 各视角）
- ASCII 图表（数据流、状态机、错误路径）

---

### 4. GSD / Get Shit Done（gsd-build/get-shit-done）⭐ 61K

#### 需求/规范阶段：/gsd-new-project → /gsd-discuss-phase

**输出文件：** `.planning/` 目录下的多个结构化文档

**目录结构：**
```
.planning/
├── PROJECT.md          # 项目愿景
├── REQUIREMENTS.md     # 需求文档（需求 ID + 描述 + 验收标准）
├── ROADMAP.md          # 分阶段路线图
├── STATE.md            # 当前状态和决策记录
├── phases/
│   ├── 1-xxx/
│   │   ├── CONTEXT.md      # 阶段上下文（实现决策）
│   │   ├── PLAN.md         # 具体计划（frontmatter + goal + context + tasks）
│   │   ├── RESEARCH.md     # 领域研究结果
│   │   ├── SUMMARY.md      # 执行结果摘要
│   │   └── VERIFICATION.md # 验证结果
│   └── 2-xxx/
└── todos/
    ├── pending/
    └── completed/
```

**REQUIREMENTS.md 结构：**
```markdown
# Requirements

## REQ-001: [Requirement Title]
- **Description**: [What]
- **Priority**: [P0/P1/P2]
- **Acceptance Criteria**:
  - Given [state], When [action], Then [outcome]
- **Phase**: [Assigned phase number]

## REQ-002: [Requirement Title]
...
```

**PLAN.md 模板（含 frontmatter）：**
```markdown
---
phase: 2
plan: 1
title: User Registration Endpoint
dependencies: []
estimated_tasks: 4
---
# Plan 01: User Registration Endpoint

## Goal
[一句话目标]

## Context
- Uses NextAuth with database sessions (from RESEARCH.md)
- Reuses database connection from Phase 1
- Follow validation patterns from CONTEXT.md

## Tasks
<task type="auto">
1. [Task description]
</task>
```

**独特字段：**
- Goal-Backward 方法论（从目标倒推）
- STATE.md 跨会话状态管理
- Context Quality Curve（上下文质量曲线）
- 每个 Phase 有 RESEARCH.md（领域研究）
- PLAN.md 使用 YAML frontmatter 结构化元数据
- Phase/Plan/Task 三级粒度
- 自动状态检测（`/gsd-progress --next`）

---

### 5. BMAD Method（bmad-code-org/BMAD-METHOD）⭐ 47K

#### 需求/规范阶段：Analyst → PM → Architect

**工作流产出文件：**
```
_bmad-output/
├── planning-artifacts/
│   ├── brainstorming-report.md    # 阶段 1：头脑风暴
│   ├── product-brief.md           # 项目简报
│   ├── prfaq-{project}.md         # PRFAQ 文档
│   ├── PRD.md                     # 阶段 2：产品需求文档
│   ├── ux-spec.md                 # UX 设计规范
│   ├── architecture.md            # 阶段 3：架构文档
│   ├── epics/                     # Epic 和 Story 文件
│   │   ├── epic-1.md
│   │   └── story-[slug].md
│   └── sprint-status.yaml         # Sprint 跟踪
├── implementation-artifacts/
│   └── ...
└── project-context.md             # 项目上下文（宪法）
```

**PRD 模板结构：**
```markdown
# Product Requirements Document

## Product Overview
### Product Vision
### Target Users
### Problem Statement

## Epics
### Epic 1: [Name]
- Description
- Business Value

## User Stories
### US-001: [Story Title]
**As a** [user type], **I want** [goal], **so that** [benefit]
- **Priority**: [P0/P1/P2]
- **Story Points**: [N]
- **Acceptance Criteria**:
  - Given/When/Then
- **Dependencies**: [Other stories]

## Functional Requirements
### FR-001: [Requirement]
- **Priority**: P0
- **Description**: [详细描述]
- **Epic**: [所属 Epic]

## Non-Functional Requirements
### NFR-001: [Requirement]
- **Category**: [Performance/Security/Scalability/...]
- **Description**: [详细描述]
- **Measurement**: [如何验证]

## Constraints & Assumptions
## Out of Scope
## Open Questions
```

**12 步 PRD 引导流程（bmad-create-prd → bmad-prd）：**
1. Discovery — 理解产品愿景
2. User Personas — 定义用户画像
3. Problem Statement — 问题陈述
4. Epics Definition — Epic 定义
5. User Stories — 用户故事
6. Functional Requirements — 功能需求
7. Non-Functional Requirements — 非功能需求
8. Constraints — 约束条件
9. Assumptions — 假设
10. Dependencies — 依赖
11. Validation Checklist — 验证检查
12. Polish — 最终打磨

**独特字段：**
- PRFAQ（Press Release + FAQ，亚马逊风格）
- Functional + Non-Functional 分离
- 12 步引导流程
- 三层配置合并（base → team → user）
- persistent_facts（角色记忆持久事实）
- config.yaml 集中配置
- 角色 persistent_facts 支持文件引用和 glob
- Implementation Readiness Check（实现前就绪检查）

---

### 6. OpenSpec（Fission-AI/OpenSpec）⭐ 46K

#### 需求/规范阶段：/opsx:propose → proposal.md → specs/ → design.md → tasks.md

**输出文件结构（Change-based）：**
```
openspec/
├── specs/                    # 源事实（当前系统行为）
│   └── <domain>/
│       └── spec.md
├── changes/                  # 变更提案
│   └── <change-name>/
│       ├── proposal.md       # Why and what
│       ├── design.md         # How (technical approach)
│       ├── tasks.md          # Implementation checklist
│       ├── .openspec.yaml    # Change metadata
│       └── specs/            # Delta specs
│           └── <domain>/
│               └── spec.md
└── config.yaml
```

**proposal.md 结构：**
```markdown
# Proposal: [Change Name]

## Motivation
[为什么需要这个变更]

## Scope
[变更范围]

## Expected Impact
[预期影响]
```

**spec.md 格式（Requirement + Scenario 结构）：**
```markdown
## Purpose
[High-level description of this spec's domain]

### Requirement: [Title]
[What the system must do — using SHALL/MUST/SHOULD]

#### Scenario: [Name]
Given [context]
When [action]
Then [expected outcome]
```

**Delta Spec 格式（变更标记）：**
```markdown
## ADDED Requirements
### Requirement: [New requirement]
...

## MODIFIED Requirements
### Requirement: [Changed requirement]
[Old → New]
...

## REMOVED Requirements
### Requirement: [Removed requirement]
...
```

**独特字段：**
- Delta Spec（增量/差异规范）——只描述变化，不全量重写
- ADDED / MODIFIED / REMOVED 标记
- 源事实（Source of Truth）vs 变更提案分离
- 按领域组织（domain-based）
- Requirement + Scenario 的严格层级
- RFC 2119 关键词（SHALL/MUST/SHOULD）
- 归档时自动 merge delta specs 到 main specs
- 非线性工作流（Action-based，不是 Phase-based）

---

### 7. specs.md（fabriqaai/specs.md）⭐ 142

#### 需求/规范阶段：/specsmd-agent（Simple Flow）

**三种流程：**

**Simple Flow** — 仅规范生成：
```
specs/
└── {feature-name}/
    ├── requirements.md
    ├── design.md
    └── tasks.md
```

**FIRE Flow** — 快速执行：
```
.specs-fire/
├── state.yaml              # Central state tracking
├── standards/              # Project standards
├── intents/                # Intent documentation
├── runs/                   # Run logs
└── walkthroughs/           # Generated documentation
```

**AI-DLC Flow** — 完整方法论：
```
memory-bank/
├── standards/              # Project standards
├── intents/
│   └── {intent-name}/
│       ├── requirements.md
│       ├── system-context.md
│       └── units/
├── bolts/                  # Bolt execution records
└── operations/             # Deployment context
```

**requirements.md 模板结构（推断）：**
```markdown
# Requirements: [Feature Name]

## Overview
[Feature overview]

## Functional Requirements
- FR-001: [Description]

## Non-Functional Requirements
- NFR-001: [Description]

## Constraints
- [Constraint]

## Out of Scope
- [Excluded items]

## Acceptance Criteria
- [Given/When/Then]
```

**独特字段：**
- 自适应检查点（0-2 checkpoints based on complexity）
- Intent 概念（而非 Feature/Requirement）
- System Context（系统上下文文件）
- Standards（项目标准）
- Walkthroughs（自动生成的文档）
- 三种流程可选（Simple / FIRE / AI-DLC）
- Brownfield & Monorepo 一等公民支持

---

## 二、横向对比表

### 2.1 字段维度对比

| 字段/章节 | Superpowers | SpecKit | gstack | GSD | BMAD | OpenSpec | specs.md |
|-----------|:-----------:|:-------:|:------:|:---:|:----:|:--------:|:--------:|
| **问题陈述/背景** | ✅ | ❌ | ✅ (6 Questions) | ✅ (PROJECT.md) | ✅ (Problem Statement) | ✅ (Motivation) | ✅ (Overview) |
| **用户故事** | ❌ | ✅ (P1/P2 优先级) | ❌ | ❌ | ✅ (As a/I want/So that) | ❌ | ❌ |
| **功能需求** | ✅ (设计目标) | ✅ (FR-001..N) | ❌ | ✅ (REQ-001..N) | ✅ (FR/NFR 分离) | ✅ (Requirement) | ✅ (FR-001..N) |
| **非功能需求** | ❌ | ❌ | ❌ | ❌ | ✅ (NFR-001..N) | ❌ | ✅ (NFR-001..N) |
| **验收标准** | ❌ | ✅ (Given/When/Then) | ❌ | ✅ (Given/When/Then) | ✅ (Given/When/Then) | ✅ (Scenario) | ✅ |
| **成功标准** | ❌ | ✅ (Measurable Outcomes) | ❌ | ❌ | ❌ | ❌ | ❌ |
| **边界条件/Edge Cases** | ❌ | ✅ (独立章节) | ✅ (Eng Review) | ❌ | ❌ | ❌ | ❌ |
| **范围界定** | ✅ | ❌ | ✅ (4 种模式) | ❌ | ✅ (Out of Scope) | ✅ (Scope) | ✅ (Out of Scope) |
| **方案对比** | ✅ (2-3 approaches) | ❌ | ✅ (Alternatives) | ❌ | ❌ | ❌ | ❌ |
| **假设** | ❌ | ✅ (Assumptions) | ❌ | ❌ | ✅ (Assumptions) | ❌ | ✅ (Constraints) |
| **术语定义** | ❌ | ❌ | ❌ | ❌ | ✅ (config.yaml) | ❌ | ❌ |
| **数据模型/实体** | ❌ | ✅ (Key Entities) | ✅ (Data Flow) | ❌ | ✅ (Architecture) | ❌ | ❌ |
| **依赖关系** | ❌ | ❌ | ❌ | ✅ (frontmatter) | ✅ (Dependencies) | ❌ | ❌ |
| **需求 ID 编号** | ❌ | ✅ (FR-001) | ❌ | ✅ (REQ-001) | ✅ (FR-001, NFR-001) | ✅ (Requirement) | ✅ (FR-001) |
| **优先级标注** | ❌ | ✅ (P1/P2) | ❌ | ✅ (P0/P1/P2) | ✅ (P0/P1/P2) | ❌ | ❌ |
| **待澄清标记** | ❌ | ✅ (NEEDS CLARIFICATION) | ❌ | ❌ | ❌ | ❌ | ❌ |
| **用户审核门控** | ✅ (HARD-GATE) | ✅ (review-spec gate) | ✅ (design doc approval) | ❌ | ✅ (PO review) | ❌ | ✅ (checkpoints) |
| **自审核** | ✅ (placeholder scan) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **领域组织** | ❌ | ❌ | ❌ | ✅ (Phase-based) | ❌ | ✅ (Domain-based) | ❌ |
| **增量/Delta** | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ (ADDED/MODIFIED/REMOVED) | ❌ |
| **多方案评审** | ❌ | ❌ | ✅ (4 个视角) | ❌ | ❌ | ❌ | ❌ |

### 2.2 文件组织对比

| 框架 | 文件策略 | 产出路径 | 与代码共存 |
|------|---------|---------|-----------|
| Superpowers | 单文件 spec | `docs/superpowers/specs/` | ✅ |
| SpecKit | 每功能一个目录 | `specs/<feature>/` | ✅ |
| gstack | 单文件 design doc | `~/.gstack/projects/` | ❌ (外部) |
| GSD | 多文件层级 | `.planning/` | ✅ |
| BMAD | 多角色多文件 | `_bmad-output/` | ✅ |
| OpenSpec | Change-based 文件夹 | `openspec/` | ✅ |
| specs.md | 三种流程可选 | `specs/` or `.specs-fire/` or `memory-bank/` | ✅ |

---

## 三、各框架的独特亮点

### 3.1 只在该框架中出现的优秀设计

| 框架 | 独特亮点 | 描述 |
|------|---------|------|
| **Superpowers** | Spec Self-Review 循环 | 写完 spec 后自动做 placeholder scan、一致性检查、scope 检查，发现问题直接修复，不需要再跑一遍。这是唯一内建自审核的框架 |
| **Superpowers** | 方案对比强制 | 要求提供 2-3 个方案含 trade-off + 推荐标记，而不是只给一个方案 |
| **SpecKit** | `[NEEDS CLARIFICATION]` 标记 | 不确定的需求显式标记，不跳过也不猜，强制留下审计痕迹。这个设计极其轻量但极其有效 |
| **SpecKit** | User Story 独立可测试性声明 | 每个 User Story 必须声明"如何独立测试 + 交付什么价值"，防止不可验证的故事混入 |
| **SpecKit** | Constitution Check | 项目级"宪法"文件定义质量标准，所有产出必须通过宪法检查 |
| **SpecKit** | Complexity Tracking | 当设计违反了 Constitution 中的规则，必须填写复杂度追踪表（违反了什么 + 为什么需要 + 更简方案为什么不行） |
| **gstack** | Six Forcing Questions | YC 风格的六个追问，把模糊的需求变成经过压力测试的问题定义 |
| **gstack** | Problem Reframing | AI 不按用户说的原样理解，而是重新理解"你真正要解决的是什么" |
| **gstack** | 多维度评审 | CEO/Design/Eng/DX 四个独立视角评审，每个视角有独立的 skill 和输出 |
| **gstack** | Reflection on Thinking | AI 对用户思考方式的观察记录在 design doc 里，下次读到会重新遇到 |
| **GSD** | STATE.md 跨会话状态 | 精确记录当前项目位置和决策，新会话加载后立刻知道状态 |
| **GSD** | YAML Frontmatter 元数据 | PLAN.md 使用结构化 frontmatter（phase/plan/title/dependencies/estimated_tasks），可被工具解析 |
| **GSD** | RESEARCH.md 领域研究 | 每个 Phase 开始前做领域研究，研究结果被后续 Plan 和 Execute 引用 |
| **BMAD** | PRFAQ（Press Release + FAQ） | 亚马逊风格的文档——先写新闻稿和 FAQ，倒逼需求清晰化 |
| **BMAD** | 12 步 PRD 引导流程 | 最完整的 PRD 引导，从 Discovery 到 Polish，每步有独立的 .md 文件 |
| **BMAD** | FR/NFR 严格分离 | 功能需求和非功能需求分开管理，NFR 有明确的 Category + Measurement |
| **BMAD** | Implementation Readiness Check | 实现前的就绪检查，验证所有规划文档的一致性 |
| **OpenSpec** | Delta Spec（增量规范） | 只描述变化（ADDED/MODIFIED/REMOVED），不全量重写，归档时自动 merge |
| **OpenSpec** | 源事实 vs 变更分离 | `specs/` 是当前系统的真实行为，`changes/` 是提案，两者严格分离 |
| **OpenSpec** | RFC 2119 关键词 | 使用标准化的 SHALL/MUST/SHOULD 表达需求强度 |
| **OpenSpec** | 非线性 Action-based | 不是阶段式而是 action-based——随时可以 propose/apply/archive |
| **specs.md** | 自适应检查点 | 根据任务复杂度自动调整检查点数量（0-2），不强制一刀切 |
| **specs.md** | Intent 概念 | 用"意图"而非"需求"作为核心组织单元，更贴近业务思维 |
| **specs.md** | 三种流程可选 | Simple/FIRE/AI-DLC 三种流程适配不同复杂度，用户自选 |

---

## 四、对 AI-Native-SDLC Requirement Review Spec 模板的综合建议

### 4.1 设计原则

基于对比分析，我们的模板设计应遵循：

1. **结构化但不过度** — 参考SpecKit 的做法，区分必填（mandatory）和可选（optional）
2. **需求 ID 化** — 所有框架共识：需求必须有编号（FR-001），便于追踪和引用
3. **验收标准前置** — SpecKit、BMAD、OpenSpec 都把验收标准放在需求阶段，不是后面补
4. **模糊显式化** — 借鉴 SpecKit 的 `[NEEDS CLARIFICATION]` 标记
5. **自审核内建** — 借鉴 Superpowers 的 Spec Self-Review

### 4.2 推荐的模板字段

基于对比分析，建议 Requirement Review Spec 模板包含以下字段：

```markdown
# Requirement Spec: [Feature Name]

## 元数据（必填）
\Spec ID\: SPEC-[XXX]
\Feature Branch\: [branch-name]
\Created\: [DATE]
\Status\: Draft | Under Review | Approved
\Input Source\: [PRD URL / Issue # / 口头描述]
\Flow Type\: Quick | Standard | Full（推荐流程类型）

## 1. 问题定义（必填）
### 1.1 问题陈述
[要解决的业务问题是什么——借鉴 gstack 的 Problem Reframing]
### 1.2 业务背景
[为什么现在要做这个——借鉴 GSD PROJECT.md]
### 1.3 目标用户
[为谁解决——借鉴 BMAD Target Users]

## 2. 需求描述（必填）
### 2.1 功能需求
- FR-001: [System MUST ...]（借鉴 SpecKit/BMAD 的编号规范）
- FR-002: ...
> 标记方式：不确定的需求用 `[NEEDS CLARIFICATION: 原因]`（来自 SpecKit）

### 2.2 非功能需求（可选，Full 流程必填）
- NFR-001: [Category: Performance] [Description] [Measurement]（借鉴 BMAD 的 FR/NFR 分离）

### 2.3 边界条件（必填）
- 当 [边界条件] 时，系统应该 [行为]（借鉴 SpecKit Edge Cases）
- 当 [错误场景] 时，系统应该 [行为]

## 3. 验收标准（必填）
### SC-001: [验收标准标题]
- Given [初始状态], When [操作], Then [期望结果]
### SC-002: ...
> 每条验收标准必须可独立验证（借鉴 SpecKit 的 Independent Test）

## 4. 范围界定（必填）
### 4.1 范围内
- [包含的功能]
### 4.2 范围外
- [明确排除的功能]
### 4.3 范围决策理由（可选）
[为什么选择这个范围——借鉴 gstack Scope Decision]

## 5. 术语定义（Full 流程必填，其他可选）
| 术语 | 定义 | 来源 |
|------|------|------|
| [Term] | [Definition] | [文档/讨论出处] |

## 6. 假设与依赖（可选）
### 6.1 假设
- [Assumption about ...]（借鉴 SpecKit Assumptions）
### 6.2 依赖
- [Dependency on ...]
### 6.3 待澄清问题
- [ ] [Question 1]（借鉴 SpecKit NEEDS CLARIFICATION）
- [ ] [Question 2]

## 7. 方案预研（可选，Full 流程推荐）
### Option A: [Name]（推荐）
- 优势: ...
- 劣势: ...
### Option B: [Name]
- 优势: ...
- 劣势: ...
> 推荐理由: [why]（借鉴 Superpowers 的方案对比）

---

## 门控检查清单
- [ ] 问题定义清晰
- [ ] 范围界定明确
- [ ] 验收标准已列出且每条可独立验证
- [ ] 关键术语已定义（Full 流程）
- [ ] 待澄清问题全部解决或已标记
- [ ] 用户确认 ✓

## Spec Self-Review（自动检查，借鉴 Superpowers）
- [ ] 无 placeholder 残留
- [ ] 无内部矛盾
- [ ] 无超出范围的隐含需求
- [ ] 所有 NEEDS CLARIFICATION 已解决
```

### 4.3 字段来源追溯

| 模板字段 | 主要借鉴来源 | 辅助借鉴 |
|---------|-------------|---------|
| 元数据（Spec ID / Branch / Status / Flow Type） | SpecKit（Branch/Status/Input） | GSD（frontmatter 模式）、我们（Flow Type） |
| 问题陈述 | gstack（Problem Reframing） | GSD（PROJECT.md） |
| 目标用户 | BMAD（Target Users） | — |
| 功能需求（FR-001 编号） | SpecKit（FR-001..N） | BMAD、GSD |
| 非功能需求（NFR 分离） | BMAD（FR/NFR 分离 + Category + Measurement） | specs.md |
| 边界条件 | SpecKit（Edge Cases） | gstack（Eng Review） |
| 验收标准（Given/When/Then + 独立可验证） | SpecKit（Independent Test） | BMAD、OpenSpec（Scenario） |
| 成功标准（Measurable Outcomes） | SpecKit（SC-001..N） | — |
| 范围界定（内/外 + 决策理由） | BMAD（Out of Scope） | gstack（Scope Decision） |
| 术语定义 | 我们的设计决策（统一语言） | BMAD（config.yaml）、Matt Pocock（CONTEXT.md） |
| 假设与依赖 | SpecKit（Assumptions） | BMAD（Dependencies） |
| 待澄清标记 `[NEEDS CLARIFICATION]` | SpecKit | — |
| 方案预研（2-3 options + 推荐） | Superpowers（Approach Options） | gstack（Alternatives） |
| 门控检查清单 | 我们的设计决策 | Superpowers（HARD-GATE）、SpecKit（review-spec gate） |
| Spec Self-Review | Superpowers（自审核循环） | — |

### 4.4 关键设计决策建议

| 决策点 | 建议 | 理由 |
|--------|------|------|
| 模板是否分 Quick/Standard/Full？ | 是。Full 版本包含所有字段，Standard 眀略"方案预研"和"术语定义"，Quick 不产出 spec | 与我们已有的三种流程分层对齐 |
| 验收标准用什么格式？ | Given/When/Then（Gherkin 风格） | 7 个框架中 5 个使用此格式，事实标准 |
| 需求是否必须编号？ | 是。FR-001/NFR-001 格式 | 所有成熟框架的共识，便于追踪引用 |
| 是否内建自审核？ | 是。产出后自动跑 Self-Review checklist | Superpowers 独有的好设计，零成本高收益 |
| 模糊需求怎么处理？ | `[NEEDS CLARIFICATION: 原因]` 标记 | SpecKit 的轻量设计，不跳过也不猜 |
| 是否需要方案对比？ | Full 流程推荐，Standard 可选，Quick 不需要 | 超半数框架有方案对比，但简单需求不需要 |
| 文件命名规范？ | `specs/SPEC-[XXX]-[slug].md` | 结合 SpecKit（每功能目录）和 GSD（编号）的优点 |

### 4.5 特别值得注意的设计模式

1. **SpecKit 的 Constitution Check** — 我们已有类似概念（项目级质量标准），建议在模板中显式引用，Plan 阶段必须通过宪法检查
2. **OpenSpec 的 Delta Spec** — 对于迭代开发场景，增量规范比全量重写更实际。建议在后续版本考虑 Delta Spec 支持
3. **gstack 的多维度评审** — Full 流程的 Review 阶段值得引入 CEO/Eng 多视角，但在 Requirement Review 阶段暂不需要
4. **BMAD 的 Implementation Readiness Check** — 这是一个好设计，建议放在 Plan → Execute 之间作为门控
5. **Superpowers 的 Anti-Pattern 声明** — "This Is Too Simple" 反模式声明，可以有效防止跳过关键步骤

---

*本分析基于各框架 GitHub 仓库的实际模板文件和文档，以及 2026-05-09 的研究报告。*
