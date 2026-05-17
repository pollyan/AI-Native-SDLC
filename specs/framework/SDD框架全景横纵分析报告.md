# SDD框架全景横纵分析报告
> 研究时间：2026-05-14 | 所属领域：AI辅助软件工程 / Spec-Driven Development | 研究对象类型：开发方法论、工具链、IDE、代理框架与相邻生态

## 一、一句话定义

SDD 这轮爆发，不是「多写一点需求文档」，而是 AI 编程从补全器时代进入代理时代以后，开发者重新需要一套可以约束模型、沉淀意图、拆解任务、追踪执行、验证结果的工程操作系统。

如果只看几个热门开源仓库，会误以为这个赛道只有 Spec Kit、OpenSpec、GSD、Superpowers、gstack。真正把市场摊开看，会发现它已经分成五层：原生 SDD IDE，开源 SDD 工具链，多代理 SDLC 方法论，轻量 spec 协议，以及文档/上下文/任务管理相邻工具。

本报告把主流和重要相邻项目纳入同一张图里：Kiro、GitHub Spec Kit、OpenSpec、GSD、Superpowers、gstack、BMAD-METHOD、Task Master、Agent OS、Spec Workflow MCP、IntentSpec、Auto、Augment Intent、pre.dev、Traycer、BetterSpec、Planu、Spec Native、ADD、MetaGPT、Cursor Rules、Google Code Wiki / DeepWiki 等。它们不是同一层竞争，但都在回答同一个问题：当 AI agent 能自己改代码以后，人类如何把「我要什么」变成可以长期执行、可以审查、可以复盘的系统？

## 二、研究范围与筛选标准

这次不按用户点名项目收窄，而按市场全景筛选。纳入分析的标准有四条：

1. 是否明确使用 spec-driven、spec-first、requirements/design/tasks、PRD-to-tasks、intent/spec-as-source 等 SDD 相关工作流。
2. 是否已经有可公开访问的产品、文档、GitHub 仓库、npm 包、MCP server、IDE 功能或社区采用证据。
3. 是否对 AI coding agent 的实际开发流程产生作用，而不只是泛泛的文档生成。
4. 是否在 2025-2026 年 SDD 讨论中被反复提及，或在 GitHub stars、产品背书、生态入口上形成可见势能。

因此，本报告不会把所有小型 Claude Code slash command、单人模板、一次性 prompt 包都逐一展开。它们会被归到「长尾工具」和「观察名单」。否则全景会变成目录站，而不是判断。

## 三、纵向分析：从传统规格到 AI 代理工程

### 1. 老问题：文档离代码太远，代码离意图太远

SDD 的源头比 AI 更早。

软件工程一直想解决一个朴素问题：人脑里的意图，怎么变成团队和机器都能稳定执行的东西。瀑布时代的答案是需求规格说明书；敏捷时代把厚文档压缩成用户故事和验收标准；TDD 把规格放到测试里；BDD 用 Gherkin 和 Given/When/Then 把业务语言变成可执行例子；API-first、OpenAPI、AsyncAPI、Pact、契约测试又把接口行为变成机器可校验的契约。

所以今天的 SDD 不是凭空出现。它更像老问题在 AI 时代重新长出新器官。

区别在于，过去规格主要写给人看。现在规格同时写给人和 agent 看。它既要让产品、设计、工程对齐，也要能被 Claude Code、Codex、Copilot、Cursor、Kiro、Gemini CLI 这样的 agent 消费。规格如果只是漂亮文档，仍然会漂移；规格如果能驱动任务、测试、审查和执行，才有机会变成工程资产。

Thoughtworks 在 2025 年 Technology Radar 把 spec-driven development 放进 Assess，给出的信号很微妙：值得探索，但还没到无脑推广。这个判断很准确。SDD 已经从概念变成工具潮，但成熟实践还在快速试错。

### 2. 2023-2024：多代理软件公司和上下文工程的前史

现代 SDD 工具爆发前，有两条重要前史。

第一条是多代理软件公司路线。MetaGPT 在 2023 年出现，定位是「第一个 AI software company」式的多代理框架。它不是严格意义上的 SDD 工具，但它把产品经理、架构师、工程师等角色拆成 agent，并让自然语言需求沿着角色链条变成软件项目。这为后来 gstack、BMAD 这类角色化框架提供了想象空间。

