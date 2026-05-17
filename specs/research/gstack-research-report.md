# GStack 深度调研报告

> 仓库：https://github.com/garrytan/gstack  
> Stars：92K  
> 更新时间：2026-05-17

---

## 1. 产品定位与核心哲学

**定位**：致力于将单一 AI 编程助手"升级"为一个拥有记忆、浏览器操作能力和多专家视角的**持久化工程团队**。

**两大核心信念**：
- **突破文本局限，拥抱浏览器生态**：认为做好 Web 和应用开发，仅在终端写代码远远不够。AI 必须能在运行态直接看到 UI 渲染效果、检查 Console 和 Network。产品灵魂是一套为 AI 极度优化的"**持久化无头浏览器环境（Persistent Browser Daemon）**"
- **多维度专家"会诊"体验**：不仅有 `eng-review`（工程评审），还有 `ceo-review`（商业价值把控）、`design-review`（设计视角）、`devex-review`（开发者体验），以及跨模型 `codex` 会诊，完美模拟真实软件公司运作

**独特的市场定位**：
- 行业中唯一将 **"Reflection on How You Think"** 作为结构化字段的框架（AI 对用户思考方式的具体观察，记录在 design doc 里，下次读到会重新遇到）
- 唯一使用 **YC 风格六个强制追问（Six Forcing Questions）** 进行需求压力测试的框架
- 唯一部署本地 **ML 模型（BERT ONNX int8）** 进行 Prompt 注入防御的框架

---

## 2. 全生命周期工作流设计

### 2.1 Requirement Review 阶段（/office-hours → /plan-ceo-review → /plan-eng-review）

**产出文件路径**：`~/.gstack/projects/<project>/design-doc.md`

**design-doc.md 完整模板结构**：
```markdown
# Design Doc: [Project Name]
\Mode\: Startup | Builder
\Date\: [DATE]

## Problem Framing
[Six Forcing Questions 的回答]
1. What is the real problem people have?
2. What is the status quo workaround?
3. Who specifically has this problem?
4. What is the smallest wedge that's valuable?
5. What have you observed users doing?
6. Does this fit where the market is going?

## Original Request
[用户的原始描述]

## Reframed Understanding
[AI 重新理解后的真实需求——可能与原始请求不同]

## Implementation Alternatives
[2-3 个实现方案及 Trade-off 分析]

## Recommended Approach
[推荐方案及理由]

## Scope Decision
[Expansion | Selective Expansion | Hold Scope | Reduction]

## Reflection on How You Think
[AI 对用户思考方式的具体观察——非泛泛的赞扬，而是具体的回调模式]
```

### 2.2 Six Forcing Questions（六个强制追问）

来源于 YC 加速器的需求压力测试方法，强制 AI 在提出解决方案前，先把以下六个问题逐一回答：

| # | 问题 | 目的 |
|---|------|------|
| 1 | What is the real problem people have? | 挖掘根本问题，而非表面症状 |
| 2 | What is the status quo workaround? | 理解当前如何绕过该问题（揭示市场空白） |
| 3 | Who specifically has this problem? | 精确定义目标用户（避免"所有人"的陷阱） |
| 4 | What is the smallest wedge that's valuable? | 找到最小可交付价值切口（MVP 思维） |
| 5 | What have you observed users doing? | 基于观察的事实，而非假设 |
| 6 | Does this fit where the market is going? | 检验方向与市场趋势的契合度 |

### 2.3 Problem Reframing（问题重构）

AI 不按用户说的原样理解需求，而是主动重新诠释"你真正要解决的是什么"，产出：
- `Original Request`：用户的原始描述
- `Reframed Understanding`：AI 重新理解后的真实需求

这不是简单的确认，而是**可能挑战用户原始表述**的主动思维。

### 2.4 Scope Decision（四种范围决策模式）

- **Expansion**：扩展范围，找更大价值（用户可能低估了问题的价值）
- **Selective Expansion**：选择部分扩展（加一些但不全加）
- **Hold Scope**：保持范围（用户定义是合理的）
- **Reduction**：削减范围（防止过度设计）

每次 `/plan-ceo-review` 都会输出一个明确的模式选择，而不是模糊的建议。

### 2.5 四维度多专家评审体系

**CEO Review（/plan-ceo-review）**：
- 商业价值评估
- 范围决策（Expansion / Hold / Reduction）
- 战略一致性检查

**Eng Review（/plan-eng-review）**关键字段：
```markdown
## Architecture
[系统架构设计]

## Data Flow
[ASCII 数据流图]
A → B → C
     ↓
     D

## State Machine
[状态转换图（ASCII）]

## Error Paths
[错误处理路径]

## Edge Cases
[边界情况列表]

## Test Matrix
[测试矩阵：功能 × 场景]

## Failure Modes
[可能的失败模式及恢复策略]

## Security Concerns
[安全威胁分析]
```

**Design Review（/plan-design-review）**：
- UI/UX 一致性
- 交互模式评估
- 可访问性检查

**DevEx Review（/plan-devex-review）**：
- 开发者体验评估
- 调试和可观测性
- 文档和 API 设计

**跨模型会诊（Codex）**：
- 使用不同架构的 AI 模型做第二意见审查
- 覆盖单一模型的盲点和偏见

---

## 3. 工程架构设计

