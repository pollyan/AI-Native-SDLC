# AI-Native SDLC

AI 原生软件开发生命周期框架——一套以 Spec 文件为传递载体、以工作流阶段为驱动、面向企业开发团队的规范驱动开发方法论。

## 工作流阶段

**Requirement Review**:
需求评审阶段。对输入的需求文档进行评审，结合业务上下文和代码上下文发现模糊地带、提出精确问题、澄清边界场景，产出包含验收标准的需求 Spec。
_Avoid_: Brainstorm, 头脑风暴, 需求分析

**Plan**:
方案设计阶段。基于需求 Spec 进行技术方案设计和任务拆分，每个任务是一个可独立测试的垂直切片，产出方案 Spec。
_Avoid_: 设计阶段, 架构设计

**Execute**:
执行阶段。逐切片串行执行，推荐 TDD 模式（测试→实现→重构），但不强制。每个切片完成后标记完成。
_Avoid_: 开发阶段, 编码阶段

**Review**:
评审阶段。从代码质量、需求一致性、验收标准三个维度审查，产出问题清单和自动修复建议，由开发者决定是否采纳。
_Avoid_: 测试阶段, QA

## 传递载体

**Spec**:
一个阶段的结构化产出文件，同时作为下一阶段的输入。通过模板约束内容结构，内置最佳实践以保证稳定输出质量。
_Avoid_: 文档, 产出物, 交付物

**Skill**:
AI Agent 在某个工作流阶段的行为定义文件（SKILL.md）。内嵌该阶段的 AI 角色人格（Persona）、Prompt 行为、I/O 契约和门控检查逻辑。每个阶段对应一个独立可用的 Skill，角色人格作为 Skill 的一个组成部分内嵌管理，不单独维护角色维度。
_Avoid_: 插件, 模块, 脚本, Agent

## 用户

**Developer**:
使用本框架的一线开发人员，是企业客户团队的成员。在所有阶段中是决策者和操作主体，AI 是辅助角色。
_Avoid_: 用户, 人, 操作者

**FDE**:
现场开发工程师。短期内作为框架实施的替身用户，负责在客户现场对开发者进行赋能培训。
_Avoid_: 实施顾问, 现场工程师

## 上下文

**Context**:
项目的领域词汇表，定义项目特有的业务术语及其关系。所有阶段的 AI 行为必须遵守 Context 中的术语定义。
_Avoid_: 上下文, 背景, 知识库

**Vertical Slice**:
可独立测试的端到端任务单元，贯穿所有集成层（schema + API + UI + tests），不是单个层的水平切片。
_Avoid_: 垂直切面, 任务切片

## Relationships

- **Requirement Review** 产出 **Spec**，作为 **Plan** 的输入
- **Plan** 产出 **Spec**（含方案 + 垂直切片列表），作为 **Execute** 的输入
- **Execute** 逐个消费 **Vertical Slice**，产出的代码作为 **Review** 的输入
- **Review** 对照 **Requirement Review** 阶段定义的验收标准逐条确认
- **Context** 被所有阶段的 AI 行为依赖

## Flagged ambiguities

- "用户" 同时被用来指 Developer（客户开发者）和终端用户——已解决：Developer 指使用框架的人，终端用户是 Developer 开发的产品的使用者。
- "Brainstorm" 沿用自 Superpowers 框架——已解决：在企业场景中重命名为 Requirement Review，因为输入物不是模糊想法而是已有需求文档。
- "测试" 在不同语境下指 TDD 的单元测试和 Review 的验收确认——已解决：前者是 Execute 阶段的推荐实践，后者是 Review 阶段的审查维度。
