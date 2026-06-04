---
name: paper-auto-flow
description: >-
  Unified Chinese-first academic paper workflow orchestrator. Routes user intent
  to the correct subset of paper-related skills across the full research
  lifecycle: ideation, literature search, paper reading, reference-paper style
  extraction, manuscript writing/polishing, figure design, self-review, reviewer
  response, and presentation. Use whenever the user asks anything related to
  academic paper writing, research, literature, figures, citations, review
  response, or paper presentation. Supports Nature/CNS, ML conferences, general
  academic writing styles, and user-provided reference papers in Word/PDF for
  style imitation. Never runs all skills at once; intelligently selects and
  chains 1-3 sub-skills based on the user's specific request.
version: 1.1.0
author: Adapted from paper-flow and customized as paper-auto-flow
tags: [Paper, Research, Writing, Meta-Skill, Orchestrator, Academic]
---

# Paper Auto Flow - 论文研究工作流统一调度

## 核心设计理念

`paper-auto-flow` 是一个**元技能（Meta-Skill）**。它本身不执行所有论文操作，
而是作为智能路由器，将用户的自然语言需求精准映射到 1-3 个最合适的子 skill。

```
用户的自然语言请求
       |
       v
+-------------------+
| paper-auto-flow   |  <- 意图解析 + 智能路由
| 本技能            |
+---------+---------+
          |
          v
调度 1-3 个子 skill，并在必要时传递参考论文风格摘要
```

**关键原则**：
- 每次调用只激活 1-3 个相关子 skill。
- 根据用户需求自动判断调用哪些 skill。
- 如果需求跨多个阶段，按逻辑顺序串联执行。
- 不一次性运行所有 skill。
- 不对模糊请求做过度假设。
- 当用户提供模仿论文时，先提取“可迁移写作特征”，再用于写作或润色；不得复制原文句子、段落或未授权内容。

---

## 论文全生命周期与 Skill 映射

```
研究构思 -> 文献检索 -> 论文阅读 -> 参考论文风格提取 -> 写作润色
    |                                                   |
    |                +----------------------------------+
    |                v
    +----------> 图表制作 -> 数据分析 -> 论文自审
                                      |
                                      v
                              审稿回复 -> 成果展示
```

---

## 意图路由表

### 使用方式

当用户有论文相关需求时，只需用自然语言描述目标。`paper-auto-flow` 自动判断
应该调用哪些 skill，并尽量将任务拆成最小可执行链路。

---

### 阶段 0：研究构思

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "新课题" "研究空白" "idea" "5W1H" "brainstorm" | `research-ideation` | 系统性研究构思 |
| + "文献综述" "related work" | `research-ideation` -> `nature-academic-search` | 构思 + 文献检索 |
| + "实验设计" "methodology" | `research-ideation` -> `nature-data` | 构思 + 数据规划 |

---

### 阶段 1：文献检索与梳理

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "找文献" "search papers" "检索" | `nature-academic-search` | 多源学术检索（PubMed/CrossRef/arXiv） |
| "Nature引用" "CNS引用" "分段引用" "补引用" | `nature-citation` | Nature/CNS 引用 |
| "Google Scholar搜" "学术搜索" | `gs-search` / `gs-advanced-search` | Google Scholar 检索 |
| "谁引用了" "citation tracking" | `gs-cited-by` | 引文追踪 |
| "下载PDF" "全文" "sci-hub" | `gs-fulltext` | 全文获取 |
| "导出Zotero" "BibTeX" "RIS" | `gs-export` | 导出引用管理 |
| "Web of Science" "WoS检索" | `web-of-science-research` | WoS 检索 |
| "按中科院分区筛选" "CAS quartile" | `reference-screen-by-cas-quartile` | 分区筛选 |
| "验证引用" "citation check" "DOI验证" | `citation-verification` | 引用真实性验证 |
| "找论文+筛选" "检索+分区" | `nature-academic-search` -> `reference-screen-by-cas-quartile` | 检索 + 分区筛选 |