第二条是上下文工程。早期 `.cursorrules`、`CLAUDE.md`、`AGENTS.md`、项目 README、架构文档、prompt 模板，都是在解决同一件事：不要让模型每次都从零猜项目规则。Cursor Rules 后来发展成 `.cursor/rules/` 下不同触发模式的规则系统，Claude Code 和 Codex 也都鼓励把项目规范放入仓库上下文。这些东西不是完整 SDD，但它们是 SDD 的土壤。

当 agent 只会补全代码时，这些上下文文件只是提示增强；当 agent 可以跨文件改动、跑测试、开 PR 时，它们就开始承担治理职责。

### 3. 2025上半年：Task Master、BMAD、Agent OS先把「计划」产品化

2025 年 3 月，Task Master 的仓库 `eyaltoledano/claude-task-master` 创建。它的官方定位是可以放进 Cursor、Lovable、Windsurf、Roo 等环境的 AI task-management system。它不是传统意义上的 spec 工具，而是把 PRD 解析成任务、子任务、依赖、复杂度和执行顺序。到 2026-05-14，GitHub API 显示它有约 27,126 stars、2,529 forks，npm `task-master-ai` latest 是 0.43.1，1.0.0 仍在 rc。

Task Master 的关键意义是：它抓住了 AI 编程真正痛点之一不是「不会写」，而是「不知道下一步该写什么」。很多团队的第一次 SDD 实践不是从完整 spec schema 开始，而是从 PRD-to-tasks 开始。先把模糊需求拆成可跟踪任务，agent 才不会在大需求里乱撞。

2025 年 4 月，BMAD-METHOD 创建。它的全称是 Breakthrough Method for Agile AI Driven Development。到 2026-05-14，它有约 47,092 stars、5,517 forks。BMAD 的姿态不是轻工具，而是完整 AI-driven agile framework。它用 Analyst、Product Manager、Architect、Developer、QA、Scrum Master 等角色，把传统敏捷团队搬进 agent 世界。

BMAD 的价值在于它把 SDD 从「写 spec」扩展为「跑一个 AI 敏捷团队」。它的模块体系也很明显：核心 BMad Method、Builder、Test Architect、Game Dev Studio、Creative Intelligence Suite。它关注的是从分析、计划、方案到实现的全生命周期，而不只是某个 `requirements.md`。

2025 年 7 月，Agent OS 创建。它的定位很直接：把 codebase standards 注入 agent，并写出更好的 specs。它比 BMAD 轻，比 Spec Kit 更偏「团队标准 + 规格写作」。到 2026-05-14，GitHub API 显示约 4,526 stars、724 forks。Agent OS 的出现说明一个趋势：很多团队并不想上完整 SDLC 框架，只想让 agent 在现有代码库里更懂规则、更会写 spec。

### 4. 2025年中：Kiro把SDD放进IDE，Spec Kit把SDD推向开源主流

Kiro 是这轮 SDD 最重要的产品化节点之一。AWS 在 2025 年 7 月公开 Kiro，口号是从 vibe coding 到 viable code。Kiro 官方文档把 specs、steering、hooks 列为核心能力：specs 把高层想法变成 requirements、design、tasks；steering files 持久化项目规范；agent hooks 在文件创建、保存、删除、prompt submit、agent stop、tool use、spec task 等事件上触发自动动作。

Kiro 的路线很清晰：把 spec 变成 IDE 内的工作单元。用户不是先在聊天里让模型写代码，而是先生成 requirements.md、design.md、tasks.md，再逐步执行任务。它的优势是原生体验和企业叙事，缺点是绑定 IDE 和 AWS 生态更深，规格仍主要是 markdown prose，不是天然可执行测试。

同一时期，GitHub Spec Kit 进入主流视野。GitHub 官方博客把它定位为开源 SDD toolkit，支持 GitHub Copilot、Claude Code、Gemini CLI 等 agent workflow。GitHub 仓库创建于 2025-08-21，到 2026-05-14 约 98,488 stars、8,577 forks，最新 release v0.8.9 发布于 2026-05-12。