### 3.1 基于 Bun 的编译型守护进程（Daemon Model）
为解决每次启动浏览器的 3-5 秒高昂延迟，使用 `Bun.serve()` 起了一个本地 HTTP 常驻服务，连接并驱动无头 Chromium：
- 第一次启动后，所有交互动作（点击、截图、查询）可以控制在 **100-200ms 极低延迟**
- 跨命令保留状态（Cookie、Session、登录态、DOM 状态）

### 3.2 双重监听器的隧道安全架构
为支持 `pair-agent`（远端配对 Agent）安全访问，严格切分：
- `127.0.0.1:LOCAL_PORT`——仅允许全功能本地访问
- `127.0.0.1:TUNNEL_PORT`——通过 ngrok 穿透的受限沙盒端口

基于物理套接字的隔离，彻底避免 HTTP Header 伪造带来的提权风险。

### 3.3 环形缓冲区日志（Ring Buffer Logging）
为不阻塞极速 HTTP IO 请求，针对浏览器运行时产生的大量 Console、Network 和 Dialog 信息：
- 构建单缓冲 **50,000 记录**的内存环形队列
- 以异步 **1 秒**为周期 Flush 到磁盘 `.log` 文件

### 3.4 非侵入式 DOM 定位系统（The Ref System）
AI 定位页面元素的世界级难题解法：
- 直接注入 `data-ref` 等属性会破坏 React/Vue 的渲染一致性（Hydration Mismatch）甚至被 CSP 拦截
- GStack 利用 Chromium 内部的**无障碍访问树（Accessibility Tree）**，将其展平分配 `@e1`, `@e2` 等逻辑引用
- 通过 Playwright 的 Locator 系统映射点击
- 对于不在无障碍树里却有点击事件的怪异组件，用 `-C` 模式分配 `@c` 开头的单独引用
- **对前端框架零入侵**

---

## 4. 核心实现技巧

### 4.1 五层联动的防注入安全体系
面对 AI 直接抓取外部恶意网站可能带来的指令注入风险，部署了多层防御网：

| 层级 | 机制 | 实现方式 |
|------|------|---------|
| L1 | 内容提取 | 只提取 ARIA 文本，丢弃原始 HTML |
| L2 | 正则过滤 | 过滤常见的注入模式（如 `IGNORE PREVIOUS INSTRUCTIONS`） |
| L3 | 语义清洗 | 移除高风险 HTML 属性和 JavaScript |
| L4 | 本地 ML 分类 | 部署 22MB int8 量化版 BERT ONNX，对读取文本做纯本地无网分类探测 |
| L5 | 金丝雀令牌 | 动态向系统 Prompt 中插入金丝雀令牌，侦听 AI 返回数据流，一旦发现泄漏，判定攻击成功并强制阻断 Session |

### 4.2 基于预设模板与代码反射的动态文档
为防止手写文档与实际支持的指令参数产生漂移，引入 `gen-skill-docs.ts`：
- 基于源码的实际情况动态填充 `{{COMMAND_REFERENCE}}` 等占位符
- 自动生成给模型阅读的 `SKILL.md`
- 从根本上杜绝了幻觉引用（AI 描述了不存在的命令参数）

### 4.3 Reflection on How You Think
结构化字段设计，捕捉 AI 对用户思维模式的具体观察：
- 不是"你的想法很好"这种泛泛赞扬
- 而是具体的"你倾向于先想接口再想数据"、"你在权衡时偏好简单性而非性能"等
- 记录在 design doc 里，下次读到时 AI 会重新遇到这些观察，形成**持续性的用户理解**

### 4.4 跨会话项目记忆系统
通过 `~/.gstack/projects/` 目录维护的持久化项目状态：
- 设计文档（design-doc.md）跨会话持久
- 多次会话的决策可以累积到同一个 design doc
- 不同会话的 AI 可以读取历史设计上下文，实现**连贯的长程工程合作**

---

## 5. Spec 字段对比优势

| 能力 | GStack | 其他框架 |
|------|:------:|:-------:|
| Six Forcing Questions | ✅（唯一） | 全部缺失 |
| Problem Reframing | ✅（唯一） | 全部缺失 |
| Reflection on How You Think | ✅（唯一） | 全部缺失 |
| 四维度独立专家评审 | ✅（唯一） | 全部缺失 |
| 浏览器运行时感知 | ✅（唯一） | 全部缺失 |
| 本地 ML Prompt 注入防御 | ✅（唯一） | 全部缺失 |
| ASCII 数据流图/状态机图 | ✅ | 仅 Superpowers 有 |
| 跨模型第二意见（Codex） | ✅ | 全部缺失 |

---

## 6. 对我们框架的借鉴价值

| 借鉴点 | 优先级 | 对应我们的设计 |
|-------|--------|--------------|
| 多维度评审视角（CEO/Design/Eng/DX） | ⭐⭐⭐ P3-6（已规划） | Review 阶段多视角扩展 |
| Six Forcing Questions | ⭐⭐ 高优 | Requirement Review 阶段需求质询协议 |
| Problem Reframing | ⭐⭐⭐ 高优 | Requirement Review 阶段问题重构能力 |
| Eng Review 的 ASCII 图 | ⭐⭐ 中优 | Plan Spec 中架构图设计 |
| Scope Decision 四种模式 | ⭐⭐ 中优 | Plan 阶段范围决策结构 |
| 安全审计（OWASP + STRIDE） | ⭐⭐ P4-5（已规划） | Review 阶段安全维度 |
| 跨模型第二意见 | ⭐ P4-6（已规划） | 交叉验证机制 |
| 本地 ML 注入防御 | ⭐ 长期 | 执行环境安全（v2.0+） |