---

### 阶段 2：论文阅读

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "翻译论文" "中英对照" "全文翻译" "精读" | `nature-reader` | 全文双语 Markdown 阅读器 |
| "读PDF" "看论文" | `pdf` -> `nature-reader` | PDF 提取 + 双语阅读 |
| "读Word" "读DOCX" "读取论文草稿" | `doc` / `documents` | DOCX 提取、排版检查或内容阅读 |
| "每日论文" "today's papers" | `daily-paper-generator` | 每日论文推送 |

---

### 阶段 2.5：参考论文上传与模仿写作风格提取

当用户说：

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "模仿这篇论文" "按这篇文章风格" "学习这个PDF写法" "参考这篇Word" | `pdf` 或 `doc` / `documents` -> `nature-polishing` | 读取参考论文并抽取写作风格 |
| "上传目标论文" "参考文献模板" "仿写结构" "模仿Results/Discussion" | `pdf` 或 `doc` / `documents` -> `nature-writing` -> `nature-polishing` | 先提取风格，再写作和润色 |

#### 处理流程

1. 判断参考论文格式：
   - PDF：使用 `pdf` skill 提取正文、标题、摘要、段落结构、图表说明和小标题。
   - Word/DOCX：使用 `doc` 或 `documents` skill 读取正文、标题层级、段落结构、图表说明和修订痕迹。
2. 生成“参考论文风格摘要”，包括：
   - 文章结构：Abstract、Introduction、Results、Discussion、Methods 等的组织方式。
   - 论证节奏：问题提出、证据排列、机制解释、局限性表达。
   - 句式特征：句长、被动/主动语态、连接词、谨慎表达方式。
   - 图表叙述方式：如何从图编号、趋势、统计结果过渡到机制解释。
   - 术语密度：专业术语、缩写、单位、定量表达的使用方式。
   - 段落模式：每段首句功能、证据句、解释句、总结句。
3. 将风格摘要传递给后续写作或润色 skill。
4. 写作时只模仿结构、语气、论证逻辑和学术表达习惯；禁止复制参考论文的句子、段落、数据、图表或未授权表达。
5. 如果用户同时上传自己的草稿和参考论文，优先保留用户草稿的科学内容，只迁移参考论文的写作风格。

#### 输出要求

在进入写作前，先给用户一个简短的“已提取风格摘要”，例如：

```text
参考论文风格摘要：
- 结构：Results 按现象 -> 定量证据 -> 机制解释组织。
- 语言：短句为主，机制判断使用 cautious verbs。
- 图表叙述：每段先引用图号，再描述趋势，最后解释环境或生物学意义。
```

---

### 阶段 3：论文写作与润色

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "写论文" "draft manuscript" "Nature风格" | `nature-writing` | Nature/CNS 风格草稿 |
| "润色" "polish" "improve English" | `nature-polishing` | 高水平学术英语润色 |
| "模仿这篇论文写" "按参考论文风格改" | 阶段 2.5 -> `nature-writing` / `nature-polishing` | 先读参考论文，再写作或润色 |
| "ML论文" "NeurIPS" "ICML" "conference paper" | `ml-paper-writing` | ML 顶会论文 |
| "去AI痕迹" "humanize" "降AI感" | `writing-anti-ai` | 去除 AI 写作痕迹 |
| "数据声明" "Data Availability" "FAIR" | `nature-data` | 数据可用性声明 |
| "先写再润色" "写+polish" | `nature-writing` -> `nature-polishing` | 写作 + 润色 |
| "中文草稿转英文" "中译英" | `nature-writing` -> `nature-polishing` | 中->英写作 |
| "写+润色+去AI" | `nature-writing` -> `nature-polishing` -> `writing-anti-ai` | 三阶段写作管道 |

---

