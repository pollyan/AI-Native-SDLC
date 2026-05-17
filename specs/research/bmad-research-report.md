# BMAD Method 深度调研报告

> 仓库：https://github.com/bmad-code-org/BMAD-METHOD ｜ Stars：47K ｜ 更新时间：2026-05-17

---

## 1. 产品定位与核心哲学

**定位**：宣扬"AI 作为专家协作者（AI as an Expert Collaborator）"而非完全代工工具。核心理念是通过**多角色会议机制**还原真实开发团队体验。

**两大核心哲学**：
- **派对模式（Party Mode）**：用户不是单独跟一个冷冰冰的"AI"对话，而是召唤出 Product Manager（PM）、Frontend Engineer（FE）、Architect 等角色进入群聊（Context），多角色甚至会在同一 Prompt 流里"互相辩论"和"补充设计"
- **规模与领域自适应（Scale-Domain-Adaptive）**：不同项目需求自动路由到不同工作流（34 种内置工作流），小项目走极速通道直接写代码，大系统走完整架构、UML 设计流

**独特市场定位**：
- 行业中 **PRD 引导流程**最完整（12 步，每步有独立 .md 文件）
- 唯一使用 **PRFAQ（Press Release + FAQ）**亚马逊风格倒逼需求清晰化
- 唯一系统实现 **三层配置合并**（base → team → user）的框架
- 唯一有 **persistent_facts 角色记忆**（支持文件引用和 glob 路径）

---

## 2. 全生命周期工作流设计

### 2.1 工作流产出文件体系

```
_bmad-output/
├── planning-artifacts/
│   ├── brainstorming-report.md    # 阶段 1：头脑风暴报告
│   ├── product-brief.md           # 项目简报
│   ├── prfaq-{project}.md         # PRFAQ 文档（亚马逊风格）
│   ├── PRD.md                     # 阶段 2：产品需求文档
│   ├── ux-spec.md                 # UX 设计规范
│   ├── architecture.md            # 阶段 3：架构文档
│   ├── epics/
│   │   ├── epic-1.md
│   │   └── story-[slug].md
│   └── sprint-status.yaml         # Sprint 跟踪
├── implementation-artifacts/
└── project-context.md             # 项目上下文（宪法）
```

### 2.2 PRFAQ（Press Release + FAQ）

亚马逊内部流程的精髓：先写新闻稿和 FAQ，倒逼需求清晰化：

```markdown
# Press Release: [Product Name]

FOR IMMEDIATE RELEASE

[City, Date] — [Company Name] today announced [Product Name], 
a [brief description] that [key benefit].

## Problem
[The problem you're solving in 2-3 sentences]

## Solution  
[How your product solves it]

## Key Features
- Feature 1: [Benefit]
- Feature 2: [Benefit]

## Quote
"[Vision statement from fictional CEO]"

---

# FAQ

Q: Who is this for?
A: [Target user definition]

Q: How is this different from [competitor]?
A: [Differentiation]

Q: What does it cost?
A: [Pricing/access model]
```

**PRFAQ 的价值**：在开始 PRD 之前，强制团队用"发布时的新闻稿"描述产品——如果无法写出让人兴奋的新闻稿，说明价值主张还不够清晰。

### 2.3 PRD 模板（12 步引导流程）

**12 步 PRD 引导流程（bmad-create-prd → bmad-prd）**：
1. Discovery — 理解产品愿景
2. User Personas — 定义用户画像
3. Problem Statement — 问题陈述
4. Epics Definition — Epic 定义
5. User Stories — 用户故事
6. Functional Requirements — 功能需求（FR 系列）
7. Non-Functional Requirements — 非功能需求（NFR 系列）
8. Constraints — 约束条件
9. Assumptions — 假设
10. Dependencies — 依赖
11. Validation Checklist — 验证检查
12. Polish — 最终打磨

**PRD 模板结构**：
```markdown
# Product Requirements Document

## Product Overview
### Product Vision / Target Users / Problem Statement

## Epics
### Epic 1: [Name]
- Description / Business Value

## User Stories
### US-001: [Story Title]
**As a** [user type], **I want** [goal], **so that** [benefit]
- **Priority**: [P0/P1/P2]
- **Story Points**: [N]
- **Acceptance Criteria**: Given/When/Then
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

## Constraints & Assumptions / Out of Scope / Open Questions
```

**FR/NFR 严格分离**是 BMAD 的独特贡献：
- 功能需求（FR）：系统做什么
- 非功能需求（NFR）：系统的质量属性（性能/安全/可扩展性...）
- NFR 必须有明确的 Category 分类和可量化的 Measurement（而不是"系统必须很快"）

### 2.4 角色体系（12+ 种专家画像）

| 角色 | 职责 |
|------|------|
| Analyst | 业务分析、PRFAQ 创作 |
| PM（Product Manager） | PRD 创作、优先级排序 |
| Architect | 技术架构设计 |
| Frontend Engineer | UI/前端实现 |
| Backend Engineer | 后端/API 实现 |
| QA | 测试策略和用例 |
| DevOps | 部署和基础设施 |
| Security | 安全审计 |
| ... | （共 12+ 种） |