Spec Kit 的历史位置很特殊。它不是功能最重的，也不是最先的，但它最像公共入口。GitHub 的背书让 SDD 从社区实践变成可被大多数开发者尝试的默认模板。它的核心流程围绕 specify、plan、tasks、implement，并通过项目模板和扩展 catalog 让不同 agent 使用同一组制品。

如果 Kiro 代表「原生 IDE 化」，Spec Kit 代表「开源标准入口化」。

### 5. 2025下半年：OpenSpec、Spec Workflow MCP、IntentSpec走向制品化与协议化

OpenSpec 的仓库创建于 2025-08-05，npm `@fission-ai/openspec` 在 2025-09-06 发布 0.1.0，2026-01-26 到 1.0.0，2026-04-21 到 1.3.1。它的定位是面向 AI coding assistants 的 SDD。和 Spec Kit 相比，OpenSpec 更重视 change proposal、delta spec、apply、archive、validation 这些工程制品。

OpenSpec 的特点是克制。它不试图扮演 PM 或 CEO，而是把「变更如何被提出、应用、归档」做成可追踪流程。它对 brownfield 项目特别有意义，因为老系统不可能一次性写完完整规格。更现实的做法是写 delta：这次新增什么、修改什么、移除什么。OpenSpec 在这条路线上很有优势。

同在 2025 年 8 月，Pimzino 的 Spec Workflow MCP 创建，官方描述是一个 MCP server，为 AI-assisted software development 提供 structured spec-driven workflow tools，并带实时 dashboard 和 VS Code extension。它可以看作 Kiro-style workflow 的开放 MCP 化版本。它之前还有 Claude Code 专用的 `claude-code-spec-workflow`，后者 README 明确说明开发重心已经转向 MCP 版本。到 2026-05-14，Spec Workflow MCP 约 4,170 stars、344 forks，npm latest 2.2.5；旧 Claude Code 版约 3,726 stars、264 forks，但更新趋缓。

IntentSpec 则走另一条路：极简开放标准。它让团队提交一个 `intent.md`，用 YAML frontmatter 定义 objective、outcomes、constraints、edgeCases、healthMetrics，并可用 JSON Schema 和 GitHub Action 做格式校验。它的优点是工具无关，Claude Code、Cursor、Windsurf、Augment、Copilot 都能读。缺点也明显：它能验证 spec 形状，却不能证明代码满足 spec。

这一阶段的共同特征是：大家开始意识到 SDD 不应该锁死在单个 IDE 或 agent 里。spec 要能进 Git，要能被 MCP 暴露，要能被 CI 校验，要能跨工具迁移。

### 6. 2025末到2026：GSD、Superpowers、gstack把SDD推向执行纪律和组织角色

GSD 的仓库创建于 2025-12-14，npm `get-shit-done-cc` 在 2025-12-15 发布 1.0.0，到 2026-05-10 latest 已是 1.41.2。它的官方描述把 meta-prompting、context engineering、spec-driven development 放在一起，面向 Claude Code。到 2026-05-14，GitHub API 显示约 62,006 stars、5,258 forks。

GSD 最像 AI 项目运行系统。它不只生成 spec，而是管理 state、roadmap、phase、workstream、planner、executor、verification、ship preflight、safe resume。它的 release notes 里反复修复 human-needed verification、probe script、TBD/FIXME markers、worktree 隔离、跨 Codex/Gemini/Antigravity 转换等问题。这些不是文档问题，是 agent 长任务运行问题。

Superpowers 的仓库创建于 2025-10-09，到 2026-05-14 约 189,693 stars、16,882 forks，最新 release v5.1.0。它不是单纯 SDD 框架，而是 agentic skills framework 与软件工程方法论。它把 brainstorming、TDD、debugging、writing plans、executing plans、code review、verification before completion、worktrees、subagent-driven development 做成 skills。

Superpowers 的价值在于把成熟工程师习惯变成 agent 的强制行为。很多 AI 编程失败不是因为没有 spec，而是因为 agent 没有先澄清、没复现、没写测试、没验证、没审查。Superpowers 站在行为层，而不是规格层。

