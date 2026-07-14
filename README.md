# Super Writer

一个面向中文长文/公众号文章的 AI 写作 Skill。它只负责：素材充分性检查、选题评分、研究、找核、攻核、结构、初稿和内容审稿。

明确不负责：去 AI 味、美化排版、配图和发布。最终稿通过 `handoff` 交给下游 Skill。

> **v0.2.0-rc2.1-hotfix** — `ready_for_personal_use` | 89 tests passed | MIT License

## 目录结构

```
super-writer/
├── SKILL.md                  # Skill 主入口（方法论核心）
├── VERSION                   # 版本与状态
├── CHANGELOG.md              # 完整变更历史
├── README.md                 # 本文件
├── LICENSE                   # MIT
├── ATTRIBUTION.md            # 引用与归属
├── MANIFEST.sha256           # 文件完整性校验
├── references/               # 方法论文档（按需加载）
│   ├── workflow.md           #   工作流与运行契约
│   ├── research-evidence.md  #   研究协议与证据地图
│   ├── core-finding.md       #   找核与压力测试
│   ├── structure-design.md   #   结构设计
│   ├── drafting.md           #   初稿写作
│   ├── editorial-review.md   #   内容审稿
│   ├── edit-learning.md      #   编辑学习
│   ├── voice-profile.md      #   文风画像
│   └── handoff.md            #   下游交接
├── templates/                # 输出模板
│   ├── writing-brief.md
│   ├── evidence-map.md
│   ├── core-card.md
│   ├── outline.md
│   └── editor-report.md
├── profiles/                 # Profile 示例
│   ├── audience-profile.example.yaml
│   ├── voice-profile.example.yaml
│   ├── belief-profile.example.yaml
│   └── edit-rules.example.yaml
├── scripts/                  # 工具脚本
│   ├── score_article.py      #   预检评分
│   ├── calibrate_scorer.py   #   评分器校准
│   ├── learn_edits.py        #   编辑学习
│   └── validate_skill.py     #   结构验证
├── tests/                    # 测试套件（89 个）
│   ├── test_structure.py
│   ├── test_calibration.py
│   └── fixtures/             #   测试样本
├── capability-matrix.md      # 能力对比矩阵
├── design-decisions.md       # 设计决策记录
└── conflict-resolution.md    # 冲突处置记录
```

## 设计原则

1. 事实真实性高于文风和戏剧性。
2. 先评估素材充分性，再决定能写到什么程度。
3. 先找到值得写的核心，再扩写。
4. 先构造证据地图，再写正文。
5. 作者经历只能来自用户输入或经历库。
6. Writer 与 Reviewer 分离；严重问题会阻塞交付。
7. 文风画像短小、可执行、可复用、分层加载。
8. 大规则按需加载，避免一个巨型 SKILL.md 占满上下文。
9. 允许失败退出，不强行产出有问题的文章。
10. 编辑学习自动分析但人工确认写入，禁止静默修改持久规则。

## v0.2 新增能力

### P1（核心能力）

- **素材充分性检查**：分维度评估已有素材，决定输出上限和后续动作。
- **两层选题评分**：HKR 判断阅读价值 + UEPT 判断可写性。
- **研究协议增强**：排斥分析 + 痛点深度检查 + 研究停止条件。
- **证据地图增强**：完整字段（来源日期、原始来源、支持/反对、适用边界、核验状态等）+ Reader Tension Statement。
- **四铲深化**：反转 / 追问前提 / 追问情绪 / 翻转定义。
- **Voice Profile 分层重构**：核心 8 维 + 可选 4 维，每维带 confidence/source_count/sources/scope。
- **内容审稿增强**：claim→mechanism 检查、A vs B 结构化对比、示例去重、"So what" 测试、视角审计。
- **编辑学习基础架构**：自动分析 + 自动提议 + 人工确认写入 + key 复用 + confidence 分级。
- **评分器 JSON Schema**：结构化 JSON 输出支持校准。
- **失败退出机制**：core_broken / evidence_conflict / unsuitable_topic 等退出状态。
- **评分器校准架构**：样本集格式、盲测隔离、MAE/Spearman/classification 指标。

### P2（增强能力）

- **承重类比裂口处理**：类比"裂开"的地方是文章最值钱的段落。
- **下游交接增强**：humanizer / formatter / publisher 三种下游 Skill 及触发条件。
- **编辑学习 key 复用与置信度更新**：自动合并重复 key、occurrences 累加、confidence 自动提升。
- **评分器校准执行框架**：支持真实样本校准（需 24 篇校准集 + 12 篇盲测集）。

### P3（补充完善）

- **Burstiness 诊断**：非阻塞节奏诊断，不设通用中文硬阈值，交接给 humanizer。

## 当前状态

