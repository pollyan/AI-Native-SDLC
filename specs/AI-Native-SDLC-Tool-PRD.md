# PRD: AI-Native SDLC（规范驱动开发引擎）

> 版本：v0.4（全集版 + 竞品代码分析增补）
> 日期：2026-05-17
> 状态：待 to-issues 拆分

---

## 一、产品定位

### 我们是谁

一家 B2B AI Coding 工具公司，产品线涵盖开发 Agent、测试 Agent、需求 Agent、运维 Agent 等。本框架是为 **Developer** 设计的差异化方法论内核，是整个产品线的执行纪律引擎。

### 三个时间跨度的定位

| 时间跨度 | 定位 | 交付形态 |
|---|---|---|
| **短期** | 方法论资产，由 FDE 团队在客户现场实施赋能时使用 | SKILL.md 规范包 + 最佳实践手册 |
| **中期** | 集成到 AI Coding 工具中，成为内置的差异化功能 | CLI 库 + IDE 插件 |
| **长期** | 扩展为完整产研全流程引擎，覆盖战略到运维 | 完整平台能力 |

### 用户定义

- **Developer**：客户企业的一线开发人员，框架的主要使用者，所有阶段的决策者和操作主体
- **FDE**：现场开发工程师，短期内作为框架实施的替身用户，负责客户赋能培训

### 差异化定位

| 对比对象 | 他们做什么 | 我们的差异 |
|---|---|---|
| Devin / 黑盒 AI Agent | AI 自动干、人旁观 | **人主控，AI 辅助**；所有关键决策由 Developer 确认 |
| 传统 Copilot / 代码补全 | 微观代码建议，无全局视角 | **阶段化全流程管控**；Spec 作为跨阶段的上下文传递载体 |
| BMAD / gstack / Superpowers | 通用 AI 开发框架 | **企业场景优先**；垂直切片执行 + 宽进严出 + 术语拦截，适配真实团队 |

---

## 二、核心设计原则

1. **人主控，AI 辅助**：Developer 控制所有阶段切换，AI 可建议但人最终确认；所有 AI 产出（方案推荐、修复建议等）由 Developer 决定是否采纳
2. **Spec 是阶段间唯一传递载体**：每个阶段的产出都是一份 Spec 文件，既是下一阶段的输入，也是本阶段的质量约束；阶段间不通过口头描述传递信息
3. **统一语言作为基础设施**：`CONTEXT.md` 词汇表被所有阶段的 AI 行为依赖；术语冲突时实时拦截，发现新术语时立即写入，不批量处理
4. **垂直切片驱动执行**：每个切片贯穿所有集成层（schema → API → UI → tests），可独立验证；禁止水平切片（先写完所有 API 再写所有 UI）的反模式。在 AI 辅助时代完整实现的边际成本趋近于零（gstack 实测：功能实现约 30x 时间压缩），"做完整版本"永远优于"做 90% 的快捷版"
5. **Spec 必须可失败**：规格不应只是建议，而应能成为 AI 不能随便绕过的约束；实现偏离 Spec 时系统能够感知和拦截（来自 SDD 全景分析的最重要洞察）
6. **宽进严出**：接受任何形式的输入（完整 PRD / Issue / 口头描述），但输出必须通过门控（自审核 + Developer 确认）才能流入下一阶段
7. **证据先于声明（Evidence Before Claims）**：任何"完成了""通过了"的声明，必须附有刚刚运行过的命令输出作为证据；AI 不得使用"应该能行""看起来对了"等推测性语言；无验证证据的完成声明视为无效输出（来自 Superpowers verification-before-completion）
8. **目标先于任务（Goal Before Tasks）**：每个阶段和每个切片的执行，必须先明确可量化的目标（Goal），才能生成任务列表；禁止"先列任务再倒推目标"的反模式；无法被验证的目标等于无目标（来自 GSD Goal-Backward 方法论）

---

## 三、全景 SDLC 覆盖路线

### 完整产研流程

