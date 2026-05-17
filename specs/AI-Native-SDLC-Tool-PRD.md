# PRD: AI-Native SDLC（规范驱动开发引擎）

> 版本：v0.3（全集版）
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
4. **垂直切片驱动执行**：每个切片贯穿所有集成层（schema → API → UI → tests），可独立验证；禁止水平切片（先写完所有 API 再写所有 UI）的反模式
5. **Spec 必须可失败**：规格不应只是建议，而应能成为 AI 不能随便绕过的约束；实现偏离 Spec 时系统能够感知和拦截（来自 SDD 全景分析的最重要洞察）
6. **宽进严出**：接受任何形式的输入（完整 PRD / Issue / 口头描述），但输出必须通过门控（自审核 + Developer 确认）才能流入下一阶段

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

构建 AI-Native SDLC 规范驱动开发引擎。核心能力抽象为 TypeScript 模块集（CLI 库形态，提供 IDE 插件级接入）。

**运作机制：**
- 将开发生命周期解耦为多个独立可调用的阶段，每个阶段通过加载对应的 `SKILL.md` 注入 AI 角色人格（Persona）和执行边界（依据 ADR 0001，Persona 内嵌于 SKILL.md，不单独管理）
- 引入实时"统一语言"拦截机制（`CONTEXT.md`），阻止术语混淆向下游传播
- 双层阶段门控：AI 自审核（6 项检查清单）+ Developer 人工确认
- 宽进严出的输入路由：基于输入质量动态决定交互模式（Coaching / Mixed / Batch / Fast）

---

## 六、User Stories

### Requirement Review 阶段

1. As a Developer, I want to 提交一句话的模糊需求，so that 系统能评估输入质量并进入 Coaching 模式逐步引导我补充缺失细节，而非盲目生成代码
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

**技术栈：** TypeScript + Node.js，底层封装为纯 TypeScript 模块集，暴露 API 供 CLI 和后续 IDE Plugin 消费，确保核心工作流和 LLM 交互逻辑的跨平台复用。

**核心模块（MVP 6 个）：**

1. **工作流状态机引擎（Workflow State Engine）**：管理任务状态在 `.agents/active` 目录下的生命周期，维护 `status.yaml`，执行状态流转安全校验，以及 active/archive 的归档流转
2. **Skill 加载与解析器（Skill Loader）**：依据 ADR 0001，不单独维护角色层级，直接读取并解析 `SKILL.md`，装载角色人格（Persona）、I/O 契约及门控逻辑
3. **Context 拦截器（Context Interceptor）**：实时读取 `CONTEXT.md`，在 LLM 生成阶段或输出后处理阶段进行术语规范拦截，并提供交互式 UI 将新术语写入 `CONTEXT.md`（AI 写入，Developer 确认）
4. **输入分析与路由控制器（Input & Routing Controller）**：多维度评估原始需求输入质量，动态决定触发哪种问答模式（Coaching / Mixed / Batch / Fast），并在前置依赖不满足时拦截并提示
5. **垂直切片执行追踪器（Slice Tracker）**：读取 Plan Spec 中的切片列表，生成交互式 Checkbox，追踪当前执行的切片，将相关切片的上下文打包传给 LLM
6. **Spec 模板引擎（Template Engine）**：管理三个阶段的 Spec 模板（Requirement Spec / Plan Spec / Review Report），负责模板渲染、Placeholder 扫描和版本管理

---

## 八、功能路线图

> 依据 `archive/功能积压与借鉴来源.md` 整理，来源已标注

### Phase 2：高优先级功能

| # | 功能 | 核心价值 | 借鉴来源 |
|---|---|---|---|
| P2-1 | **Anti-Rationalization 机制** | 在每个阶段关键决策点预设 AI 常见偷懒借口 + 反驳表，防止 agent 跳过关键步骤 | agent-skills (Addy Osmani) |
| P2-2 | **Doubt-Driven Development 协议** | Review 阶段引入对抗性审查：不是"检查对不对"而是"假设它是错的，找出哪里错"，CLAIM → EXTRACT → DOUBT → RECONCILE → STOP 五步闭环 | agent-skills |
| P2-3 | **Source-Driven Development** | Execute 阶段编码前必须从官方文档获取并引用来源；来源层级（官方文档 > 官方博客 > Web 标准）；不确定的标记 UNVERIFIED | agent-skills |
| P2-4 | **声明式工作流定义（YAML）** | 将工作流从 Markdown 改为 YAML 声明式定义，支持结构化步骤、门控、角色映射、on_reject 行为 | SpecKit + OpenSpec |
| P2-5 | **Constitution 机制（项目宪法）** | 项目级不可违反约束文件（Always/Never/Ask First），所有 Skill 产出必须通过宪法检查 | SpecKit |
| P2-6 | **编排模式分类文档** | 明确支持和禁止的编排模式（禁止 Persona 调用 Persona，编排权属于用户或工作流定义） | agent-skills |