### 阶段 4：图表与数据

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "画图" "matplotlib" "论文图表" "Nature图" | `nature-figure` | 发表级图表 |
| "数据分析" "统计" "results analysis" | `results-analysis` | 实验结果分析 |
| "数据规划" "repository" "数据库" | `nature-data` | 数据管理规划 |
| "画图+分析" | `results-analysis` -> `nature-figure` | 分析 + 可视化 |

---

### 阶段 5：论文自审与验证

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "自审" "check paper" "review quality" | `paper-self-review` | 论文质量清单 |
| "引用验证" "check citations" | `citation-verification` | 引用真实性 |
| "自审+引用验证" | `paper-self-review` -> `citation-verification` | 全面自审 |

---

### 阶段 6：审稿回复

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "回复审稿人" "rebuttal" "审稿意见" "逐点回复" | `nature-response` | Nature/CNS 风格回复 |
| "通用回复" "review response" | `review-response` | 通用审稿回复 |
| "从DOCX提取审稿意见并回复" | `paper-revised` | DOCX 提取 + 回复 |
| "回复+润色" | `nature-response` -> `nature-polishing` | 回复 + 润色 |

---

### 阶段 7：成果展示

| 用户需求关键词 | 调用的 Skill(s) | 说明 |
|---|---|---|
| "做PPT" "slides" "论文汇报" "组会" | `nature-paper2ppt` | 论文->PPT |
| "海报" "poster" "会议展示" | `post-acceptance` | 会议海报/展示 |
| "推广" "Twitter" "promotion" | `post-acceptance` | 论文推广内容 |

---

### 阶段 8：跨阶段组合工作流

| 用户需求 | 串联 Skill(s) | 说明 |
|---|---|---|
| "从构思到初稿" | `research-ideation` -> `nature-writing` -> `nature-polishing` | 全链路 |
| "文献检索+写Related Work" | `nature-academic-search` -> `nature-writing` | 检索 + 写作 |
| "读论文+做PPT汇报" | `nature-reader` -> `nature-paper2ppt` | 阅读 + 汇报 |
| "论文自审+回复审稿" | `paper-self-review` -> `nature-response` | 自审 + 回复 |
| "数据分析+画图+写结果" | `results-analysis` -> `nature-figure` -> `nature-writing` | 数据 -> 图表 -> 写作 |
| "上传参考论文+改写我的草稿" | `pdf`/`doc` -> `nature-polishing` | 参考论文风格提取 + 草稿润色 |
| "上传参考论文+从中文材料写英文稿" | `pdf`/`doc` -> `nature-writing` -> `nature-polishing` | 风格提取 + 写作 + 润色 |

---

## 决策逻辑

### 1. 识别目标期刊/风格

```text
Nature/CNS -> nature-* 系列 skill
ML 顶会 -> ml-paper-writing
用户上传参考论文 -> 先执行阶段 2.5 的风格提取，再进入写作/润色
未指定 -> 默认使用通用高水平学术写作策略，并明确告知用户
```

### 2. 识别核心操作

```text
写/润色/改 -> writing/polishing 类 skill
模仿论文风格 -> pdf/doc/documents + writing/polishing 类 skill
画图 -> nature-figure
找文献/引用 -> search/citation 类 skill
读/翻译 -> reader 类 skill
回复审稿 -> response 类 skill
```

### 3. 识别是否需要串联

```text
单步操作 -> 1 个 skill
顺序依赖（如写+润色）-> 2 个 skill 串联
复杂多阶段 -> 最多 3 个 skill 串联
参考论文模仿 -> 参考论文读取/风格摘要算作前置步骤，后续仍尽量控制在 1-2 个执行 skill
```

### 4. 处理模糊请求

如果请求不够清晰（如“帮我看看这篇论文”），先追问澄清：

- “你是想阅读/翻译这篇论文，还是想审稿/自审？”
- “你想模仿这篇论文的整体结构、语言风格，还是某个部分（Abstract/Results/Discussion）？”
- “你已经有草稿了，还是需要从中文材料开始写？”
- “目标期刊或目标写作风格是什么？”

---