```
战略 → 投注/立项 → 需求评审 → 技术设计 → 开发执行 → 测试 → 上线 → 运维
        ↑                                                            ↑
        └──────────────── MVP 覆盖（4 阶段核心）───────────────────┘
                          Requirement Review → Plan → Execute → Review

v1.x 扩展：技术设计（独立化）、测试（独立化）、上线（preflight）
v2.0 扩展：战略、投注/立项、运维
```

### 各阶段定义（含非 MVP 占位）

| 阶段 | 核心价值 | MVP 状态 |
|---|---|---|
| **战略** | 商业战略分析、技术方向选择、竞品定位 | v2.0 |
| **投注/立项** | 项目可行性分析、资源投注决策、ROI 预估 | v2.0 |
| **Requirement Review（需求评审）** | 澄清问题定义、建立验收标准、统一术语 | ✅ MVP |
| **Plan（方案设计）** | 技术方案设计、垂直切片拆分、方案预研 | ✅ MVP |
| **Execute（开发执行）** | 逐切片实现、TDD 推荐实践、进度追踪 | ✅ MVP |
| **Review（评审）** | 三轴独立审查、问题分级、修复建议 | ✅ MVP |
| **技术设计** | 独立架构设计、ADR 记录、接口契约定义 | v1.x |
| **测试** | 独立测试策略、QA 验证、Eval 评测 | v1.x |
| **上线** | 发布前 preflight、feature flag、回滚预案 | v1.x |
| **运维** | 线上问题响应、监控告警、需求回溯 | v2.0 |

---

## 四、Problem Statement

当前 AI 编程辅助工具市场存在两极分化：要么是黑盒自动执行（如 Devin 模式）导致开发者失去代码控制权，要么是传统代码补全工具缺乏对长周期任务的阶段管控。两者都无法解决**跨阶段的上下文一致性**问题：需求术语在设计时走样、设计意图在编码时漂移、编码结果无法回溯到验收标准。企业开发者需要一个能保证"人主控"、通过结构化 Spec 和门控机制约束 AI 行为、且每个阶段可独立调用的规范驱动开发流程引擎。

---

## 五、Solution

构建 AI-Native SDLC 规范驱动开发引擎。**交付形态是一套基于 `SKILL.md` 的规范包**，加载进 AI Coding 插件或 IDE 工具中运行（如 Cursor、Claude Code、Windsurf 等），不是独立的应用程序或命令行工具。

**运作机制：**
- 将开发生命周期解耦为四个独立可调用的阶段（Requirement Review / Plan / Execute / Review），每个阶段对应一个独立的 `SKILL.md` 文件，由 AI Coding 工具加载执行
- **阶段间信息传递唯一载体是 Spec 文件**：Requirement Review 产出 Requirement Spec → Plan 读取并产出 Plan Spec → Execute 读取并逐切片实现 → Review 读取并产出 Review Report；禁止跨阶段的口头描述传递
- 引入实时"统一语言"拦截机制（`CONTEXT.md`），阻止术语混淆向下游传播
- 双层阶段门控：AI 自审核（6 项检查清单）+ Developer 人工确认
- 宽进严出的输入处理：Skill 始终对输入做完整性评估——缺什么问什么，不缺就直接推进；用户无需感知或选择任何"模式"

---

## 六、User Stories

### Requirement Review 阶段

1. As a Developer, I want to 提交一句话的模糊需求，so that Skill 能评估输入完整性并仅针对缺失的关键信息提问补充，而非盲目生成代码
2. As a Developer, I want to 在整个项目中使用统一的 `CONTEXT.md` 词汇表，so that AI 若检测到与"_Avoid_"冲突的术语，能自动标记为 `❌ 矛盾` 并要求我裁决
3. As a Developer, I want to AI 遇到不确定信息时输出显式的 `[NEEDS CLARIFICATION: 影响什么决策]`，so that 我能清晰分辨每个待办点影响的具体决策，避免隐藏假设
4. As a Developer, I want to 在阶段结束前看到自动生成的"门控自审核清单"，so that 我能确认当前 Spec 合规（无 Placeholder、无内部矛盾），并由我最终签署放行

### Plan 阶段

