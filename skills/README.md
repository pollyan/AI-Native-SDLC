# AI-Native SDLC — Skills 目录

本目录包含 4 个阶段的 Skill，每个 Skill 独立成文件夹，主文件为 `SKILL.md`。

## 目录结构

```
skills/
├── requirement-review/   ← 阶段一：需求评审
│   └── SKILL.md
├── plan/                 ← 阶段二：方案设计
│   └── SKILL.md
├── execute/              ← 阶段三：开发执行
│   └── SKILL.md
└── review/               ← 阶段四：代码评审
    └── SKILL.md
```

## 各 Skill 职责

| Skill | 输入 | 输出 |
|-------|------|------|
| `requirement-review/` | 任意形式的需求描述 + `CONTEXT.md` | `specs/requirement-spec-<功能名>.md` |
| `plan/` | `requirement-spec-<功能名>.md` | `specs/plan-spec-<功能名>.md` |
| `execute/` | `plan-spec-<功能名>.md` | 代码（按切片提交） |
| `review/` | Requirement Spec + Plan Spec + 代码变更 | `specs/review-report-<功能名>.md` |

## 安装方式

将各 Skill 的 `SKILL.md` 内容加载到 AI 编码工具的系统提示词、Rules 或 Memories 中。  
各工具具体安装步骤请参见 `docs/installation-guide.md`。

## 客户侧项目结构

```
<项目根目录>/
├── CONTEXT.md                            ← 从 templates/CONTEXT.md 复制并填写，所有 Skill 共享
└── specs/
    ├── requirement-spec-<功能名>.md
    ├── plan-spec-<功能名>.md
    └── review-report-<功能名>.md
```

同一功能在所有阶段使用**相同的功能名**，便于追踪和引用。
