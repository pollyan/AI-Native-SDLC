# Review Report：<功能名称>
> 版本：1.0 | 日期：<YYYY-MM-DD>
> 审查来源：Requirement Spec `specs/requirement-spec-<功能名>.md` + Plan Spec `specs/plan-spec-<功能名>.md`
> Developer 签署：_________________ 日期：_________

---

## Axis 1 — 代码质量（Code Quality）

> 独立审查维度，不参照其他轴的发现。

| ID | 文件:行号 | 严重程度 | 描述 | 建议修复方式 |
|----|---------|---------|------|------------|
| Q-001 | `<file>:<line>` | Critical / Important / Minor | | |
| Q-002 | `<file>:<line>` | | | |

> **严重程度说明：**
> - `Critical`：必须修复，存在正确性错误、安全漏洞或重大性能问题
> - `Important`：强烈建议修复，影响可维护性或健壮性
> - `Minor`：可选改进，不影响功能

**Axis 1 汇总：** Critical: X 个 | Important: X 个 | Minor: X 个

---

## Axis 2 — 需求一致性（Requirements Consistency）

> 对照 Requirement Spec 逐条验证实现。独立审查维度，不参照其他轴。

| FR ID | 需求描述（摘要） | 实现状态 | 说明 |
|-------|---------------|---------|------|
| FR-001 | | Implemented / Partial / Missing / Wrongly Implemented | |
| FR-002 | | | |

> **实现状态说明：**
> - `Implemented`：完全按 FR 定义实现
> - `Partial`：部分实现，有遗漏
> - `Missing`：未实现
> - `Wrongly Implemented`：实现了但行为与 FR 定义不符

**Axis 2 汇总：** Implemented: X | Partial: X | Missing: X | Wrongly Implemented: X

---

## Axis 3 — 验收标准验证（AC Validation）

> 对照 Plan Spec 逐个切片、逐条 AC 验证。独立审查维度，不参照其他轴。

| 切片 | AC 描述（摘要） | 验证结果 | 证据 |
|------|--------------|---------|------|
| S-001 | | Pass / Fail / Unverifiable | |
| S-002 | | | |

> **验证结果说明：**
> - `Pass`：AC 可被验证且通过
> - `Fail`：AC 未通过（附证据）
> - `Unverifiable`：无法通过自动化或手动方式验证（说明原因）

**Axis 3 汇总：** Pass: X | Fail: X | Unverifiable: X

---

## Developer 决策记录（Decision Record）

> 每条 Critical 发现必须有 Developer 的明确决策，不得遗漏。

| 发现 ID | 严重程度 | Developer 决策 | 决策日期 | 备注 |
|--------|---------|--------------|---------|------|
| Q-001 | Critical | 立即修复 / 接受风险 / 延期到 Backlog | | |

---

## 门控自审核（Gate Checklist）

> AI 完成三个轴的审查后必须逐项检查，全部通过后才能输出给 Developer。

- [ ] Axis 1 完成：所有代码问题均有 `文件:行号` 引用和建议修复方式
- [ ] Axis 2 完成：Requirement Spec 中每条 FR 均有实现状态标注
- [ ] Axis 3 完成：Plan Spec 中每个切片的每条 AC 均有验证结果和证据
- [ ] 所有 Critical 发现均已在 Developer 决策记录中等待或收到决策
- [ ] 三个轴的审查独立进行（不在同一上下文中混合执行）

**Developer 签署：** _________________ **日期：** _________