gstack 创建于 2026-03-11，到 2026-05-14 约 95,810 stars、14,249 forks。它来自 Garry Tan 的 Claude Code setup，包含 23 个 opinionated tools，角色包括 CEO、Designer、Eng Manager、Release Manager、Doc Engineer、QA。它不是标准 SDD，但它把规格、责任、评审和发布拆给角色化 agent。对创始人和 maker 来说，这比抽象流程更容易上手。

这一阶段的核心变化是：SDD 不再只是 spec 文件，而开始和 agent 组织结构、执行纪律、验证流程绑定。

### 7. 2026：商业平台和新一代长尾工具涌入

到 2026 年，SDD 开始从开源方法论进入商业平台。

Auto 把自己定位为 Spec-Driven Development Platform，强调“Don’t just prompt. Specify.” 它用 narrative spec、visual narratives、document view、agent connector、local build 等方式把产品意图变成可构建模型。它更像 model-based / narrative-driven development 的商业化路线，强调 spec 可视化、结构校验和测试生成。

Augment 的 Intent、pre.dev、Traycer、BetterSpec、Planu、Spec Native、ADD 等都在相近空间里发力。pre.dev 生产更详细的 transferable specs，并通过 API/MCP 接入 Cursor、Claude Code 等工具；Traycer 强调扫描代码库、验证 AI 生成变更并纠偏；BetterSpec 用 `proposal.md`、requirements、scenarios、design、tasks、archive 和 knowledge base 构造 spec engine；Planu 做本地 MCP spec memory；Spec Native 强调 living specifications 和 Intent Integrity Chain；ADD 把 TDD/BDD 之后的 agent-driven development 做成团队方法论。

这批项目还不一定都足够成熟，但方向很清楚：SDD 正在从“Markdown workflow”走向“spec engine + agent orchestration + validation + drift detection”。

## 四、横向分析：当下市场的五层结构

### 第一层：原生 SDD IDE / 平台

代表：Kiro、Auto、Traycer、pre.dev、Augment Intent。

这一层的特点是产品体验完整。它们不只是给你一个仓库模板，而是把 spec 的创建、编辑、执行、监控、验证做进产品界面。

Kiro 是这一层最有标志性的产品。它有 IDE、spec mode、steering、hooks、MCP 支持，适合希望把 SDD 变成日常 IDE 工作流的团队。它的竞争力来自原生体验和 AWS 背景，风险是平台绑定和 markdown spec 的漂移问题。

Auto 更偏应用建模，把 narrative spec、visual canvas、tests、frontend/backend 生成连起来，适合业务应用和事件建模场景。它的强项是把 spec 做得可视、可审，弱项是通用工程复杂度仍需验证。

pre.dev 不是传统开源 SDD 框架，而是 spec engine + build agents + agency network 的商业化形态。它的价值在于把规格做成可交付资产，尤其适合非工程用户、产品团队、外包协作；但它和自托管工程流程之间有边界。

Traycer 强调扫描代码库、验证 AI 生成变更并纠偏，重点更靠近「spec + verification」而不是「spec + generation」。如果它能把代码事实和 spec 对齐做深，会切中 SDD 最大痛点：规格漂移。

Augment Intent 偏企业多仓库和 living specs，它关心大代码库的上下文、检索和意图维护。它不是开源社区最热的话题，但在企业 AI coding 里位置重要。

这一层的共同风险是黑盒化。产品越完整，用户越舒服，但 spec 是否真的可迁移、可审计、可在离开平台后继续使用，就变成关键问题。

### 第二层：开源 SDD 工具链

代表：GitHub Spec Kit、OpenSpec、GSD、Spec Workflow MCP、BetterSpec、Agent OS。

这是开发者最容易采用的一层。

Spec Kit 的优势是标准入口。它适合把团队从“聊天式开发”拉到“spec/plan/tasks/implement”的结构化流程。它背靠 GitHub，兼容多个 agent，生态潜力最大。短板是仍偏模板和流程，验证能力不够硬。

OpenSpec 的优势是变更制品。它适合 brownfield、长期维护、需要 delta spec 和 archive 的团队。它不像 Spec Kit 那么大众，但工程味更浓。短板是它需要用户理解 spec lifecycle，否则会显得比直接 plan mode 多一层仪式。

