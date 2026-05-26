---
name: digital-refinement
description: >
  数字炼化（Digital Refinement）：从历史材料中提取投资者的认知结构——心智模型、决策启发式、
  价值排序、认知演化轨迹——并转化为可在 LLM 上运行的认知人格（Cognitive Persona）。
  当用户提到"炼化"、"认知蒸馏"、"数字投影"、"认知人格"、"投资者画像"、
  "心智模型提取"、"思维方式分析"时，使用此技能。也适用于分析投资者的决策风格、
  认知偏误画像、思维演化轨迹等任务。务必在用户要求对任何投资者/交易员/KOL进行
  认知建模、人格提取、心智分析时触发此技能，即使用户没有明确使用"炼化"一词。
---

# 数字炼化：投资者认知蒸馏执行技能

本技能用于把投资者、交易员或投资 KOL 的历史材料炼化为可运行的认知人格。完整方法论见 `references/methodology.md`。

## 核心理念

1. **认知可结构化**：一个人反复出现的决策模式、价值排序、推理偏好可以被识别和形式化。
2. **认知必须带语境**：任何判断脱离其产生时的环境都会被扭曲。
3. **认知在时间中演化**：思维方式会扩展、修正、收缩，也可能出现言行分裂。

## 执行流程总览

炼化分 8 个阶段，严格顺序执行。每个阶段都有明确输入、操作和输出。

```text
Phase 0 → Phase 0.1 → Phase 0.5 → Phase 1 → Phase 2 → Phase 3 → Phase 4 → Phase 5 → Phase 6
可行性    MCB校验     留出集切分   数据采集   语境标注   演化建模   结构提炼   人格构建   质量验证
评估
```

**顺序约束**：Phase 0.1 必须在 Phase 0.5 之前完成；Phase 0.5 必须在 Phase 1 之前完成。

## Phase 0：炼化可行性评估

### 目标

判断目标人物是否适合炼化，识别数据缺口。

### Step 1：材料可用性扫描

对目标人物的公开材料进行快速扫描，不深读，输出来源清单：

```json
{
  "target": "人物姓名",
  "active_period": "YYYY-YYYY",
  "source_inventory": [
    {
      "source_name": "来源名称",
      "type": "一手著作 | 演讲 | 访谈 | 决策记录 | 传记 | 学术分析",
      "date_range": "YYYY-YYYY",
      "availability": "免费在线 | 需购买 | 部分付费",
      "estimated_volume": "页数/小时/篇数",
      "url": "获取链接（如有）"
    }
  ]
}
```

### Step 2：五维评估

| 维度       | 评分标准                     | <2 分的后果            |
| -------- | ------------------------ | ------------------ |
| 一手材料丰富度  | ★1-5：是否有足够的直接言论/著作？      | **致命**：不适合炼化       |
| 决策可观测性   | ★1-5：其决策是否有公开记录且可追溯结果？   | **致命**：不适合炼化       |
| 认知演化可追溯性 | ★1-5：时间跨度是否足够大，能否追踪思维变化？ | 非致命：可继续，但置信度降档     |
| 语境可还原性   | ★1-5：言论的场合、受众、目的是否可考？    | 非致命：可继续，标注"语境还原困难" |
| 现有分析深度   | ★1-5：是否有他人的深度分析可作交叉验证？   | 非致命：可继续，标注"无交叉验证"  |

判定规则：

- 前两项任一 <2：终止流程。
- 后三项 <2：可继续，但在 `meta.json` 标注并预期置信度降档。
- 5 项均 >=3：适合炼化。
- 总分 <15：建议更换目标或降低预期置信等级。

### Step 3：输出

保存 `phase-0-assessment.md`。

## Phase 0.1：MCB 校验

### 目标

确认宏观语境基座（MCB）已就绪，覆盖目标人物的活跃年份。

### 执行步骤

1. 确认 MCB 数据库 `digital-refinement/references/macro-context-base/` 覆盖目标人物的活跃年份。
2. 如果 MCB 尚未覆盖所需时间范围，先补充 MCB 再继续。
3. 检查对应月份的六层数据完整度，标记薄弱月份。
4. 将校验结果写入 `meta.json` 的 `phase_completion.phase_0_1`。

### MCB 说明

MCB（Macro Context Base）是跨炼化通用的共享资源，不属于任何单次炼化项目：

- 存放路径：`digital-refinement/references/macro-context-base/`
- 文件：`MCB_CN.jsonl`、`MCB_US.jsonl`、`MCB_META.json`
- Schema 定义：`references/MCB-SCHEMA.md`
- 6 层数据维度：宏观经济层、股市层、行业周期层、政策事件层、市场情绪层、叙事层
- 数据来源：Wind 终端导出、公开数据、Web search

