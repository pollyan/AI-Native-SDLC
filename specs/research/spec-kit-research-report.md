# SpecKit 深度调研报告

> 仓库：https://github.com/github/spec-kit  
> Stars：94K（GitHub 官方出品）  
> 更新时间：2026-05-17

---

## 1. 产品定位与核心哲学

**定位**：GitHub 官方出品，反"Vibe Coding"（凭感觉写代码）。核心信念：**"规约即执行（Specifications become executable）"**。

**目标人群**：
- 习惯于企业级规范的开发者
- 需要在长生命周期（Brownfield）项目中引入 AI 的团队

**独特立场**：
- 全市场中 `[NEEDS CLARIFICATION]` 显式标记机制的首创者（唯一）
- 唯一要求 User Story 必须声明"独立可测试性"的框架
- 唯一内建 Constitution（项目宪法）门控的框架
- 唯一有 Complexity Tracking（复杂度追踪表）的框架

---

## 2. 全生命周期工作流设计

### 2.1 核心命令流水线（5 步指令集）

```
/speckit.constitution  →  /speckit.specify  →  /speckit.plan  →  /speckit.tasks  →  /speckit.implement
```

1. **`/speckit.constitution`**：建立当前项目宪法（代码规范、质量标准）
2. **`/speckit.specify`**：产出产品维度的用户故事与需求文档
3. **`/speckit.plan`**：输出具体的技术实现方案和数据流图
4. **`/speckit.tasks`**：通过特殊约束规范（`[P]` 表示并行，`[US]` 表示用户故事归属）产出原子化任务
5. **`/speckit.implement`**：正式进入基于规划的代码生成阶段

### 2.2 Requirement Review 阶段（specify → review-spec）

**产出文件路径**：`specs/<feature-name>/spec.md`

**spec.md 完整模板（从 `templates/spec-template.md` 提取）**：
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

**spec.md 的独特设计点**：
- `[NEEDS CLARIFICATION: ...]` 标记——不确定的需求显式标记，强制留下审计痕迹
- User Story 必须声明 `\Independent Test\`——每个故事必须声明"如何独立测试 + 交付什么价值"，防止不可验证的故事混入
- `Edge Cases` 独立章节（不是 Requirements 的附属）
- `Key Entities` 数据建模预览（非技术性描述）
- `Success Criteria` 必须是**可量化的**指标（SC-001 等），不接受模糊描述
- 优先级标注（P1/P2）在 User Story 级别

### 2.3 Plan 阶段

**配套文件体系**：
```
specs/<feature-name>/
├── spec.md              # 需求规范
├── plan.md              # 实现计划
├── tasks.md             # 原子化任务列表
├── checklist.md         # 验收检查清单
├── data-model.md        # 数据模型
└── contracts/           # API/接口合约
```

**plan.md 模板关键字段（从 `plan-template.md` 提取）**：
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

**Constitution Check 机制（项目宪法）**：
- 项目级不可违反的约束文件（Always/Never/Ask First）
- 所有 Spec 和 Plan 产出必须通过宪法检查
- Constitution Check 是硬门控（GATE），不是建议项

**Complexity Tracking（复杂度追踪表）**：
- 当设计违反了 Constitution 中的规则，必须填写追踪表
- 三列：Violation（违反了什么）| Why Needed（为什么需要）| Simpler Alternative Rejected Because（为什么更简单的方案不行）
- 强制让 AI 书面说明为何要增加复杂度，而不是默默绕过约束

### 2.4 Execute 阶段（tasks.md）

**tasks.md 的并发调度设计**：

通过严格的标签约定，将平面 Markdown 文件变成可被并行调度的**有向无环图（DAG）**：
```markdown
## Phase 0: Research & Setup
- [ ] T001 [US1] Research existing auth libraries
- [ ] T002 [US1] Setup project structure

## Phase 1: Core Implementation  
- [ ] T010 [P] Contract Test for auth endpoint [US1]
- [ ] T011 [P] Integration Test setup [US1]
> Write these tests FIRST, ensure they FAIL before implementation

## Phase 2: Feature Development
- [ ] T020 [US1] Implement auth endpoint
  Dependencies: T010, T011