GSD 是最重的开源系统之一。它适合多阶段、长时间、重 agent 的项目。它的 state/phase/verification/safe-resume 能解决真实问题，但它会改变团队工作方式。小功能用它可能累，大项目不用它又容易乱。

Spec Workflow MCP 把 Kiro-style workflow 做成 MCP server，优势是可以跨 Claude Code 之外的工具，并提供 dashboard。它是「工作流中台」路线。风险是许可证 GPL-3.0 和生态成熟度需要团队评估。

BetterSpec 是值得观察的新 entrant。它明确支持 OpenCode、Claude Code、Gemini CLI、Cursor、Codex，强调 propose、verify、archive、knowledge base、drift score、MCP server。它看起来吸收了 Spec Kit、OpenSpec、Agent OS 的许多经验，但市场成熟度还需要时间验证。

Agent OS 则更轻。它用 standards + specs 帮 agent 在 codebase 中工作，不想接管全生命周期。对已有流程的团队，这可能比 GSD 更容易落地。

### 第三层：多代理 SDLC 与组织角色框架

代表：BMAD-METHOD、Superpowers、gstack、MetaGPT、ADD。

这一层不是只管 spec，而是管「谁来做事」。

BMAD 把 AI-driven agile 做成框架，适合需要产品、架构、QA、Scrum 等角色协同的项目。它比 Spec Kit 更组织化，比 gstack 更系统化。它的问题是流程重，需要团队愿意进入它的世界。

Superpowers 把工程纪律做成 skills。它不一定生成最漂亮的 spec，但能防止 agent 跳过关键步骤。它尤其适合和其他工具叠加：Spec Kit 管 spec，Superpowers 管 agent 行为。

gstack 用强风格角色栈打动用户。它适合创始人、小团队、maker 快速复制一个“AI 小公司”。但它更像个人方法论产品化，不一定适合所有组织。

MetaGPT 是前史和相邻主流。它的 stars 很高，角色化多代理影响很大，但它不是 2025-2026 这波 repo-native SDD 的典型工具。它更像“需求到软件项目”的多代理框架，为后来的 agent organization 提供范式。

ADD 是新兴方法论，把 TDD、BDD 之后的 Agent Driven Development 做成 coordinated agent teams，强调 test-first 和 independent verification。它很符合 SDD 未来方向，但目前还属于观察名单。

### 第四层：轻量 spec 协议与格式

代表：IntentSpec、SpecDD、Spec Native、Tigs/WellDefined、OpenAPI/AsyncAPI/Pact 等传统契约体系。

这一层的核心不是完整工具，而是让 spec 变成可复用格式。

IntentSpec 最典型。一个 `intent.md` 加 YAML frontmatter，就能定义目标、结果、约束、边界情况、健康指标，并在 CI 校验 schema。它的好处是极轻、可移植、不锁工具。坏处是它不会替你拆任务，也不会证明代码真的匹配。

Spec Native 把规格上升到 living specification 和 Intent Integrity Chain，强调 spec、feature、steps、code 的可追踪与防篡改。它代表的是更硬核、更验证导向的未来方向。

传统契约工具也应放进 SDD 版图。OpenAPI、AsyncAPI、Protocol Buffers、GraphQL schema、Pact、Cucumber/Gherkin，本来就是可执行或半可执行 spec。AI SDD 最应该向它们学习的，不是格式，而是验证精神：spec 不应只是建议，而应能失败。

### 第五层：相邻上下文、文档与规则工具

代表：Cursor Rules、Google Code Wiki、DeepWiki、CLAUDE.md/AGENTS.md 生态、Red Hat 的模块化 what-spec/how-spec 思路。

这些不是严格 SDD 框架，但会影响 SDD 成败。

Cursor Rules 通过 always、auto-attached、agent-requested、manual 等触发模式，把项目规范注入 Cursor。它不产生 feature spec，却能持续约束 agent 行为。

Google Code Wiki 和 DeepWiki 通过 AI 生成代码库文档、架构图、类图、序列图和问答代理，解决的是“理解现有系统”的问题。对 brownfield SDD 来说，spec 不能凭空写，必须先知道系统现在是什么样。文档生成工具如果能和 spec 生成结合，会变得很重要。