5. As a Developer, I want to 当方案间存在重大 tradeoff 时 AI 自动生成方案预研对比，so that 每个选项都回答"接受了什么代价、放弃了什么、什么条件下需要被推翻"，让技术决策有据可查
6. As a Developer, I want to Plan Skill 将方案强制拆分为端到端的垂直切片，so that 每个切片贯穿 schema + API + UI + tests，可独立验证，Execute 阶段不会出现水平分层的反模式

### Execute 阶段

7. As a Developer, I want to Execute Skill 根据 Plan Spec 中的切片列表逐切片推进，so that 每个切片有独立的完成状态 Checkbox，我可以随时查看整体进度，不会出现一次性生成所有代码的失控局面
8. As a Developer, I want to 当某切片调试遇到问题时 AI 给出 3-5 个可证伪假设并按优先级排列，so that 系统性排查根因，不会锚定第一个猜测

### Review 阶段

9. As a Developer, I want to Review Skill 从三个独立视角（代码质量 / 需求一致性 / 验收标准逐条确认）产出分级问题清单，so that 我清楚哪些必须修复、哪些建议修复，并由我决定是否采纳自动修复建议

### 跨阶段

10. As a Developer, I want to 能够随时独立调用任意阶段的 Skill，so that 在信息不足时由 Skill 自动评估前置依赖并提示我补充或调用上游 Skill，而非静默降质执行

### 非 MVP 阶段（v1.x+）

11. As a Developer, I want to 在技术设计阶段留存 ADR（架构决策记录），so that 关键技术选型的决策背景被结构化归档，后续阶段的 AI 行为能够引用和遵守这些约束
12. As a Developer, I want to 在上线前自动执行 preflight checklist，so that 部署前的必检项（环境配置、回滚预案、监控就绪）不会被遗漏
13. As a Developer, I want to 线上故障时能够从运维日志回溯到产生该功能的 Requirement Spec 和验收标准，so that 排查根因时有完整的意图上下文，而非只看代码

---

## 七、Implementation Decisions

**交付形态：本框架不是独立应用程序，而是一套 Skill 规范包。**

核心交付物是一组结构化的 `SKILL.md` 文件，每个文件对应一个开发阶段的完整执行规范（包含角色人格、I/O 契约、门控逻辑、反模式约束）。这些 Skill 文件被加载进用户已有的 AI Coding 插件或 IDE 工具中（如 Cursor Rules、Claude Code Slash Commands、Windsurf Rules 等），复用这些工具的 LLM 调用能力。

**阶段间连接方式：** 各阶段之间不依赖任何运行时 API 调用，而是通过 **Spec 文件（Markdown 格式）** 作为唯一信息载体顺序传递：

```
Requirement Review Skill → [Requirement Spec.md]
                                      ↓
                           Plan Skill → [Plan Spec.md]
                                              ↓
                                   Execute Skill → 逐切片实现 → [Review Report.md]
                                                                          ↓
                                                              Review Skill
```

**短期（v1.0 MVP）实现策略：** 以纯 Markdown 规范包为主，Skill 文件直接由 FDE 手动安装到客户 IDE 工具的规则目录中，无需编写应用程序代码。框架产物 = Skill 文件 + Spec 模板 + CONTEXT.md 词汇表 + 配套最佳实践手册。

**中期（v1.x）集成策略：** 结合宿主 AI Coding 工具的插件 API（如 VSCode Extension API），将 Skill 加载、Spec 文件管理、状态追踪等能力封装为插件功能，但核心 Skill 逻辑仍以 `SKILL.md` 为单一真相来源。

**MVP 交付物 = 4 个 SKILL.md 文件**（每个文件对应一个阶段）：

| Skill 文件 | 职责 |
|---|---|
| `requirement-review.skill.md` | 收集需求 → 质询本质问题 → 产出 Requirement Spec |
| `plan.skill.md` | 读取 Requirement Spec → 拆分垂直切片 → 产出 Plan Spec |
| `execute.skill.md` | 读取 Plan Spec → 逐切片实现 → 更新切片状态 |
| `review.skill.md` | 读取所有制品 → 三轴审查 → 产出 Review Report |

