# 下游交接契约

写作 Skill 完成后输出：

```yaml
handoff:
  article_path: article.md
  content_score: 0
  p0_count: 0
  exit_status: ready_to_write      # 来自 workflow.md 的退出状态
  unresolved_facts: []
  editor_anchors: []
  author_confirmations: []
  preserve_exactly: []
  next:
    - skill: humanizer
      trigger: "P3 表达层问题存在或用户要求去 AI 味"
      priority: recommended
    - skill: formatter
      trigger: "需要公众号/多平台排版格式转换"
      priority: optional
    - skill: publisher
      trigger: "用户要求直接发布到平台"
      priority: optional
```

## 下游 Skill 说明

| Skill | 职责 | 触发条件 | 传递内容 |
|-------|------|----------|----------|
| humanizer | 去 AI 味、节奏调整、表达优化 | P3 问题存在或用户要求 | 核心观点、不可删证据、必须保留原话、编辑锚点、内容已确认边界 |
| formatter | 排版、格式转换、标题优化 | 需要平台格式转换 | 标题、摘要、正文、引用、脚注、配图建议点位 |
| publisher | 多平台发布 | 用户要求直接发布 | 完成稿 + 格式标记 |

## 给去 AI 味 Skill

传递：核心观点、不可删证据、必须保留的作者原话、编辑锚点、内容已确认边界。去 AI 味过程中不得改变事实、立场和论证关系。

## 给排版 Skill

传递：标题、摘要、正文、引用、脚注、配图建议点位。排版 Skill 不得重写文章核心内容。

## 未决项

未决事实、作者未确认立场和缺失经历必须显式保留，不得因进入下一环节而消失。
