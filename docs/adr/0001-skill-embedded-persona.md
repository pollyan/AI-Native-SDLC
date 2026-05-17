# ADR 0001: 角色人格（Persona）内嵌于 Skill，不独立维护

## Date
2026-05-17

## Status
Accepted

## Context
在设计框架 v0.4 时，我们定义了三种 AI 模式/角色（Definer、Builder、Verifier），分别对应 Requirement Review、Plan/Execute 和 Review 阶段。在实现层面，需要决定这些角色是以独立的维度（如 `roles/` 目录）维护，还是作为 Skill（`skills/` 目录）的一部分。

由于框架的核心设计是：
1. 四个阶段都是独立可调用的工作流环节
2. 每个环节由一个单独的 Skill 承载
3. 角色能力在当前主要服务于特定的工作流阶段

## Decision
我们决定**不单独维护角色维度（不设 `roles/` 目录）**，而是**将角色人格（Persona）作为 Skill 的一个组成部分内嵌在 `SKILL.md` 中管理**。

## Consequences
- **Positive:** Skill 实现真正的高内聚和独立可用。当开发者调用某个阶段的 Skill 时，该阶段需要的 Persona、I/O 契约、处理逻辑一次性全加载，不需要处理多个配置文件的组合逻辑。
- **Positive:** 简化了文件目录结构和开发者的认知负担。
- **Negative:** 如果未来某个角色（例如"架构师"）需要横跨多个阶段（在 Plan 和 Review 都起作用），其核心提示词可能会在多个 `SKILL.md` 中存在一定程度的冗余。但目前基于"流程驱动"的设计，这种冗余是可以接受的。