**每个 SKILL.md 在编写时必须满足以下 7 条内容设计约束**（不是 7 个独立的 Skill，而是写进 4 个 Skill 里的行为要求）：

1. **Skill 自描述**（4 个 Skill 都有）：YAML Frontmatter 声明角色人格（Persona）、输入/输出契约、前置依赖，AI Coding 工具据此加载
2. **统一语言检查**（4 个 Skill 都有）：每次生成前读取 `CONTEXT.md`，发现术语偏差立即拦截，新术语由 AI 提议、Developer 确认写入
3. **Spec 模板引用 + 门控扫描**（4 个 Skill 都有）：每个 Skill 通过 `template_ref` 字段引用外部模板（`templates/` 目录），执行到"起草 Spec"步骤时才按需加载，不在初始化时读取；门控时自动扫描 `TODO`/`TBD`/`??`，有则阻断
4. **前置依赖检查**（4 个 Skill 都有）：加载时检查上游 Spec 文件是否存在（按命名约定 `specs/<phase>-spec-<feature>.md` 查找），缺失则提示 Developer 提供路径或先完成上游阶段
5. **输入完整性评估 + 需求深度质询**（仅 `requirement-review.skill.md`）：① 对输入做完整性检查，缺什么问什么，不重复追问已有信息 ② 输入补全后必须完成 Problem Reframing（重诠释真实问题）和 Scope Decision（四选一范围决策），再起草 Spec
6. **垂直切片追踪**（仅 `execute.skill.md`）：解析 Plan Spec 中的切片列表，生成可交互 Checkbox，每个切片独立推进，当前切片上下文精确传递给 LLM
7. **前置依赖检查**（4 个 Skill 都有）：加载时自动评估上游 Spec 是否存在且合规，缺失则阻断并提示 Developer 先完成上游阶段


---

## 八、功能路线图

> 依据 `archive/功能积压与借鉴来源.md` 整理，来源已标注

### Phase 2：高优先级功能

| # | 功能 | 核心价值 | 借鉴来源 |
|---|---|---|---|
| P2-1 | **Anti-Rationalization 机制** | 在每个阶段关键决策点预设 AI 常见偷懒借口 + 反驳表，防止 agent 跳过关键步骤 | 行业最佳实践 |
| P2-2 | **Doubt-Driven Development 协议** | Review 阶段引入对抗性审查：不是"检查对不对"而是"假设它是错的，找出哪里错"，CLAIM → EXTRACT → DOUBT → RECONCILE → STOP 五步闭环 | 行业最佳实践 |
| P2-3 | **Source-Driven Development** | Execute 阶段编码前必须从官方文档获取并引用来源；来源层级（官方文档 > 官方博客 > Web 标准）；不确定的标记 UNVERIFIED | 行业最佳实践 |
| P2-4 | **声明式工作流定义（YAML）** | 将工作流从 Markdown 改为 YAML 声明式定义，支持结构化步骤、门控、角色映射、on_reject 行为 | SpecKit + OpenSpec |
| P2-5 | **Constitution 机制（项目宪法）** | 项目级不可违反约束文件（Always/Never/Ask First），所有 Skill 产出必须通过宪法检查；Plan 阶段新增 Phase -1 门控层：AI 在进入方案设计前，必须先通过宪法合规检查，不合规须书面说明理由；配套 **Complexity Tracking 表**：违反约束时必须填写（Violation / Why Needed / Simpler Alternative Rejected Because），让每次"违宪"都有迹可查而非默默绕过 | SpecKit |
| P2-6 | **编排模式分类文档** | 明确支持和禁止的编排模式（禁止 Persona 调用 Persona，编排权属于用户或工作流定义） | 行业最佳实践 |
| P2-7 | **系统性调试四阶段协议** | Execute 阶段遭遇 bug 时强制走四阶段：① Root Cause Investigation → ② Pattern Analysis → ③ Hypothesis & Test → ④ Implementation；关键规则：尝试 3 次修复仍未解决必须停止并质疑架构，不得继续猜测 | Superpowers |
| P2-8 | **Execute 前置就绪检查（Pre-Execute Readiness Gate）** | 用户触发 Execute 前，Skill 自动扫描 Plan Spec 完整性：① 所有切片有对应验收标准 ② 无未解决的 `[NEEDS CLARIFICATION]` 标记 ③ 无无测试计划的切片；任一不满足则阻断并给出具体修复提示 | BMAD |
| P2-9 | **需求强度分级标注（RFC 2119）** | Requirement Spec 模板强制引入需求强度关键词：`SHALL/MUST`（绝对要求）/ `SHOULD`（强烈推荐，有理由可不做需说明）/ `MAY`（真正可选）/ `MUST NOT`（绝对禁止）；Review 阶段可精确判断"必须实现的需求被漏了"vs"建议项没做" | OpenSpec |
| P2-10 | **阶段切换 Context Hygiene** | 每个阶段 Spec 归档后，Skill 主动触发上下文清洁动作：① 生成下一阶段精简状态摘要（当前决策 + 未解决问题 + 下一步行动）② 提示 Developer 建议开启新会话；防止长链路对话导致 AI 上下文腐败（Context Rot） | OpenSpec + GSD |
| ~~P2-11~~ | ~~**需求深度质询协议（Problem Reframing + Scope Decision）**~~ | ~~已纳入 MVP 内建能力 #5~~ | GStack |