## Checkpoint (P1 MVP Complete)
> Human validation required before proceeding to P2
```

**标签系统说明**：
- `[P]`：并行任务，无依赖关系，可同时执行
- `[US1]`：归属 User Story 1
- `Checkpoint`：人工验证节点，阻断自动继续
- `Dependencies`：显式声明依赖关系

**TDD 前置审查机制**：
- 强制将测试任务（如 T010 Contract Test）按优先级提升到业务代码前
- 显式注释："Write these tests FIRST, ensure they FAIL before implementation"

### 2.5 验收与分析命令

- **`/speckit.analyze`**：反向工程——让 AI 将自己生成的 Tech Plan 和 Spec 重新对应匹配，检查是否存在遗漏或自相矛盾
- **`/speckit.checklist`**：运行检查清单，像跑单元测试一样检查生成逻辑的合规性
- **`/speckit.review-spec`**：显式 Review 门控，通过前不进入 Plan 阶段
- **`/speckit.review-plan`**：Plan 完成后的验证门控

---

## 3. 工程架构设计

### 3.1 中心化 CLI 管理架构
通过 `specify-cli`（基于 Python 和 `uv` 环境构建）实现全局管控，配合多套动态渲染的文件树系统改变 Agent 行为。

### 3.2 动态分发的重载引擎（Template Overrides Engine）
优先级自上而下的回退机制：
```
.specify/templates/overrides/  →  presets  →  extensions  →  core
```
企业内部可根据自身 DevOps 标准随意覆盖 `/speckit.plan` 所依赖的 Prompt 模板，而不需要改动底层逻辑，确保极高的合规适配性。

### 3.3 跨工具集成架构（30+ 工具适配）
在 `src/specify_cli/agents.py` 和 `integrations/` 目录下，通过抽象统一接口，将生成的 Markdown 提示词结构化地安装或"注入"到超过 30 种主流 Coding Agent 的特定环境目录下。

### 3.4 可热插拔的预设与扩展库
- **Extensions**：引入新工具流或外部 API（如 Jira 同步）
- **Presets**：改变规范输出格式

类似于针对"Prompt Engineering"的包管理工具。

---

## 4. 核心实现技巧

### 4.1 `[NEEDS CLARIFICATION]` 显式标记机制
行业中唯一的显式不确定性标记设计：
- 不跳过也不猜测，强制留下审计痕迹
- 不是"加个注释"而是**结构化字段**，可被工具扫描
- 每个标记必须说明影响了什么决策

### 4.2 User Story 独立可测试性声明
每个 User Story 必须声明：
- `\Independent Test\`：如何独立测试这个故事
- `\Why this priority\`：为什么是这个优先级
- 防止不可验证的故事（"用户体验更好"）混入 spec

### 4.3 防发散与重收敛机制
`/speckit.analyze` 命令让 AI 把自己生成的 Tech Plan 和 Spec 重新对应匹配——像跑单元测试一样检查自身生成的逻辑是否存在遗漏或自相矛盾。这是**对 AI 输出进行形式化自验证**的核心技巧。

### 4.4 多阶段 Checkpoint 防"迷失"
为避免长上下文导致的"Lost in the middle"现象，强硬要求在完成一个优先级的用户故事（如 P1 MVP）之后，通过特殊 `Checkpoint` 标识停止后续执行，交由人类或脚本验证独立功能后才继续 P2 任务。

### 4.5 声明式工作流 YAML（扩展计划）
已有将工作流从 Markdown 改为 YAML 声明式定义的设计方向，支持结构化步骤、门控、角色映射、on_reject 行为。

---

## 5. Spec 字段对比优势

| 能力 | SpecKit | 其他框架 |
|------|:-------:|:-------:|
| `[NEEDS CLARIFICATION]` 标记 | ✅（唯一） | 全部缺失 |
| User Story 独立可测试性声明 | ✅（唯一） | 全部缺失 |
| Constitution Check 门控 | ✅（唯一） | 全部缺失 |
| Complexity Tracking 表 | ✅（唯一） | 全部缺失 |
| Success Criteria 可量化指标 | ✅ | 仅 BMAD 有类似 |
| Spec 自验证（analyze） | ✅ | 仅 Superpowers 有 |
| 30+ 工具适配器 | ✅ | GSD 也有 |

---

## 6. 对我们框架的借鉴价值

| 借鉴点 | 优先级 | 对应我们的设计 |
|-------|--------|--------------|
| `[NEEDS CLARIFICATION]` 标记 | ✅ 已纳入 | §二原则 #3 显式标记 |
| User Story 独立可测试性 | ⭐⭐⭐ 高优 | Requirement Review 阶段验收标准设计 |
| Constitution 机制 | ⭐⭐⭐ P2-5（已规划） | 项目宪法 + Plan Phase -1 门控 |
| YAML 声明式工作流 | ⭐⭐⭐ P2-4（已规划） | Skill YAML 化方向 |
| Complexity Tracking | ⭐⭐ 中优 | Plan Spec 违反约束时的说明机制 |
| 多阶段 Checkpoint | ⭐⭐⭐ 高优 | 双层阶段门控设计参考 |
| Task 并发调度标记（[P]） | ⭐⭐ P4-2 | 与 Wave-based 并行结合 |
| 30+ 工具适配器架构 | ⭐⭐ 中优 | CLI 工具跨平台适配层 |
