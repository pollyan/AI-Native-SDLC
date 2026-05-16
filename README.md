# AI-Native SDLC

> 规范驱动的 AI 原生软件开发生命周期 — 研究与设计

## 项目简介

本项目是一份**规范驱动开发（Spec-Driven Development）工作流的系统性研究与设计方案**。

核心问题是：当 AI 成为开发流程的核心参与者时，软件开发生命周期（SDLC）应该如何重新设计？

我们研究了 7+ 个主流 AI 编程框架（Superpowers、SpecKit、gstack、GSD、BMAD、OpenSpec 等），吸收各方优点、规避已知缺陷，提出了一套**多角色专家智能体协作框架**。

## 核心理念

1. **多角色分工** — 多个专精 Agent 协作，而不是一个全能 Agent 硬扛
2. **辅助定位** — AI 作为辅助角色参与全流程，人始终是执行主体和决策者
3. **需求流转驱动** — 以需求流转为主线，而不是工具堆砌
4. **对话式工作流** — 人在 IDE 对话框中以连贯上下文驱动 AI 专家协作
5. **规范先行** — 先写 Spec 再写代码，没有例外

## 文档目录

### 研究报告

| 文档 | 说明 |
|------|------|
| [规范驱动开发框架研究报告](docs/规范驱动开发框架研究报告.md) | 7 个主流框架的深度分析，覆盖方法论、工具链、工作流系统的完整光谱 |

### 框架方案（迭代演进）

| 文档 | 说明 |
|------|------|
| [多智能体专家团框架 v0.1](docs/多智能体专家团框架-高阶方案-v0.1.md) | 初版讨论稿，建立基础概念 |
| [多智能体专家团框架 v0.2](docs/多智能体专家团框架-高阶方案-v0.2.md) | 吸收评审意见，补充细节 |
| [多智能体专家团框架 v0.3](docs/多智能体专家团框架-高阶方案-v0.3.md) | 双层架构、活跃/归档分离、轻量/重量流程、上下文预算管理 |

### 功能规划

| 文档 | 说明 |
|------|------|
| [功能积压与借鉴来源](docs/功能积压与借鉴来源.md) | 各框架亮点功能清单，标注来源和优先级 |

## 关键设计决策

- **4 角色最小集**（精简自 BMAD 的 8 角色）：Analyst → Architect → Engineer → Reviewer
- **工作流 + 角色能力双层架构**：工作流定义阶段流转，角色能力定义每个阶段的行为
- **轻量/标准/完整三层流程**：Quick（5 分钟内）、Standard（常规功能）、Full（跨日大需求）
- **上下文预算管理**：借鉴 GSD 的 context quality curve，避免长任务中的 Agent "失忆"
- **门控机制**：检查点 + 自审核循环，确保阶段产出质量

## 研究覆盖的框架

| 框架 | Stars | 核心特色 |
|------|-------|---------|
| [Superpowers](https://github.com/obra/superpowers) | 183K | 技能驱动 + HARD-GATE + 自审核循环 |
| [SpecKit](https://github.com/github/spec-kit) | 94K | GitHub 官方，声明式工作流 |
| [gstack](https://github.com/garrytan/gstack) | 92K | 多维度评审 + 跨会话记忆 |
| [GSD](https://github.com/gsd-build/get-shit-done) | 61K | 上下文质量管理 + Goal-Backward |
| [BMAD](https://github.com/bmad-code-org/BMAD-METHOD) | 47K | 角色驱动 + 三层配置合并 |
| [OpenSpec](https://github.com/Fission-AI/OpenSpec) | 46K | 非线性工作流 + Action-based |
| [specs.md](https://github.com/fabriqaai/specs.md) | 142 | 多流程可选 + 自适应检查点 |

## 状态

本项目当前处于**设计阶段**（v0.3.1 讨论稿），尚未进入实现。

## License

MIT