### Phase 3：中优先级功能

| # | 功能 | 核心价值 | 借鉴来源 |
|---|---|---|---|
| P3-1 | **三层配置合并** | base（框架默认）→ team（项目级）→ user（个人级）三层配置合并，支持标量覆盖和表深度合并 | BMAD |
| P3-1 | **三层配置合并** | base（框架默认）→ team（项目级）→ user（个人级）三层配置合并，支持标量覆盖和表深度合并；默认策略采用"Absent = Enabled"——最佳实践门控默认开启，team/user 配置层可显式关闭，降低认知负荷 | BMAD + GSD |
| P3-2 | **角色 persistent_facts** | 每个阶段可记住持久事实（技术栈选型、项目约束、用户偏好），跨会话引用；支持文件引用（`persistent_facts: [path/to/tech-stack.md]`）和 glob 路径 | BMAD |
| P3-3 | **Incremental Implementation + TDD per-cycle checklist** | Execute 阶段强制增量实现：每切片约 100 行，每步实现→测试→验证→提交；每个 TDD 小循环附强制 checklist：① 测试描述行为而非实现 ② 只使用公共接口 ③ 最小通过实现（无投机功能）④ YAGNI 检查通过 ⑤ 测试能承受重构 | 行业最佳实践 + Matt Pocock |
| P3-4 | **Context Engineering** | 明确定义每阶段上下文加载层级：规则文件 > Spec/架构文档 > 相关源码 > 错误输出 > 对话历史；信任分级（可信/需验证/不可信） | 行业最佳实践 |
| P3-5 | **五轴代码评审（三轴独立 Agent 架构）** | Review 阶段三轴审查（代码质量轴 / 需求一致性轴 / 验收标准轴）必须由三个上下文互不共享的独立 Agent 并行执行——防止第一轴结论污染第二轴判断；每轴覆盖 5 个维度（正确性、可读性、架构、安全、性能），每个发现标记 Critical/Important/Minor 并附 file:line 定位 | 行业最佳实践 + Matt Pocock + Superpowers |
| P3-6 | **多维度评审视角** | Full 流程 Review 阶段可选启用：产品视角（CEO review）、设计视角、工程视角、开发者体验视角 | GStack |
| P3-7 | **Idea Refine 结构化发想** | Requirement Review 前置的发散→收敛→聚焦三阶段流程，7 种发想透镜（逆向、约束移除、受众转换等） | 行业最佳实践 |
| P3-8 | **自适应检查点** | 检查点数量根据任务复杂度动态调整：简单任务 0 个，中等 1 个，复杂全量，取代固定门控 | specs.md |
| P3-9 | **结构化 Learnings 提取** | 每个阶段结束时自动从阶段制品中提取 4 类制度记忆：Decisions（技术决策及理由）/ Lessons（执行中才发现的教训）/ Patterns（可复用的实现模式）/ Surprises（出乎预料的发现）；每条记录附 Source 归因；可 hook 到外部 MCP 知识库，否则降级为本地 LEARNINGS.md | GSD |
| P3-10 | **工作流健康检查（/sdlc:health）** | 提供三级状态诊断（HEALTHY / DEGRADED / BROKEN），含具名错误码体系、自动修复（--repair）和 Context 利用率检测（--context），帮助发现状态不一致、孤立制品、context 超限等问题 | GSD |
| P3-11 | **评审接收协议** | Review 阶段 AI 接收评审意见时，外部评审建议视为"待验证"而非命令：先 grep 代码库确认是否真的被使用（YAGNI 检查）→ 不清楚的条目必须全部澄清后才开始实现 → 实现前独立验证技术正确性；禁止表演性认同回应 | Superpowers |
| P3-12 | **切片执行模式分类（HITL/AFK）** | Plan Spec 的每个切片标注 `execution_mode: HITL | AFK`：HITL（Human-in-the-Loop）需要 Developer 介入确认；AFK（Away From Keyboard）可无监督自动执行并优先排序；Slice Tracker 可基于此过滤"可批量运行的切片"，让 Developer 把精力集中在真正需要决策的环节 | Matt Pocock |
| P3-13 | **会话交接协议（Session Handoff）** | 上下文接近 50% 警戒线时，Skill 主动生成结构化 handoff 摘要（当前切片进度 + 关键决策 + 未解决问题 + 下一步行动），新会话加载 handoff 文件后立刻知道当前状态，无需回放历史；防止 AI 降智期间产生架构漂移 | Matt Pocock + GSD |
| P3-14 | **依赖合法性检查（Dependency Legitimacy Gate）** | Execute 阶段切片执行前，对 Plan 中新引入的依赖包（npm/pip 等）进行基础合法性验证：包名是否存在于官方注册表、版本是否真实存在；不确定的自动标记 `[UNVERIFIED]` 并阻断执行，要求 Developer 确认后才继续；抵御 AI 幻觉引入不存在依赖（Slopsquatting）的风险 | GSD |


