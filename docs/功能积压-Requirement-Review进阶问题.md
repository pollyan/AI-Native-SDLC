# 功能积压：Requirement Review 进阶问题

> 创建日期：2026-05-17
> 状态：待定（未来按需启用）
> 来源：竞品调研（gstack Six Forcing Questions、superpowers Scope Check）
> 决策：MVP 只做理解层问题，挑战性问题留给未来

---

## 背景

Six Forcing Questions 的内核是好的，但在企业端：
- 开发人员是被动的需求接收者，很少有机会质疑"为什么要做这个"
- 产品经理已经做了权衡，有些方案背后是业务妥协
- 挑战性问题可能给开发人员带来困扰和团队摩擦

**决策：MVP 只做理解层（6 个必问问题），挑战性问题记录在此，未来按需启用。**

---

## MVP 已采纳：理解层问题（6 个）

| # | 问题 | 目的 | 来源 |
|---|------|------|------|
| 1 | 这个需求要解决什么业务问题？ | 确保理解背景 | gstack Q1 + bmad Product Brief |
| 2 | 用户是谁？他们的核心场景是什么？ | 确保理解用户 | gstack Q3 |
| 3 | 怎么判断做完了？验收标准是什么？ | 确保可交付 | gsd Acceptance Criteria |
| 4 | 有什么边界条件和异常场景？ | 确保覆盖完整 | bmad Edge Case Hunter |
| 5 | 和已有的功能有什么关联/依赖？ | 确保不遗漏上下文 | matt-pocock 交叉验证 |
| 6 | 有什么明确的约束（时间/技术/兼容性）？ | 确保可行 | gsd Constraint Clarity |

---

## 未来可选：洞察层问题（3 个）

**启用条件：** 团队文化鼓励开发人员参与需求讨论时

| # | 问题 | 定位 | 来源 |
|---|------|------|------|
| 1 | 有没有更简单的实现路径？ | 工程优化建议，不是质疑需求 | superpowers YAGNI |
| 2 | 如果只做一半，最小可用版本是什么？ | 帮助理解优先级 | gstack Q4 Narrowest Wedge |
| 3 | 有没有可能用已有的方案替代？ | 避免重复建设 | superpowers Scope Check |

---

## 未来可选：挑战层问题（6 个）

**启用条件：** 技术驱动型团队，开发人员有权限参与需求决策时

### gstack Six Forcing Questions 原始版

| # | 原始问题 | 企业版改编 | 来源 |
|---|---------|-----------|------|
| Q1 | 需求证据是什么（不是兴趣，是行为） | 有没有用户反馈或数据支撑这个需求？ | gstack Demand Reality |
| Q2 | 用户现在怎么解决这个问题的 | 现有方案的成本和痛点具体是什么？ | gstack Status Quo |
| Q3 | 说出具体的人名、职位、后果 | 能不能举一个具体的用户故事？ | gstack Desperate Specificity |
| Q4 | 最小可付费版本是什么 | 这个需求的核心价值点是什么？砍掉什么不影响核心？ | gstack Narrowest Wedge |
| Q5 | 你有没有实际观察用户使用 | 我们有没有用户的实际使用数据？ | gstack Observation & Surprise |
| Q6 | 世界变了你的产品更还是更不重要 | 这个需求的时效性如何？会不会很快过时？ | gstack Future-Fit |

---

## 未来可选：高级功能

### gstack 反谄媚规则

**启用条件：** 团队需要高质量的需求验证时

```
禁止说"这个需求看起来很合理"
必须对每个需求表态：清晰 / 模糊 / 矛盾 / 缺失
必须指出具体什么证据会改变判断
```

### bmad 对抗性审查

**启用条件：** 高风险需求需要深度验证时

```
以"愤世嫉俗的审查者"角色重新审视需求
目标：找到至少 5 个问题
输出：问题清单 + 修复建议
```

### gstack 跨模型第二意见

**启用条件：** 关键需求需要独立验证时

```
结构化上下文摘要传给独立模型
输出：Steelman 版本 / 挑战一个前提 / 原型建议
前提修订检查：如果第二意见挑战了已同意的前提，让用户决定
```

### gsd 访谈视角轮换

**启用条件：** 复杂需求需要多角度验证时

```
Researcher → Simplifier → Boundary Keeper → Failure Analyst → Seed Closer
每轮 2-3 个问题，最多 6 轮
```

---

## 启用策略

| 阶段 | 启用内容 | 条件 |
|------|---------|------|
| **MVP** | 理解层 6 问 | 默认启用 |
| **v1.1** | 洞察层 3 问 | 团队主动启用 |
| **v1.2** | 挑战层 6 问 + 反谄媚规则 | 团队主动启用 |
| **v2.0** | 对抗性审查 + 跨模型意见 + 视角轮换 | 高级团队 |

---

## 参考

- gstack office-hours/SKILL.md（Six Forcing Questions）
- superpowers brainstorming/SKILL.md（Scope Check）
- gsd spec-phase.md（Ambiguity Model）
- bmad product-brief/SKILL.md（Coach Don't Quiz）
- bmad review-adversarial-general/SKILL.md（Adversarial Review）