**角色的 persistent_facts 设计**：
- 每个角色可以记住持久事实（技术栈选型、项目约束、用户偏好）
- 支持文件引用：`persistent_facts: [path/to/tech-stack.md]`
- 支持 glob 路径：`persistent_facts: [docs/**/*.md]`
- 跨会话引用，形成真正的角色"记忆"

---

## 3. 工程架构设计

### 3.1 技能树双层分离

```
src/
├── core-skills/        # 适配层（与 Claude Code、Cursor 等平台对接）
└── bmm-skills/         # 方法论层
    ├── personas/        # 12+ 种专家画像
    └── macros/          # 30+ 种方法论宏
```

**core-skills**：负责与具体 Agent 平台对接的适配层和底层原子指令  
**bmm-skills（BMad Method Skills）**：包含高层专家画像和方法论宏

### 3.2 三层配置合并

```
base (框架默认)
    ↓ 覆盖
team (项目级，.bmad/config.yaml)
    ↓ 覆盖
user (个人级，~/.bmad/config.yaml)
```

- **标量覆盖**：user 层覆盖 team 层的单值配置
- **表深度合并**：数组和对象做深度合并而不是替换
- 允许团队统一配置同时支持个人定制

### 3.3 去中心化的状态路由器（bmad-help）
没有强类型的外部运行时，巧妙地将 `bmad-help` 技能打造成软路由：
- 用户不知道该做什么时，询问 `bmad-help`
- AI 读取当前工程下的 Markdown 工件状态（是否存在设计文档）
- 在内部流转并推荐下一个需要触发的专家或指令

### 3.4 无头 CI/CD 安装能力
提供通过 `--set` 参数无头（Headless）运行的能力：
- 可在企业 Git Actions 环境里作为前置架构师（Pre-commit/Pre-PR Architect）使用
- 极大地扩宽了在自动化流水线的应用边界

---

## 4. 核心实现技巧

### 4.1 Prompt 宏与文件展开机制
底层实现了复杂的安装脚本，处理 Prompt 之间的继承与包含关系：
- 专家定义通过 `{{include}}` 语法动态拉取通用编码规范
- 分发给本地 `SKILL.md` 时完成类似 C++ 宏展开的预编译动作
- 团队共享基础规范，各角色按需扩展

### 4.2 隐式记忆管理
相较于其他框架强迫每次建立新上下文，BMAD 依赖底层大模型的大窗口（Long Context）：
- 通过规范化输出文档格式，让模型形成"写入本地 → 在下个对话周期读取本地"的长程连贯行为
- `persistent_facts` 机制是这一模式的显式化版本

### 4.3 Implementation Readiness Check（实现前就绪检查）
实现前验证所有规划文档的一致性：
- 所有 Epic 都有对应 Story 吗？
- 所有 Story 都有 Acceptance Criteria 吗？
- 所有 FR 都追踪到具体 Epic 吗？
- NFR 都有 Measurement 吗？

### 4.4 基于领域的微调能力
系统支持通过提供特定"业务语料"进行软切换：
- 不仅能当传统 Web 开发专家
- 加载特定 Module 就能变身 Game Dev Studio
- 表现出极强的跨领域（Domain）延展性

---

## 5. Spec 字段对比优势

| 能力 | BMAD | 其他框架 |
|------|:----:|:-------:|
| PRFAQ（亚马逊风格） | ✅（唯一） | 缺失 |
| 12 步 PRD 引导流程 | ✅（最完整） | 简化版 |
| FR/NFR 严格分离（含 Measurement） | ✅ | 部分有但不完整 |
| 三层配置合并 | ✅（唯一系统实现） | 缺失 |
| persistent_facts 角色记忆 | ✅（唯一，含文件引用） | 缺失 |
| 12+ 专家角色画像 | ✅（最丰富） | 其他 3-5 个 |
| 派对模式多角色并发 | ✅（唯一） | 缺失 |
| Implementation Readiness Check | ✅ | 缺失 |

---

## 6. 对我们框架的借鉴价值

| 借鉴点 | 优先级 | 对应我们的设计 |
|-------|--------|--------------|
| 三层配置合并 | ⭐⭐⭐ P3-1（已规划） | base/team/user 三层配置 |
| persistent_facts 角色记忆 | ⭐⭐⭐ P3-2（已规划） | 跨会话持久事实 |
| FR/NFR 严格分离 | ⭐⭐ 高优 | Requirement Spec 模板字段设计 |
| 12 步引导流程 | ⭐⭐ 中优 | Requirement Review Coaching 模式 |
| PRFAQ 倒逼需求清晰 | ⭐⭐ 中优 | 产品愿景澄清环节 |
| Implementation Readiness Check | ⭐⭐⭐ 高优 | Execute 阶段前置就绪检查 |
| Prompt 宏展开机制 | ⭐⭐ 中优 | Skill 模板引擎参考 |
| 无头 CI/CD 运行 | ⭐ 长期 | GitHub Actions 集成（v2.0） |
