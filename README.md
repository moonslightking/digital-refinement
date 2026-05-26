# 数字炼化

语言：中文 | [English](README.en.md)

数字炼化是一套 skill，用于从投资者的历史材料中提取认知结构，并转化为可在 LLM agent 中运行的认知人格。

它适用于投资者、交易员、基金经理、投资写作者和投资 KOL。前提是目标人物有足够公开材料，能够还原其推理方式、决策风格、认知变化、风险处理方式和典型盲区。

## 产出内容

一次完整炼化会产出一个认知人格文件夹，通常包括：

- `SKILL.md`：可运行的人格 skill。
- `meta.json`：置信等级、材料覆盖、语料统计、验证结果和运行参数。
- `cognitive-timeline.md`：认知时间线。
- `cognitive-evolution-narrative.md`：认知演化叙事。
- `stage-profiles/`：各阶段认知画像。
- `cognitive-structure/`：继承层和元信息层。
- `references/`：采集证据和语境标注。
- `validation/`：留出集验证、功能测试、基线对照和交叉评审。

## 核心原则

- 认知可以被结构化为心智模型、决策启发式、价值排序、表达模式、偏误画像和诚实边界。
- 判断必须放回语境：日期、受众、市场环境、个人处境都要记录。
- 认知人格要保留目标人物的局限和偏差，不是制造一个完美投资者。
- 社会角色约束应放入元信息层，不应写成 agent 的硬性运行禁令。

## 工作流

```text
Phase 0 → Phase 0.1 → Phase 0.5 → Phase 1 → Phase 2 → Phase 3 → Phase 4 → Phase 5 → Phase 6
可行性    MCB校验     留出集切分   数据采集   语境标注   演化建模   结构提炼   人格构建   质量验证
评估
```

留出集切分必须发生在正式数据采集之前。MCB 校验必须发生在语境标注之前。

## 文件结构

```text
digital-refinement/
├── SKILL.md
├── README.md
├── README.en.md
├── README.zh-CN.md
├── references/
│   ├── methodology.md
│   ├── agent-prompts.md
│   ├── context-annotation-guide.md
│   ├── agm-decision-tree.md
│   └── MCB-SCHEMA.md
└── templates/
    ├── persona-skill-template.md
    ├── stage-profile-template.md
    └── meta-json-template.json
```

## 外部数据

投资者炼化通常需要 Macro Context Base（MCB）作为宏观语境基座。本 skill 提供 `references/MCB-SCHEMA.md`，但不内置真实 MCB 数据。

预期数据文件：

- `MCB_CN.jsonl`
- `MCB_US.jsonl`
- `MCB_META.json`

原始语料、私人笔记和封存留出集不应放进公开 skill 文件夹，除非这些材料已经确认可以公开。

## 安装方式

把 `digital-refinement` 文件夹放入 agent 的 skills 目录，确保文件夹根目录下存在 `SKILL.md`。

常见路径：

- Codex：`~/.codex/skills/digital-refinement/`
- Claude Code：`~/.claude/skills/digital-refinement/`

在本仓库中，先修改 `skills/skills-staging/digital-refinement/` 下的源文件。确认后再同步安装或打包。

## 使用场景

当用户要求炼化、蒸馏、构建或分析某个投资者的认知人格时触发本 skill。它也适用于分析决策风格、心智模型、认知偏误画像和思维演化轨迹。

典型提示：

- "炼化巴菲特的股东信，做成认知人格。"
- "提取这个投资者的心智模型和决策启发式。"
- "根据这个 KOL 的公开帖子构建认知人格。"
- "分析这位基金经理的思维方式如何随时间变化。"

## 质量要求

合格炼化必须包括：

- 来源清单和证据质量标注。
- 必要的 MCB 引用和个人语境标注。
- 阶段画像和认知演化解释。
- 继承层与元信息层分离。
- 留出集验证和交叉评审记录。
- 明确置信等级和使用边界。