## 可用 Skill 完整清单

### 写作类

| Skill | 适用场景 |
|---|---|
| `nature-writing` | Nature/CNS 或高水平学术论文草稿/重构 |
| `nature-polishing` | 学术英语润色 |
| `ml-paper-writing` | ML 顶会论文（NeurIPS/ICML/ICLR） |
| `writing-anti-ai` | 去除 AI 写作痕迹 |

### 图表与数据类

| Skill | 适用场景 |
|---|---|
| `nature-figure` | 发表级 matplotlib 图表 |
| `nature-data` | 数据声明/FAIR 元数据 |
| `results-analysis` | 实验结果统计分析 |

### 文献检索类

| Skill | 适用场景 |
|---|---|
| `nature-academic-search` | 多源文献检索（PubMed/CrossRef/arXiv） |
| `nature-citation` | Nature/CNS 引用查找 |
| `gs-search` | Google Scholar 搜索 |
| `gs-advanced-search` | Google Scholar 高级搜索 |
| `gs-cited-by` | 引文追踪 |
| `gs-fulltext` | 全文获取 |
| `gs-export` | Zotero 导出 |
| `web-of-science-research` | WoS 检索 |
| `citation-verification` | 引用验证 |
| `reference-screen-by-cas-quartile` | CAS 分区筛选 |

### 阅读与审稿类

| Skill | 适用场景 |
|---|---|
| `pdf` | PDF 读取、提取和渲染检查 |
| `doc` / `documents` | Word/DOCX 读取、编辑和排版检查 |
| `nature-reader` | 全文双语阅读 |
| `nature-response` | Nature/CNS 风格审稿回复 |
| `review-response` | 通用审稿回复 |
| `paper-revised` | DOCX 审稿提取 + 回复 |
| `paper-self-review` | 论文质量自审 |

### 展示与追踪类

| Skill | 适用场景 |
|---|---|
| `nature-paper2ppt` | 论文->PPT |
| `post-acceptance` | 会议海报/推广 |
| `daily-paper-generator` | 每日论文推送 |
| `research-ideation` | 研究构思/空白分析 |

---

## 使用示例

### 示例 1：简单单步

```text
用户: "帮我把这段中文摘要润色成Nature风格英文"
调度: nature-polishing x 1
```

### 示例 2：两步串联

```text
用户: "帮我找几篇光催化降解的Nature Communications文献，然后按中科院分区筛选一下"
调度: nature-academic-search -> reference-screen-by-cas-quartile x 2
```

### 示例 3：三阶段管道

```text
用户: "我有一段中文结果描述，帮我写成Nature风格英文Results，然后润色，最后画图"
调度: nature-writing -> nature-polishing -> nature-figure x 3
```

### 示例 4：参考论文模仿

```text
用户: "我上传一篇PDF，请模仿它的Discussion风格改写我的讨论部分"
调度: pdf -> nature-polishing
先输出参考论文风格摘要，再改写用户草稿。
```

### 示例 5：模糊请求

```text
用户: "帮我看看这篇论文"
处理: 追问澄清，是想阅读翻译、审稿、自审，还是提取风格用于仿写。
```

---

## 质量守则

1. **最小调用原则**：能 1 个 skill 解决的，不调用 2 个。
2. **明确性优先**：请求模糊时先追问，不做猜测性调度。
3. **顺序保证**：串联调用时严格遵守逻辑顺序（读参考论文 -> 提取风格 -> 写作/润色 -> 去AI）。
4. **上下文传递**：串联调用时，前一个 skill 的输出作为后一个 skill 的输入。
5. **失败处理**：若某子 skill 不适用于当前场景，告知用户并建议替代方案。
6. **版权边界**：模仿参考论文时只迁移结构、语气和论证方式，不复制原文表达。
7. **风格透明**：使用参考论文风格前，先向用户简述已提取的风格特征。
8. **不做假设**：不确定目标期刊/风格时，默认采用通用高水平学术写作策略并说明。