### Phase 4+：长期规划

| # | 功能 | 核心价值 | 借鉴来源 |
|---|---|---|---|
| P4-1 | **Action-based 非线性工作流** | 随时 update spec、随时回退到 Plan，Action 随时可用而不是按顺序走阶段 | OpenSpec |
| P4-2 | **Wave-based 并行执行** | 按依赖图分组，无依赖的切片并行执行 | GSD |
| P4-3 | **Interface-First 任务排序** | Execute 阶段先定义接口合约再实现，避免切片间接口模糊 | GSD |
| P4-4 | **SDD-Cache（文档缓存）** | 跨会话缓存已获取的官方文档，用 HTTP ETag 做时效性验证 | 行业最佳实践 |
| P4-5 | **安全审计能力** | Review 阶段增加 OWASP Top 10 + STRIDE 威胁建模 | gstack |
| P4-6 | **跨模型交叉验证** | 用不同架构的 AI 模型做第二意见审查，覆盖单一模型的盲点 | 行业最佳实践 |
| P4-7 | **Change Sizing 约束** | 每次变更目标约 100 行，超 300 行需拆分，超 1000 行禁止，附带拆分策略 | 行业最佳实践 |
| P4-8 | **Spec Delta Archive（需求历史归档）** | Spec 变更不是整体覆写，而是显式写 delta（ADDED / MODIFIED / REMOVED Requirements）；REMOVED 必须附带 Reason + Migration 迁移路径；每次 apply 后将制品归档，支持需求演化的完整回溯 | OpenSpec |

---

## 九、Testing Decisions

本工具的测试将采用三层策略的 Eval-Driven Development（评测驱动开发）。

**好的测试标准：** 对 LLM 输出的测试应只关注外部可见行为（如是否触发了预期的追问分支、是否产出了合规的 Spec 结构），而非文本的修辞措辞。