CLAUDE.md、AGENTS.md、`.cursor/rules`、Copilot instructions 这类文件，是当前最朴素也最广泛的 SDD 基础设施。它们没有品牌，但每个成熟 AI coding workflow 都离不开。

## 五、代表框架对比表

| 项目 | 层级 | 开源/产品 | 核心制品 | 最适合场景 | 最大短板 |
|---|---|---|---|---|---|
| Kiro | 原生 SDD IDE | 产品 | requirements/design/tasks、steering、hooks | AWS/IDE 内 spec-first 团队 | 平台绑定，spec 可执行性有限 |
| GitHub Spec Kit | 开源 SDD 工具链 | 开源 MIT | spec/plan/tasks | 标准化 SDD 入门，多 agent 兼容 | 验证和漂移检测不够硬 |
| OpenSpec | 开源 SDD 工具链 | 开源 MIT | proposal/spec delta/apply/archive | Brownfield、可追踪变更 | 需要理解生命周期 |
| GSD | 项目运行系统 | 开源 MIT | state/phase/verification/workstream | 长任务、多阶段 agent 项目 | 重，侵入性强 |
| Superpowers | Agent 行为纪律 | 开源 MIT | skills/workflows | 约束 agent 工程习惯 | 缺少中心 spec artifact |
| BMAD-METHOD | 多代理 SDLC | 开源 | agent-as-code、多阶段流程 | AI 敏捷团队、全生命周期 | 流程重，学习成本高 |
| gstack | 角色化工作台 | 开源 MIT | 23 个角色/工具 | 创始人、小团队、maker | 个人风格强 |
| Task Master | PRD-to-tasks | 开源 | PRD、tasks.json、依赖、复杂度 | 从需求拆任务，Cursor/Windsurf 等 | 不是完整 spec 生命周期 |
| Agent OS | Standards + specs | 开源 MIT | standards、specs、instructions | 轻量团队规范注入 | 深度治理有限 |
| Spec Workflow MCP | MCP 工作流 | 开源 GPL-3.0 | requirements/design/tasks/dashboard | 跨工具 spec workflow | 生态仍小，GPL 需评估 |
| IntentSpec | 轻量标准 | 开放标准 | intent.md + schema | 极轻、跨工具、CI 格式校验 | 不验证实现正确性 |
| Auto | 商业平台 | 产品 | narrative spec、visual model、tests | 业务应用/事件建模 | 平台成熟度与迁移性待观察 |
| pre.dev | 商业平台 | 产品/API/MCP | transferable specs、subtasks | 产品到交付、外包协作 | 工程自托管边界 |
| Traycer | 商业平台 | 产品 | spec + codebase verification | AI 变更验证与纠偏 | 公开技术细节有限 |
| BetterSpec | 新兴工具链 | 产品/CLI | proposal/specs/design/tasks/archive | 多 agent 工具适配 | 市场验证尚早 |
| MetaGPT | 多代理框架 | 开源 MIT | role outputs、software project artifacts | 多代理软件生成前史 | 不是 repo-native SDD |

## 六、横纵交汇洞察

### 1. 市场不是一条赛道，而是一套堆栈

之前只看少数项目，会误判为“Spec Kit vs OpenSpec vs GSD”。全景看下来，真正的结构更像堆栈：

底层是项目事实和上下文：代码、测试、文档、规则、历史决策。Cursor Rules、AGENTS.md、Code Wiki、DeepWiki 在这里。

第二层是规格格式：intent.md、requirements.md、design.md、tasks.md、OpenAPI、Pact、Gherkin。IntentSpec、OpenSpec、Spec Kit 在这里。

第三层是任务和状态：PRD-to-tasks、phase、roadmap、workstream、safe resume。Task Master、GSD、Spec Workflow MCP 在这里。

第四层是 agent 行为：TDD、debugging、review、verification、subagent dispatch。Superpowers、ADD 在这里。

第五层是组织角色和产品体验：Kiro、BMAD、gstack、Auto、pre.dev、Traycer、Augment Intent 在这里。

未来成熟团队很少会只用一个东西。更可能是组合：Kiro 或 Spec Kit 作为入口，OpenSpec 管变更，Task Master/GSD 管执行，Superpowers 管行为纪律，AGENTS.md/Cursor Rules 管项目常识，传统测试和契约工具负责验证。

