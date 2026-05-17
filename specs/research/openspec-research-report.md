# OpenSpec 深度调研报告

> 仓库：https://github.com/Fission-AI/OpenSpec ｜ Stars：46K ｜ 更新时间：2026-05-17

---

## 1. 产品定位与核心哲学

**定位**：反瀑布流的"**Fluid Not Rigid（流体非刚性）**"与"**工件引导（Artifact-guided）**"开发哲学。在所有竞品中，OpenSpec 最接近 Git 协作模式的规范驱动框架。

**三大核心信念**：
- **极简动作**：核心只有三个动作：提议（`/opsx:propose`）、应用（`/opsx:apply`）、归档（`/opsx:archive`）
- **特征级无锁隔离**：任何变更在 `changes/<feature>` 沙箱内完全隔离讨论，成熟才合入主干
- **源事实与变更分离**：`specs/` 是系统当前行为的真实描述；`changes/` 是提案。两者严格分离

**独特市场定位**：
- 行业唯一的 **Delta Spec（增量规范）**——只描述变化（ADDED/MODIFIED/REMOVED），不全量重写
- 唯一使用 **RFC 2119 关键词**（SHALL/MUST/SHOULD）表达需求强度
- 唯一采用 **Action-based 非线性工作流**（不按顺序走阶段，随时 propose/apply/archive）
- 唯一将**需求历史归档**设计为一等公民（REMOVED 必须附带 Reason + Migration）

---

## 2. 全生命周期工作流设计

### 2.1 文件系统架构

```
openspec/
├── specs/                    # 源事实（当前系统行为）
│   └── <domain>/spec.md
├── changes/                  # 变更提案（与 specs/ 严格分离）
│   └── <change-name>/
│       ├── proposal.md       # Why and what
│       ├── design.md         # How（技术方案）
│       ├── tasks.md          # 实现清单
│       ├── .openspec.yaml    # 结构化元数据
│       └── specs/<domain>/spec.md  # Delta specs（仅变更部分）
├── config.yaml
└── archive/YYYY-MM-DD-<feature>/   # 归档（只读快照）
```

### 2.2 Spec 格式（RFC 2119 层级）

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

**RFC 2119 关键词**：
- `SHALL / MUST`：绝对要求，不可协商
- `SHOULD`：强烈推荐，有充分理由可不遵守但需说明
- `MAY`：真正可选
- `MUST NOT`：绝对禁止

### 2.3 Delta Spec 格式（核心创新）

核心创新：每次变更**不全量重写**，只描述变化：

```markdown
## ADDED Requirements
### Requirement: [New requirement]
The system SHALL [new capability].
#### Scenario: [Name]
Given [context] / When [action] / Then [outcome]

## MODIFIED Requirements
### Requirement: [Changed requirement]
OLD: The system MUST [old behavior]
NEW: The system SHALL [new behavior]

## REMOVED Requirements
### Requirement: [Removed requirement]
REASON: [Why this requirement is being removed]
MIGRATION: [How existing users should adapt]
```

### 2.4 Proposal / Design / Tasks 三阶文件

**proposal.md**（Why and What）：
```markdown
# Proposal: [Change Name]
## Motivation / Scope / Expected Impact
```

**design.md**（How，技术实现）：
- 技术方案、架构决策、数据模型变更、API 变更、风险评估

**tasks.md**（可追溯实现清单）：
```markdown
- [ ] Task 1: Update schema
  - Domain: auth
  - Spec Delta: auth/spec.md#requirement-session-management
- [ ] Task 2: Implement API (Dependencies: Task 1)
```

### 2.5 归档流程（/opsx:archive）

1. `changes/<feature>` 目录转移到 `archive/YYYY-MM-DD-<feature>`（只读快照）
2. delta specs 自动 merge 到 main `specs/`（源事实更新）
3. 触发 Context Hygiene——通知协同 AI 引擎清理旧会话缓存
4. 下一个特征享受干净 Context Window

---

## 3. 工程架构设计

### 3.1 Unix 哲学极简架构
- 没有数据库——文件即状态
- 没有 daemon 进程——命令即触发
- 目录即分支（`changes/<feature>` 类似 Git branch）

### 3.2 配置与运行时深度解耦
通过 `openspec config profile` 将能力接口 Schema 化：
- 不绑定任何 AI 厂商
- 支持 25+ 工具（只负责文件变更追踪，写代码交给 Cursor/Claude Code）

### 3.3 .openspec.yaml 结构化元数据

```yaml
name: add-oauth-support
status: in-progress  # draft | in-progress | ready | archived
created: 2026-05-17
domains: [auth, user]
dependencies: [base-auth-setup]
```

### 3.4 多语言 Schema 映射引擎
不同语言/框架（Go, Rust, React）有各自的可拔插解析器，生成的 tasks.md 能精确打击到组件层级。

---

## 4. 核心实现技巧

### 4.1 基于文件的隐式状态机
系统通过文件 Diff 机制（而非数据库）感知阶段推进：
- 检测到 `tasks.md` 所有复选框被勾选 → 触发归档推荐
- 不需要用户手动宣布"阶段完成"

### 4.2 非线性 Action-based 工作流
- 随时可以回到 Proposal 修改动机
- 随时并行维护多个 changes（类似 Git 多分支）
- 支持"随时回退到 Plan"的动态工作流

### 4.3 Spec Delta Archive（需求历史归档）
- 完整的需求演化历史，可"时间旅行"到任何历史需求状态
- `REMOVED` 必须附带 Reason + Migration 迁移路径
- 防止需求悄悄消失而不留痕迹

---

## 5. Spec 字段对比优势

| 能力 | OpenSpec | 其他框架 |
|------|:--------:|:-------:|
| Delta Spec（增量规范） | ✅（唯一） | 全量重写 |
| RFC 2119 关键词 | ✅（唯一） | 缺失 |
| 非线性 Action-based 工作流 | ✅（唯一） | 线性阶段化 |
| REMOVED 必须附带 Migration | ✅（唯一） | 缺失 |
| 源事实 vs 变更提案分离 | ✅（唯一） | 混在一起 |
| 工具无关性（25+） | ✅ | GSD 也有 |
| 变更级别 .yaml 元数据 | ✅ | GSD 有 Plan 级 YAML |

---

## 6. 对我们框架的借鉴价值

| 借鉴点 | 优先级 | 对应我们的设计 |
|-------|--------|--------------|
| Action-based 非线性工作流 | ⭐⭐ P4-1（已规划） | Phase 4 功能 |
| Spec Delta Archive | ⭐⭐ P4-8（已规划） | 需求历史归档 |
| RFC 2119 关键词 | ⭐⭐⭐ 高优 | Requirement Spec 需求强度标注 |
| REMOVED 必须附带 Migration | ⭐⭐⭐ 高优 | Spec 变更门控机制 |
| 文件即状态的极简设计 | ⭐⭐⭐ 高优 | 工作流状态机文件优先设计 |
| Context Hygiene 机制 | ⭐⭐⭐ 高优 | 阶段切换时的上下文清理策略 |