**v0.2.0-rc2.1-hotfix** — `ready_for_personal_use`

- 89 个工程与行为测试：全部通过
- 4 场景冒烟测试：全部通过
- 正式 Phase 4 评测：跳过（个人使用）
- 迭代方式：真实使用持续验证（continuous dogfooding）

## 安装

### 从 GitHub 安装

```bash
git clone https://github.com/AIXM/super-writer.git ~/.claude/skills/super-writer
```

### 验证安装

```bash
cd ~/.claude/skills/super-writer
python -m pytest tests/ -v
```

应看到 `89 passed`。

## 使用方法

### 基本用法

在 Agent 对话中直接说：

```text
我想写一篇关于 XX 的公众号文章，素材在 materials 目录里。
帮我把这些散乱想法整理成一篇长文。
围绕"XX"找一个真正值得写的角度。
审一下 article.md，只检查内容和逻辑。
根据我改前和改后的文章，学习我的编辑偏好。
```

Skill 会自动按以下 7 个 Phase 执行（根据输入状态跳过不必要步骤）：

| Phase | 名称 | 做什么 |
|-------|------|--------|
| 1 | 写作简报 | 确认主题、读者、文章类型、约束 |
| 1.5 | 素材充分性检查 | 评估 6 个维度，决定输出级别 |
| 2 | 研究 | 证据地图、来源等级标注、研究停止条件 |
| 3 | 找核 | 核心卡 + 攻核六刀压力测试 |
| 4 | 结构设计 | 大纲、叙事弧、章节承重检查 |
| 5 | 初稿 | 按已确认核心扩写 |
| 6 | 审稿 | P0-P3 分级问题检查 |
| 7 | 修订 | 修复问题，生成交接信息 |

### 素材准备

每次写作时，建议准备以下素材（不必全部具备）：

- **主题与核心想法**：你想写什么，你想说什么
- **个人经历**：你亲历的事情（只有你能提供）
- **公开资料**：文章、报告、数据（附来源链接）
- **已有草稿**：如果已经写了初稿
- **约束条件**：字数、读者、平台、风格偏好

### 素材充分性门禁

Skill 会检查 6 个维度：`topic` / `audience` / `core_opinion` / `evidence` / `personal_experience` / `voice_context`。

- 全部 sufficient → 正常产出完整文章
- 部分缺失 → 标注编辑锚点，降级输出
- 关键缺失 → 返回 `needs_author_input` 或 `research_limited`

### 失败退出

以下情况 Skill 会停止并说明原因，而不是强行产出有问题的文章：

| 退出状态 | 含义 |
|----------|------|
| `ready_to_write` | 可以进入写作 |
| `needs_research` | 需要更多研究 |
| `needs_author_input` | 需要作者补充经历或确认 |
| `research_limited` | 研究受限，降级为大纲 |
| `core_broken` | 核心观点在压力测试中崩塌 |
| `evidence_conflict` | 证据互相矛盾，无法调和 |
| `unsuitable_topic` | 选题没有信息增量 |
| `review_blocked` | 审稿发现不可修复的 P0 |

### 产物

完整流程可产出：

- `writing-brief.md`
- `material-readiness.yaml`
- `evidence-map.md`
- `core-card.md`
- `outline.md`
- `article.md`
- `editor-report.md`（Markdown + JSON）
- `generation-profile.yaml`
- `handoff.yaml`

### 下游交接

Super Writer 完成后输出 `handoff.yaml`，可交给下游 Skill：

- **humanizer**：去 AI 味、节奏调整（推荐）
- **formatter**：公众号排版、格式转换（可选）
- **publisher**：直接发布（可选）

## 测试

```bash
cd super-writer
python -m pytest tests/ -v
```

89 个测试分为三类：

- **结构测试**（26 个）：检查文件中存在必要字段和配置
- **脚本单元测试**（16 个）：测试 MAE、Spearman（含 ties）、分类准确率等指标计算
- **行为测试**（47 个）：端到端 CLI 测试、真实 fixture 加载、编辑学习冲突流程、校准器机器分输入、严格 Schema 校验、输入防御检查等

## 设计文档

- `capability-matrix.md`：跨 10 个项目、20 个维度的能力对比矩阵
- `design-decisions.md`：16 项变更的详细设计方案（含评审修订）
- `conflict-resolution.md`：14 项规则冲突的处置决策（结构化格式，支持重开）

## 迭代规则

本版本采用真实使用持续验证（dogfooding）模式：

1. 直接投入日常写作，记录真实问题
2. 严重问题立即修复
3. 普通问题重复出现 2~3 次后再纳入规则
4. 每累计 5~10 篇文章集中复盘和升级一次
5. 不因单篇文章的问题立即修改通用写作规则
6. 后续升级不得静默覆盖当前稳定版本

## License

[MIT](LICENSE)