### 2. 最大分水岭：spec 是文档，还是可失败的契约

当前 SDD 工具最大的共同弱点，是大量 spec 仍然是 markdown prose。它们可读，但不一定可验证。

Spec-driven.com 的工具景观把这个问题称为 executability gap：Kiro、Spec Kit、OpenSpec、BMAD、IntentSpec 多数仍使用 markdown，自身不能独立证明实现正确。这个判断很刺耳，但基本成立。

所以真正的未来不在“谁生成更多文档”，而在“谁能让 spec 失败”。也就是：实现偏离 spec 时，CI 会红；agent 修改代码时，spec 会更新或阻止；需求变更时，影响范围能被计算；测试不能被 agent 为了通过而随意改写。

这也是为什么 Pact、OpenAPI、BDD、TDD 这些老工具仍然重要。它们提醒新 SDD：规格的荣耀不是写得完整，而是能被执行。

### 3. Brownfield 是主战场

Greenfield SDD 容易展示，brownfield SDD 才决定商业价值。

在一个新项目里，agent 可以从空目录生成 requirements、design、tasks，再写代码，看起来很顺。但真实公司大多是老系统：已有架构、隐性依赖、历史债务、跨团队接口、破碎文档、不可轻易改的数据库和权限模型。

这时，好的 SDD 工具必须先理解现状，再写变更。OpenSpec 的 delta spec、Traycer 的 codebase verification、Augment 的 multi-repo context、Google Code Wiki/DeepWiki 的代码库理解、GSD 的 safe-resume 和验证状态，都在朝这个方向移动。

我的判断是，2026 下半年 SDD 的竞争重点会从“快速生成 plan”转向“在复杂旧系统中不犯低级错”。

### 4. 角色化会继续流行，但需要验证机制托底

BMAD、gstack、MetaGPT、ADD 说明大家很喜欢把 agent 组织成团队。这是合理的，因为软件开发本来就是多角色协作。

问题是，角色容易变成表演。如果 CEO agent、Designer agent、QA agent 只是输出更漂亮的段落，而没有独立证据、独立测试、独立审查权限，那它们只是 prompt 皮肤。

角色化框架要成熟，必须引入互相制衡：写测试的 agent 不应改实现；review agent 不应只复述计划；QA agent 要能运行真实探针；发布 agent 要能拒绝未验证任务。Superpowers 和 GSD 在这点上比很多角色框架更清醒。

### 5. 选型不该问“哪个最好”，该问“你缺哪一层”

如果缺入口和共同语言，选 Spec Kit。

如果缺 brownfield 变更管理，选 OpenSpec。

如果缺长任务状态、恢复和验证闭环，选 GSD。

如果缺 agent 工程纪律，选 Superpowers。

如果缺完整 AI 敏捷组织，选 BMAD。

如果缺创始人式角色工作台，参考 gstack。

如果缺 PRD 到任务拆解，选 Task Master。

如果缺 IDE 原生 spec 流程，试 Kiro。

如果缺跨工具轻量规范，试 IntentSpec 或 Agent OS。

如果缺产品化 spec engine 或业务建模体验，看 Auto、pre.dev、Traycer、Augment Intent。

## 七、未来三个剧本

**最可能的剧本：分层组合，而不是赢家通吃。** GitHub Spec Kit 成为默认入门模板，Kiro 占据 IDE 原生路线，OpenSpec/GSD/Superpowers 在重度 agent 用户中形成方法论底座，BMAD/gstack 服务角色化团队，IntentSpec/Agent OS 成为轻量规范补充。商业平台围绕 drift detection、multi-repo context、visual spec、enterprise governance 做差异化。

**最危险的剧本：SDD 变成 markdown waterfall。** 大量工具生成越来越厚的 requirements、design、tasks，但没有可执行验证，团队觉得只是把 vibe coding 换成文档负担。AI 很会写计划，也很会把错误写得像真的。这个剧本下，SDD 热潮会退成一堆没人维护的 spec 文件。

