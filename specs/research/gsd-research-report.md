# GSD (Get Shit Done) 深度调研报告

> 仓库：https://github.com/gsd-build/get-shit-done  
> Stars：61K  
> 更新时间：2026-05-17

---

## 1. 产品定位与核心哲学

**定位**：被定义为"元提示词框架（Meta-prompting framework）"，专为 AI 编程系统设计。核心信条：**"让系统承担复杂度，将用户的负担降到最低"**。

**Context 优先哲学**：
- **避免上下文腐败（Context Rot）**：极其强调上下文的干净。不鼓励在一个会话里做所有事，而是每次任务下发都拉起一个拥有纯净 200k+ tokens 上下文的新 Agent
- **状态透明化**：所有的项目状态均以 Markdown（`PROJECT.md`, `ROADMAP.md`, `STATE.md`）和 JSON（`config.json`）的形式暴露在 `.planning/` 目录下，人类可读且可被 Git 版本控制追踪
- **"不存在即开启（Absent = Enabled）"法则**：用户只需要显式"关闭"不想要的特性，而不是打开预设最佳实践，降低认知负荷

**Goal-Backward 方法论**：从目标倒推。每个 Phase 和 Plan 都必须先明确"Goal"，再生成任务列表，而不是枚举任务后拼凑目标。

---

## 2. 全生命周期工作流设计

### 2.1 项目文件系统架构