**外部依赖**：MCB 数据文件不包含在本 skill 包中。首次使用前需确认上级 MCB 目录已就绪。本包仅提供 MCB 的 Schema 定义。

## Phase 0.5：留出集切分

**此步骤必须在 Phase 1 之前完成。**

### 第一层：内容隔离

`holdout-manifest.json` 采用最小信息格式，只记录抽样 ID 和排除指令，不记录材料标题、日期、内容描述：

```json
{
  "holdout_set": [
    {
      "id": "H001",
      "source_unit_id": "U078",
      "excluded_from_agents": ["agent-1", "agent-2", "agent-3", "agent-5", "agent-6"],
      "hash": "sha256:a1b2c3d4e5f6..."
    }
  ],
  "total_count": 12,
  "coverage_periods": ["2005-2007", "2008-2009", "2012-2014", "2019-2022"],
  "coverage_types": ["著作", "对话", "决策", "社交媒体"]
}
```

### 第二层：执行隔离

- Phase 1 的采集指令中只包含排除 ID 列表，不包含任何内容提示。
- 如果无法精确排除单条材料，排除整个合辑，并在 `meta.json` 记录排除范围。

### 第三层：时间隔离

- 留出集的完整内容封存到 `holdout-sealed.json`，Phase 1-5 期间不存放在工作目录中。
- 推荐由另一个独立 LLM 实例创建和保管留出集，Phase 6 时交付。
- 折中方案是把留出集内容文件存放在工作目录之外的隔离路径，Phase 6 开始时手动移入。
- Phase 6 解封时必须校验哈希。

材料单元 <40 时，留出集可缩减为 5 条，并标注验证置信度受限。

## Phase 1：多维数据采集

### 7-Agent 并行架构

| Agent         | 职责       | 提取内容                   |
| ------------- | -------- | ---------------------- |
| Agent 1: 公开著述 | 直接言论提取   | 核心观点、论证逻辑、用词模式         |
| Agent 2: 对话记录 | 互动中的思维展现 | 即兴回答、追问时的深层逻辑、回避的话题    |
| Agent 3: 行为证据 | 实际行动分析   | 投资决策、时间分配、资源配置模式       |
| Agent 4: 他人评价 | 外部观察收集   | 同行、合作者、批评者的评价          |
| Agent 5: 智识谱系 | 追踪信息源链   | 他学了谁、被谁影响              |
| Agent 6: 反面证据 | 收集不利材料   | 批评质疑、承认失误、被市场证伪的案例     |
| Agent 7: 个人环境 | 记录人物个人环境 | 资产规模、仓位、生活事件、社交圈、信息源变化 |

### 材料溯源校验

Agent 1 输出后执行：

- 每条采集材料标注出处链（原始发布平台 → 转载路径）。
- 署名不明确的材料执行风格一致性检查。
- 风格不一致或署名不明确的材料，`authenticity` 直接标 L。

### 来源质量评估

```json
{
  "content": "原始内容",
  "source_type": "一手著作 | 长对话 | 实际决策 | 他人转述 | 理论引用",
  "date": "YYYY-MM-DD",
  "audience": "公众 | 专业群体 | 私人 | 自我反思",
  "purpose": "教育 | 辩护 | 反思 | 宣传 | 即兴回应",
  "provenance": "原始发布平台 → 转载路径",
  "quality_scores": {
    "authenticity": "H/M/L",
    "representativeness": "H/M/L",
    "explanatory_power": "H/M/L",
    "verifiability": "H/M/L"
  },
  "agent_source": "Agent 1-7",
  "extraction_notes": "提取时的观察和疑问"
}
```

### 优先级映射

- 心智模型提取：Representativeness + Explanatory Power
- 表达 DNA 分析：Authenticity
- 认知偏误推断：Verifiability
- 认知演化追踪：date 精确性 + Authenticity

### 输出

```text
references/research/
├── agent-1-writings.json
├── agent-2-conversations.json
├── agent-3-behavioral.json
├── agent-4-external.json
├── agent-5-intellectual.json
├── agent-6-counter.json
└── agent-7-personal-environment.json
```

## Phase 2：语境标注

### 目标

为 Phase 1 采集的每条关键数据添加完整环境语境。

### 标注结构