### Phase 3：中优先级功能

| # | 功能 | 核心价值 | 借鉴来源 |
|---|---|---|---|
| P3-1 | **三层配置合并** | base（框架默认）→ team（项目级）→ user（个人级）三层配置合并，支持标量覆盖和表深度合并 | BMAD |
| P3-2 | **角色 persistent_facts** | 每个阶段可记住持久事实（技术栈选型、项目约束、用户偏好），跨会话引用 | BMAD |
| P3-3 | **Incremental Implementation** | Execute 阶段强制增量实现：每切片约 100 行，三种切片策略（垂直切片/合约优先/风险优先），每步实现→测试→验证→提交 | agent-skills |
| P3-4 | **Context Engineering** | 明确定义每阶段上下文加载层级：规则文件 > Spec/架构文档 > 相关源码 > 错误输出 > 对话历史；信任分级（可信/需验证/不可信） | agent-skills |
| P3-5 | **五轴代码评审** | Review 阶段覆盖 5 个维度：正确性、可读性、架构、安全、性能，每个发现标记严重级别（Critical/Important/Minor） | agent-skills |
| P3-6 | **多维度评审视角** | Full 流程 Review 阶段可选启用：产品视角（CEO review）、设计视角、工程视角、开发者体验视角 | gstack |
| P3-7 | **Idea Refine 结构化发想** | Requirement Review 前置的发散→收敛→聚焦三阶段流程，7 种发想透镜（逆向、约束移除、受众转换等） | agent-skills |
| P3-8 | **自适应检查点** | 检查点数量根据任务复杂度动态调整：简单任务 0 个，中等 1 个，复杂全量，取代固定门控 | specs.md |

### Phase 4+：长期规划

| # | 功能 | 核心价值 | 借鉴来源 |
|---|---|---|---|
| P4-1 | **Action-based 非线性工作流** | 随时 update spec、随时回退到 Plan，Action 随时可用而不是按顺序走阶段 | OpenSpec |
| P4-2 | **Wave-based 并行执行** | 按依赖图分组，无依赖的切片并行执行 | GSD |
| P4-3 | **Interface-First 任务排序** | Execute 阶段先定义接口合约再实现，避免切片间接口模糊 | GSD |
| P4-4 | **SDD-Cache（文档缓存）** | 跨会话缓存已获取的官方文档，用 HTTP ETag 做时效性验证 | agent-skills |
| P4-5 | **安全审计能力** | Review 阶段增加 OWASP Top 10 + STRIDE 威胁建模 | gstack + agent-skills |
| P4-6 | **Learnings 系统** | JSONL 格式存储跨会话学习经验，角色执行时参考历史经验 | gstack + GSD |
| P4-7 | **跨模型交叉验证** | 用不同架构的 AI 模型做第二意见审查，覆盖单一模型的盲点 | agent-skills |
| P4-8 | **Change Sizing 约束** | 每次变更目标约 100 行，超 300 行需拆分，超 1000 行禁止，附带拆分策略 | agent-skills |

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
- 极差输入路由：提交"帮我加个导出功能"，验证是否进入 Coaching 模式
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
| Phase 2 功能（6 项） | v1.1 | Anti-Rationalization、Doubt-Driven、Source-Driven 等 |
| 模型路由（按阶段动态选模型） | v1.1 | 参见 `specs/framework/AI-Native-SDLC-v0.4.md` §6.8 |
| Phase 3 功能（8 项） | v1.2 | 三层配置、persistent_facts、五轴评审等 |
| 多 Agent 专家角色体系 | v1.x | MVP 中角色能力内嵌于 Skill 主线（依据 ADR 0001） |
| Review 阶段自动修复闭环 | v1.x | MVP 只抛修复建议，由 Developer 决定采纳 |
| 战略/投注/立项阶段 | v2.0 | 完整产研链路前端覆盖 |
| 运维阶段 | v2.0 | 线上问题响应、需求回溯 |
| Phase 4+ 功能（8 项） | v2.0+ | Action-based 工作流、Learnings 系统、跨模型验证等 |

---

## 十一、Further Notes

- **Context 预算监控**：由于这是长链路强依赖上下文的系统，状态机引擎需重点监控 Token 消耗。若单个阶段上下文逼近 50% 红线，应在 `status.yaml` 与交互界面透出警告，防止 AI 降智导致架构漂移
- **进阶问题积压**：高阶 Requirement Review 功能（洞察层 3 问、挑战层 6 问、跨模型第二意见等）已在 `archive/2026-05-17-specs-stages/requirement-review/功能积压-进阶问题.md` 中详细记录，按 v1.1/v1.2/v2.0 路线逐步启用
- **SDD 全景定位**：依据 `specs/framework/SDD框架全景横纵分析报告.md`，我们在五层市场堆栈中定位于第三层（多代理 SDLC 方法论）+ 第二层（开源 SDD 工具链）的结合，以"企业场景 + 人主控 + 可执行 Spec 约束"为核心差异化