**第一层：工程确定性测试（Vitest）**——针对确定性代码，要求高覆盖率：
- Context Parser：能否正确解析 `CONTEXT.md` 的 `Avoid` 字段
- Placeholder Scanner：能否 100% 揪出 `TODO`、`??`、`TBD` 等
- 状态机流转：非法的状态跳转是否被正确拦截
- Skill Loader：能否正确解析 `SKILL.md` 的 YAML 头和必填章节

**第二层：Skill 合规测试（Vitest，source-text-is-the-product 模式）**——`SKILL.md` 本身是核心交付物，测试其结构合规性（借鉴 GSD 的测试实践）：
- 每个 `SKILL.md` 的 7 个必填章节是否完整
- YAML Frontmatter 字段是否合规
- Skill 间的引用关系是否过时（`dependencies` 字段指向的 Skill 是否存在）

**第三层：AI 行为评测（Promptfoo，LLM-as-a-Judge）**——约 20 个精心设计的边界场景构成 Golden Dataset，在 `tests/eval_datasets/` 目录维护：
- 模糊输入处理：提交"帮我加个导出功能"，验证 Skill 是否仅针对缺失信息提问而非直接生成代码
- 术语冲突拦截：输入包含 `_Avoid_` 词，验证是否触发 `❌ 矛盾`
- 禁止猜测原则：模糊需求下，验证是否输出 `[NEEDS CLARIFICATION]` 而非编造
- 垂直切片校验：Plan 阶段输出是否包含端到端切片而非水平分层
- 三轴审查完整性：Review 阶段是否产出了三个独立轴的报告

---

## 十、版本规划

| 能力 | 目标版本 | 说明 |
|---|---|---|
| 技术设计阶段（独立化） | v1.0 | 从 Plan 阶段分离，加入 ADR 记录 |
| 测试阶段（独立化） | v1.0 | QA 验证 + Eval 评测作为独立阶段 |
| 上线阶段（preflight） | v1.0 | 发布前检查、回滚预案 |
| Phase 2 功能（10 项） | v1.1 | Anti-Rationalization、Doubt-Driven、Source-Driven、系统性调试协议、Execute 前置就绪检查、RFC 2119 需求强度分级、阶段切换 Context Hygiene 等 |
| 模型路由（按阶段动态选模型） | v1.1 | 参见 `specs/framework/AI-Native-SDLC-v0.4.md` §6.8 |
| Phase 3 功能（14 项） | v1.2 | 三层配置（Absent=Enabled）、persistent_facts、TDD per-cycle checklist、五轴评审（三轴独立 Agent）、Learnings 提取、Health Check、评审接收协议、切片 HITL/AFK 分类、会话交接协议、依赖合法性检查等 |
| 多 Agent 专家角色体系 | v1.x | MVP 中角色能力内嵌于 Skill 主线（依据 ADR 0001） |
| Review 阶段自动修复闭环 | v1.x | MVP 只抛修复建议，由 Developer 决定采纳 |
| 战略/投注/立项阶段 | v2.0 | 完整产研链路前端覆盖 |
| 运维阶段 | v2.0 | 线上问题响应、需求回溯 |
| Phase 4+ 功能（8 项） | v2.0+ | Action-based 工作流、Spec Delta Archive、跨模型验证、Change Sizing 等 |

---

## 十一、Further Notes

- **Context 预算监控**：由于这是长链路强依赖上下文的系统，状态机引擎需重点监控 Token 消耗。若单个阶段上下文逼近 50% 红线，Skill 应主动提示 Developer 开启新会话，防止 AI 降智导致架构漂移
- **进阶问题积压**：高阶 Requirement Review 功能（洞察层 3 问、挑战层 6 问、跨模型第二意见等）已在 `archive/2026-05-17-specs-stages/requirement-review/功能积压-进阶问题.md` 中详细记录，按 v1.1/v1.2/v2.0 路线逐步启用
- **SDD 全景定位**：依据 `specs/framework/SDD框架全景横纵分析报告.md`，我们在五层市场堆栈中定位于第三层（多代理 SDLC 方法论）+ 第二层（开源 SDD 工具链）的结合，以"企业场景 + 人主控 + 可执行 Spec 约束"为核心差异化