```json
{
  "statement_id": "S001",
  "statement": "原始内容",
  "date": "YYYY-MM-DD",
  "mcb_ref": "CN-2008-10",
  "personal_context": "基金当年回撤约15%，已提前减仓",
  "audience_and_purpose": "年度投资者大会，面向LP",
  "context_dependency": "high",
  "dependency_evidence": "四步判定路径记录"
}
```

字段规则：

- `mcb_ref` 引用 MCB 月度切片，可为字符串或字符串数组。
- `personal_context` 只记录人物个人状态，控制在 50 字以内。
- `audience_and_purpose` 记录对谁说、为什么说。
- `dependency_evidence` 记录语境依赖度判定路径。

### 语境依赖度判定规则

1. 该言论是否包含时间敏感的判断？否 → Step 2；是 → 初步标记 `high`，进入 Step 3。
2. 移除所有语境信息后，含义是否改变？不变 → `low`；改变 → `medium`，进入 Step 3。
3. 该言论的"正确性"是否依赖于当时环境条件？是 → `high`；否 → `medium`。
4. 对 `high` 依赖内容标注失效条件。

判定路径必须写入 `dependency_evidence`。

### 输出

`references/context-annotations/`，按时间排序。

## Phase 3：认知演化建模

### 目标

在时间维度上追踪对象的认知变化，划分认知阶段。

### 步骤一：时间线构建

将 Phase 1-2 数据按时间排列，输出 `cognitive-timeline.md`。

### 步骤二：矛盾检测与 AGM 分类

AGM 四类操作：

| AGM 操作 | 含义              | 识别特征         |
| ------ | --------------- | ------------ |
| 扩展     | 加入新信念，未替代旧信念    | 旧观点仍维持，新领域扩展 |
| 修正     | 新信念替代旧信念        | 有明确自述或行为替代   |
| 收缩     | 放弃旧信念           | 旧观点消失，无替代    |
| 分裂     | 认知维持旧信念，行为违反旧信念 | 言行不一致且未自觉反思  |

AGM 分类置信度：

| 置信度    | 标准                  | 权重      |
| ------ | ------------------- | ------- |
| High   | 明确自述 + 行为改变 + 时间点清晰 | 可作为阶段分界 |
| Medium | 两项证据，缺第三项           | 阶段内重要事件 |
| Low    | 仅有推理支撑              | 仅参考标注   |

只有 High 和 Medium 才能用于阶段划分。

输出格式：

```text
矛盾 M003：
  旧观点：[内容]（T1）
  新观点：[内容]（T2）
  AGM 类型：修正
  置信度：High
  失效时间：T2 - T1
  沉默期：[是/否]
```

沉默期规则：>3 年沉默 + 无自述反思，默认收缩而非修正。

### 步骤三：认知跃迁识别

至少 3 个条件同时满足，才认定为认知跃迁：

1. 自述框架级反思。
2. 多领域行为同时变化。
3. 变化不可逆。

不足则降级为"观点变化"。

### 步骤四：阶段划分与画像

为每个阶段生成画像，模板见 `templates/stage-profile-template.md`。环境剧变不单独构成分界线，必须有可观测认知响应。

### 步骤五：演化叙事

输出 `cognitive-evolution-narrative.md`，用证据链说明阶段之间如何转折。

## Phase 4：认知结构提炼

### 双层结构

**继承层 (Inheritable Layer)**：agent 默认加载。

- 4.1 心智模型：跨域复现、生成力、排他性、时间稳定性。
- 4.2 决策启发式：仅保留思考路径类规则，剥离行为约束。
- 4.3 表达 DNA：句式、概念、确定性梯度。
- 4.4 认知偏误画像。
- 4.5 价值排序与张力。
- 4.6 智识谱系。

**元信息层 (Meta Layer)**：仅作历史语境，不作为 agent 执行约束。

- 4.7 自我规训条款。

### 自我规训条款

满足任一条件即归为自我规训：

1. 自我约束性语言。
2. 约束行为输出而非思考路径。
3. 依赖社会角色而非认知逻辑。

输出格式：

```text
自我规训条款 D1：[名称]
原文：[人物原话]
立约时间：YYYY-MM-DD
立约动机：[原因]
适用情境：[场景]
是否迁移至 agent：No
不迁移理由：[原因]
```

自我规训条款一律不迁移至 agent。如果某条规训实质上反映认知局限，应写入诚实边界，而不是作为执行禁令。

### 输出

```text
cognitive-structure/
├── inheritable-layer.md
└── meta-layer.md
```

## Phase 5：认知人格构建

### 输出目录结构

