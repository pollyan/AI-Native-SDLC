# AI-Native SDLC

> 规范驱动的 AI 原生软件开发生命周期框架

---

## 这是什么

AI-Native SDLC 是一套面向企业开发团队的工作流框架，将 AI 编码工具嵌入标准软件开发流程的每一个阶段——需求评审、方案设计、开发执行、代码评审。

框架通过 **Skill 文件**（`SKILL.md`）约束 AI 的行为，通过 **Spec 文件**（`.md`）在阶段间传递结构化信息，用 **CONTEXT.md** 统一项目术语，构建一套"宽进严出"的 AI 辅助开发流水线。

---

## 快速上手

参阅 **[安装指南](docs/installation-guide.md)**，覆盖以下三种 AI 工具的安装方式：

- **Cursor** — 通过 `.cursorrules` 加载 Skill
- **Claude Code** — 通过 `CLAUDE.md` + Slash Commands 加载 Skill
- **Windsurf** — 通过 `.windsurfrules` 加载 Skill

安装后建议完成一次 [Hello World 端到端验证](docs/installation-guide.md#第一次使用hello-world)。

---

## 工作流

```
需求输入
    ↓
[Requirement Review]  →  specs/requirement-spec-<功能名>.md
    ↓ (Developer 签署)
[Plan]                →  specs/plan-spec-<功能名>.md
    ↓ (Developer 签署)
[Execute]             →  代码变更（按切片 Git 提交）
    ↓
[Review]              →  specs/review-report-<功能名>.md
    ↓ (Developer 签署)
完成
```

每个阶段都有门控（Gate Checklist）。**Developer 签署后才能进入下一阶段。**

---

## 目录结构

```
AI-Native-SDLC/
├── CONTEXT.md                          # 本框架的领域词汇表（术语唯一真相来源）
├── AGENTS.md                           # AI Agent 行为规范
│
├── skills/                             # 四个阶段的 Skill 定义
│   ├── README.md                       # Skills 目录说明与命名约定
│   │
│   ├── requirement-review/             # 阶段一：需求评审
│   │   ├── SKILL.md                    # Skill 定义（6章节）
│   │   ├── CONTEXT-FORMAT.md           # 项目 CONTEXT.md 的格式规范
│   │   └── templates/
│   │       └── requirement-spec.md     # Requirement Spec 输出模板
│   │
│   ├── plan/                           # 阶段二：方案设计
│   │   ├── SKILL.md                    # Skill 定义（6章节）
│   │   └── templates/
│   │       └── plan-spec.md            # Plan Spec 输出模板
│   │
│   ├── execute/                        # 阶段三：开发执行
│   │   └── SKILL.md                    # Skill 定义（6章节，无输出模板）
│   │
│   └── review/                         # 阶段四：代码评审
│       ├── SKILL.md                    # Skill 定义（6章节）
│       └── templates/
│           └── review-report.md        # Review Report 输出模板
│
├── docs/
│   ├── installation-guide.md           # 安装指南（Cursor / Claude Code / Windsurf）
│   └── adr/                            # 架构决策记录
│
├── specs/                              # 项目使用时的 Spec 文件存放目录（不入版本控制）
│
└── archive/                            # 历史版本与设计决策存档
```

---

## 四个 Skill 概览

### Requirement Review — 需求评审

**输入：** 任意形式的需求描述（口头、Issue、PRD 片段均可）  
**产出：** `specs/requirement-spec-<功能名>.md`

核心机制：
- **Problem Reframing**：区分"Developer 说的"和"真正需要的"，产出 Original Request vs Reframed Understanding 对比
- **Scope Decision**：四选一（Expansion / Selective Expansion / Hold Scope / Reduction）+ 理由
- **4 个提问触发器**：模糊词、边界缺失、矛盾、业务规则假设——命中任意一个立即停下提问
- **术语拦截**：加载 CONTEXT.md，Avoid 词汇触发 `❌ 矛盾` 标注

### Plan — 方案设计

**输入：** `specs/requirement-spec-<功能名>.md`（已签署）  
**产出：** `specs/plan-spec-<功能名>.md`

核心机制：
- **垂直切片强制**：每个切片必须贯穿所有相关集成层（Schema / API / UI / Tests），禁止水平切片
- **切片独立可验证**：每个切片完成后可单独 Demo 或验证，不依赖后续切片
- **3 个提问触发器**：FR 覆盖歧义、技术方案缺失、依赖关系不明

### Execute — 开发执行

**输入：** `specs/plan-spec-<功能名>.md`（已签署）  
**产出：** 代码变更（按切片 Git 提交）

核心机制：
- **前置就绪检查**：Plan Spec 中 AC 含 TODO/TBD 或缺失时阻断执行
- **严格范围边界**：范围外问题只标注 `[范围外发现]` 不处理
- **四阶段调试协议**：根因调查 → 模式分析 → 假设验证 → 修复实施；连续 3 次失败强制上报
- **完成声明**：附完整测试输出和逐条 AC 证据

### Review — 代码评审

**输入：** Requirement Spec + Plan Spec + 代码变更  
**产出：** `specs/review-report-<功能名>.md`

核心机制：
- **三轴独立审查**：代码质量 / 需求一致性 / 验收标准验证，三个轴在独立上下文中执行
- **强制定位**：每条发现必须附 `文件:行号`，禁止模糊描述
- **Developer 决策记录**：每条 Critical 发现必须等待 Developer 明确决策

---

## CONTEXT.md 机制

### 框架的 CONTEXT.md（本仓库）

`CONTEXT.md` 在本仓库根目录，定义**框架自身的术语**（Skill、Spec、Developer、Vertical Slice 等），是框架开发者的词汇统一来源。

### 客户项目的 CONTEXT.md

每个使用本框架的**客户项目**在其根目录维护自己的 `CONTEXT.md`（项目特有业务术语）：
- 由 Requirement Review Skill 在首次确认项目术语时**懒惰创建**
- 格式参照 `skills/requirement-review/CONTEXT-FORMAT.md`
- 其他 Skill（Plan / Execute / Review）加载使用，若不存在则静默跳过

---

## 设计原则

1. **人主控，AI 辅助** — AI 提出建议，Developer 做所有关键决策
2. **Spec 是阶段间唯一传递载体** — 不通过口头描述传递信息，必须写入 Spec 文件
3. **宽进严出** — 接受任何格式的输入，输出必须通过 Gate Checklist 才能交付
4. **按需加载** — 模板只在需要时加载（`template_ref` 渐进式加载），不在初始化时占用上下文
5. **懒惰创建** — CONTEXT.md 在第一次需要时才创建，不预先提供空白模板

---

## 开发现状

| Issue | 状态 | 说明 |
|-------|------|------|
| #1 Requirement Review Skill | ✅ 完成 | |
| #2 Plan Skill | ✅ 完成 | |
| #3 Execute Skill | ✅ 完成 | |
| #4 Review Skill | ✅ 完成 | |
| #5 Installation Guide | ✅ 完成 | |
| #6 Hello World 端到端验收 | ⏳ 进行中 | 需要 FDE 手动完成 |

---

## 相关文档

- [安装指南](docs/installation-guide.md) — 安装、初始化和故障排查
- [Skills 目录说明](skills/README.md) — Skill 文件结构和命名约定
- [CONTEXT.md 格式规范](skills/requirement-review/CONTEXT-FORMAT.md) — 如何写项目词汇表
- [架构决策记录](docs/adr/) — 重大设计决策存档
- [AGENTS.md](AGENTS.md) — AI Agent 行为边界规范