**最乐观的剧本：spec 成为 AI 工程的源代码之一。** 每个变更都有机器可读意图、可执行契约、任务状态、验证证据和归档历史。agent 可以接力，人类可以审查，CI 可以拒绝偏离。那时 SDD 不再是一个工具类别，而是软件交付的默认层。

## 八、最终判断

如果只选一个起点，我会推荐团队从 GitHub Spec Kit 或 Kiro 开始，因为它们最容易建立共同语言。

如果是严肃工程团队，我会尽快补 OpenSpec 或类似 delta spec 机制，因为真正难的是 brownfield。

如果已经重度使用 Claude Code/Codex/Gemini CLI，我会把 Superpowers/GSD/Task Master 作为执行层组合，而不是让 agent 只靠聊天上下文工作。

如果是创业团队，我会研究 BMAD 和 gstack，因为它们把“谁负责什么”说得更像真实组织。

如果是平台团队或大企业，要重点关注 Augment Intent、Traycer、pre.dev、Auto、IntentSpec 这类可迁移、可审计、可治理的 spec engine，而不是只看开源 star 数。

最重要的一句：SDD 的价值不在于写更多规格，而在于让规格成为 agent 不能随便绕过的约束。

## 九、信息来源

访问时间统一为 2026-05-14（Asia/Shanghai）。关键数据优先采用 GitHub API、npm registry、官方文档和官方博客。

| 来源 | 用途 |
|---|---|
| https://kiro.dev/docs/ | Kiro 官方 docs：specs、steering、hooks |
| https://aws.amazon.com/documentation-overview/kiro/ | AWS Kiro 文档概览 |
| https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/ | GitHub Spec Kit 官方博客 |
| https://api.github.com/repos/github/spec-kit | Spec Kit GitHub 数据 |
| https://api.github.com/repos/Fission-AI/OpenSpec | OpenSpec GitHub 数据 |
| https://openspec.dev/ | OpenSpec 官方站 |
| npm registry: `@fission-ai/openspec` | OpenSpec npm 时间线 |
| https://api.github.com/repos/gsd-build/get-shit-done | GSD GitHub 数据 |
| npm registry: `get-shit-done-cc` | GSD npm 时间线 |
| https://api.github.com/repos/obra/superpowers | Superpowers GitHub 数据 |
| https://api.github.com/repos/garrytan/gstack | gstack GitHub 数据 |
| https://api.github.com/repos/bmad-code-org/BMAD-METHOD | BMAD-METHOD GitHub 数据 |
| https://api.github.com/repos/eyaltoledano/claude-task-master | Task Master GitHub 数据 |
| npm registry: `task-master-ai` | Task Master npm 时间线 |
| https://api.github.com/repos/buildermethods/agent-os | Agent OS GitHub 数据 |
| https://api.github.com/repos/Pimzino/spec-workflow-mcp | Spec Workflow MCP GitHub 数据 |
| npm registry: `@pimzino/spec-workflow-mcp` | Spec Workflow MCP npm 时间线 |
| https://api.github.com/repos/FoundationAgents/MetaGPT | MetaGPT GitHub 数据 |
| https://intentspec.org/ | IntentSpec 官方说明 |
| https://on.auto/ | Auto 官方说明 |
| https://docs.pre.dev/ | pre.dev 官方文档 |
| https://traycer.ai/ 与 https://docs.traycer.ai/index | Traycer 官方说明 |
| https://www.betterspec.dev/ | BetterSpec 官方说明 |
| https://planu.dev/ | Planu 官方说明 |
| https://www.specnative.dev/ | Spec Native 官方说明 |
| https://getadd.dev/ | ADD 官方说明 |
| https://specdriven.com/landscape/ | Spec-driven 工具景观 |
| https://www.thoughtworks.com/en-gb/radar/techniques/spec-driven-development | Thoughtworks Technology Radar |
| https://arxiv.org/abs/2602.00180 | SDD 学术背景 |
| https://arxiv.org/abs/2604.05278 | Spec Kit Agents 研究 |

## 十、方法论说明

本报告采用横纵分析法：纵向追踪 SDD 从传统规格、TDD/BDD、上下文工程、多代理框架到 2025-2026 年工具爆发的演进；横向按市场层级比较主流框架和相邻工具；最后在两条轴交汇处判断未来格局和选型路径。