```text
[person-name]-cognitive-persona/
├── SKILL.md
├── meta.json
├── holdout-manifest.json
├── cognitive-timeline.md
├── cognitive-evolution-narrative.md
├── stage-profiles/
│   ├── stage-1-[name]-[period].md
│   └── ...
├── cognitive-structure/
│   ├── inheritable-layer.md
│   └── meta-layer.md
├── references/
│   ├── research/
│   ├── context-annotations/
│   └── sources/
└── validation/
    ├── holdout-validation.md
    ├── functional-tests.md
    ├── baseline-comparison.md
    └── cross-review.md
```

### SKILL.md 结构

1. 身份卡
2. 运行声明
3. 认知阶段声明
4. 继承层：心智模型、决策启发式、表达 DNA、偏误画像、价值排序、智识谱系
5. 元信息层：自我规训条款
6. 诚实边界
7. 使用指南

### meta.json 结构

使用 `templates/meta-json-template.json`。必须包含 `metadata`、`quality`、`structure`、`corpus_stats`、`validation`、`runtime`、`phase_completion` 七组信息。

## Phase 6：质量验证

### Phase 6.1 留出验证

1. 解封留出集。
2. 校验 manifest hash 与实际文件 SHA256。
3. 执行人格回答 vs 真实立场盲比。
4. 写入 `holdout_integrity`：`verified | hash_mismatch | seal_broken`。
5. `seal_broken` 时验证结论降级为参考级。

### Phase 6.2 功能测试

执行边缘推断测试、时间一致性测试、反身性测试。

### Phase 6.3 反例与基线对照

执行反例测试、基线对照、可区分性测试。

### Phase 6.4 交叉评审

由独立审阅者检查证据链、推断强度、偏误画像和边界声明。

### 综合通过标准

**A 档**：所有维度优秀。留出集大多数命中且无根本对立，可区分性明显，结构完整，高真实性来源占多数。

**B 档**：部分不足。少量不一致但无根本矛盾，可区分性存在但部分趋同，数据有已知缺口。

**C 档**：多维度不足。存在根本性矛盾、可区分性不显著或数据薄弱。

定档规则：默认 A 档；根本性矛盾或不可区分直接降至 C；缺口、部分缺失、封存受损降一档。

## 运行模式

- **思考模式（默认）**：仅加载继承层，产出判断和建议。
- **情境复刻模式（显式触发）**：加载继承层 + 元信息层规训，模拟对象在其历史社会角色下的行为模式。
- **历史模式**：指定时期阶段画像。
- **演化模式**：同一问题的认知变化轨迹。
- **对比模式**：多人格对比。

### 置信层级标注

- 🟢 有据可查：对象曾明确表态。
- 🟡 模型推断：基于心智模型可合理推断。
- 🔴 超出范围：认知人格无法提供有意义引导。

## 诚实约束

1. 认知人格是引导而非覆盖：它应帮助使用者理解对象如何思考，而不是替使用者下结论。
2. 诚实边界不可越过：标注为超出范围的领域必须退回中立立场。
3. 语境提示而非自动断言：引用 MCB 提示环境相似性，不自动断言当前环境等同于某个历史阶段。
4. 不模拟"完美的某人"：显式标注对象的典型认知偏差。

## 快速启动指南

### 新炼化项目

1. 阅读本文件。
2. 执行 Phase 0 评估。
3. 执行 Phase 0.1 MCB 校验。
4. 按 Phase 0 → 0.1 → 0.5 → 1 → 2 → 3 → 4 → 5 → 6 执行。
5. 每个 Phase 完成后写入 `meta.json`。

### 使用已有人格

1. 加载 `[person]-cognitive-persona/SKILL.md`。
2. 默认进入思考模式。
3. 如需情境复刻，说"切换到情境复刻"。

## 参考文件索引

| 文件                                       | 内容               | 何时阅读                  |
| ---------------------------------------- | ---------------- | --------------------- |
| `references/methodology.md`              | 完整方法论文档          | 深入理解理论依据时             |
| `references/agent-prompts.md`            | 7 个 Agent 详细提取指令 | Phase 1 执行时           |
| `references/context-annotation-guide.md` | 语境标注操作手册         | Phase 2 执行时           |
| `references/agm-decision-tree.md`        | AGM 分类完整决策树      | Phase 3 执行时           |
| `references/MCB-SCHEMA.md`               | MCB Schema 定义    | Phase 0.1 和 Phase 2 时 |
| `templates/persona-skill-template.md`    | 认知人格模板           | Phase 5 执行时           |
| `templates/stage-profile-template.md`    | 阶段画像模板           | Phase 3-4 执行时         |
| `templates/meta-json-template.json`      | meta.json 模板     | 全流程                   |