完整目录结构：
```
.planning/
├── PROJECT.md          # 项目愿景（产品定义、技术栈、成功标准）
├── REQUIREMENTS.md     # 需求文档（需求 ID + 描述 + 验收标准）
├── ROADMAP.md          # 分阶段路线图
├── STATE.md            # 当前状态和决策记录（跨会话恢复入口）
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

### 2.2 Requirement Review 阶段（/gsd-new-project → /gsd-discuss-phase）

**REQUIREMENTS.md 精确结构**：
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

**Phase-based 需求管理**：需求被分配到具体 Phase，形成需求→阶段→计划→任务的四级拆解体系。

### 2.3 Plan 阶段

**PLAN.md 模板（含 YAML frontmatter）**：
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

**独特结构特征**：
- YAML frontmatter 结构化元数据（phase/plan/title/dependencies/estimated_tasks）可被工具解析
- Phase/Plan/Task 三级粒度（比其他框架多一级）
- Context 字段明确引用 RESEARCH.md 和上一阶段 CONTEXT.md，形成知识链路
- 每个 Phase 开始前有 `RESEARCH.md` 领域研究，结果被后续 Plan 和 Execute 阶段引用

### 2.4 STATE.md 跨会话状态管理

这是 GSD 区别于所有竞品的最核心设计之一：
- 精确记录当前项目位置（当前 Phase、当前 Plan、当前 Task）
- 记录关键决策（Why 某个方案被选择）
- 新会话加载 STATE.md 后立刻知道当前状态，无需回放历史
- 配合 `/gsd-progress --next` 可自动检测状态并推荐下一步动作

---

## 3. 工程架构设计

### 3.1 CLI Tools 桥接层
由 TypeScript/Node.js 构建的核心运行时 SDK（`bin/gsd-tools.cjs` 或 `sdk/src/query-runtime-bridge.ts`），负责读取文件状态、验证逻辑以及处理多平台适配。

### 3.2 瘦调度器 + 庞大预设 Prompt 架构
`/gsd-` 开头的命令本身不做重度计算，而是作为"**瘦调度器（Thin Orchestrators）**"：
- 加载上下文后，将工作派发给 `agents/` 目录下的 **33 种**专家 Agent
- 每个 Agent 具备特定工具权限和角色（如 `gsd-plan-checker`、`gsd-executor`、`gsd-ui-auditor`、`gsd-debugger` 等）
- 这种分离保证了主命令极薄，所有智能都沉淀在 Agent 定义中

### 3.3 多平台适配器安装架构
安装器极其健壮，能动态翻译并安装 Workflow 和 Agent 配置到几乎所有主流客户端：
- Claude Code, OpenCode, Codex, Copilot, Cursor, Windsurf, Trae, Cline 等
- 处理了工具名映射（如 Bash 映射为 execute）和环境差异

### 3.4 状态锁机制（State Locking）
为支持 Execute 阶段的高并发 Agent 派发，引入 `STATE.md.lock` 文件锁机制，防止并发修改同一状态文件导致写入冲突。

---

## 4. 核心实现技巧

### 4.1 两级路由节省 Token 预算
面对高达 86 个可用技能，为防止每次全量输出占用极高 Token（约 2150 tokens），框架实现了**双层 Meta-skills 命名空间路由**：
- 第一层：用户看到 6 个大类（如 `gsd-workflow`, `gsd-project`）
- 第二层：再在具体大类下找具体实现
- Token 消耗从约 2150 tokens 降至约 120 tokens，**节省 94%**

### 4.2 Wave Execution Model（波次执行模型）
在 Execute 阶段自动对 `PLAN.md` 任务图进行拓扑分析：
1. 剥离出无前置依赖的任务组成"**第一波（Wave 1）**"
2. 交由多个独立 Agent 完全并行执行
3. 对这批 Agent 关闭 `pre-commit` hook（使用 `--no-verify`）以避免 Git commit 锁竞争
4. Wave 1 完成后进入 Wave 2，依此类推

### 4.3 供应链投毒防线（Package Legitimacy Gate）
引入 `slopcheck` 专门抵御 AI 在引入三方库时出现的幻觉（Slopsquatting）：
- Researcher 和 Planner 阶段对依赖进行注册表校验
- 遇到未认证的（`[ASSUMED]` 或 `[SUS]`）包，强制在 `PLAN.md` 注入 `checkpoint:human-verify` 节点
- 把控最后的供应链安全关卡

### 4.4 Hook 与系统级监听（纵深防御）
在支持的运行环境（如 Claude Code）中，注入深层系统级 Hook：
- `gsd-context-monitor.js`：监听并警告 Token 消耗比例，超 50% 阈值时触发警告
- `gsd-prompt-guard.js`：拦截非法的恶意 Prompt 修改，防止提示词注入攻击
- 形成"**Defense in Depth（纵深防御）**"体系

### 4.5 Context Quality Curve（上下文质量曲线）
明确定义了上下文在对话中随时间衰减的曲线：
- 每个 Phase 都拉起新的干净上下文（Fresh 200k context）
- `RESEARCH.md`、`CONTEXT.md` 等文件作为"知识外化"载体，实现跨会话知识传递
- 而不是依赖模型自身的"记忆"（会随 context 增长而失真）

### 4.6 结构化 Learnings 提取机制
每个阶段结束时自动从阶段制品中提取四类制度记忆：
- **Decisions**（技术决策及理由）
- **Lessons**（执行中才发现的教训）
- **Patterns**（可复用的实现模式）
- **Surprises**（出乎预料的发现）

每条记录附 Source 归因；可 hook 到外部 MCP 知识库，否则降级为本地 `LEARNINGS.md`。

### 4.7 工作流健康检查（/sdlc:health）
提供三级状态诊断：
- **HEALTHY**：状态一致，所有依赖满足
- **DEGRADED**：存在潜在问题但可继续
- **BROKEN**：需要修复才能继续

含具名错误码体系、自动修复（`--repair`）和 Context 利用率检测（`--context`），帮助发现状态不一致、孤立制品、context 超限等问题。

---

## 5. Spec 字段对比优势

| 能力 | GSD | 其他框架 |
|------|:---:|:-------:|
| STATE.md 跨会话状态 | ✅（唯一系统化实现） | 全部缺失或隐式 |
| YAML Frontmatter 元数据 | ✅（可工具解析） | 仅 OpenSpec 有 .openspec.yaml |
| RESEARCH.md 领域研究 | ✅（每 Phase 一份） | 全部没有 |
| Wave-based 并行执行 | ✅（拓扑排序+并行） | 仅 SpecKit 有[P]标记 |
| 供应链安全门控 | ✅（slopcheck） | 全部没有 |
| Context 预算监控 | ✅（~50% 阈值警告） | 全部没有 |
| Learnings 提取 | ✅（四类制度记忆） | 全部没有 |

---

## 6. 对我们框架的借鉴价值

| 借鉴点 | 优先级 | 对应我们的设计 |
|-------|--------|--------------|
| STATE.md 跨会话状态 | ⭐⭐⭐ 高优 | 工作流状态机引擎 status.yaml |
| Wave-based 并行执行 | ⭐⭐ P4-2（已规划） | Phase 4 功能 |
| Interface-First 任务排序 | ⭐⭐ P4-3（已规划） | Phase 4 功能 |
| Learnings 提取机制 | ⭐⭐⭐ P3-9（已规划） | 四类制度记忆提取 |
| 工作流健康检查 | ⭐⭐⭐ P3-10（已规划） | /sdlc:health 三级诊断 |
| 两级路由节省 Token | ⭐⭐ 高优 | Skill 加载器设计参考 |
| Context Quality Curve | ⭐⭐⭐ 高优 | Context 预算监控（§十一 Further Notes） |
| 供应链安全门控 | ⭐ 长期 | 未来安全审计能力 |
