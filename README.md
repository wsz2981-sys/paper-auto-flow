# paper-auto-flow

**Chinese-first academic paper workflow skill for literature search, manuscript writing, polishing, reviewer response, and Word/PDF reference-paper style imitation.**

`paper-auto-flow` is a workflow-orchestration skill designed for academic writing and research support. It helps an AI assistant understand a user's paper-related request, select only the most relevant sub-skills, and run a compact workflow across literature search, paper reading, manuscript drafting, polishing, figure preparation, citation checking, reviewer response, and presentation.

The key customization is a **reference-paper style imitation module**: users can upload a Word/DOCX or PDF paper, and the skill extracts transferable writing features such as structure, argument rhythm, cautious wording, paragraph pattern, and figure-description style. The extracted style is then used to guide later writing or polishing without copying original text.

## Why This Project Matters

Academic writing tasks are rarely isolated. A single request may involve reading a target paper, extracting writing style, rewriting a Results section, polishing English, checking citations, and preparing figures. Running every tool at once wastes context and increases error risk.

`paper-auto-flow` solves this by acting as a lightweight router:

- It interprets the user's natural-language academic request.
- It selects a small workflow, usually 1-3 sub-skills.
- It preserves task order, such as reading a reference paper before style-guided rewriting.
- It keeps the writing process transparent by summarizing the reference-paper style before applying it.

## Core Capabilities

| Area | What It Supports |
|---|---|
| Literature search | Google Scholar, Web of Science, DOI/citation checking, CAS quartile filtering |
| Paper reading | PDF reading, Word/DOCX reading, bilingual reading, full-text extraction |
| Style imitation | Reference-paper upload, structure extraction, paragraph pattern extraction, figure-caption and result-description style extraction |
| Manuscript writing | Abstract, Introduction, Results, Discussion, Methods, conclusions, Chinese-to-English academic rewriting |
| Polishing | High-level academic English polishing, logic tightening, tone control, AI-writing trace reduction |
| Figures and data | Figure planning, Nature-style plotting workflow, data availability and FAIR statement support |
| Review workflow | Manuscript self-review, reviewer-comment extraction, point-by-point response drafting |
| Presentation | Paper-to-slides workflow, group meeting or interview presentation support |

## Workflow Logic

```text
User request
   |
   v
Intent recognition
   |
   v
Select 1-3 relevant sub-skills
   |
   v
Run in logical order
   |
   v
Return writing, analysis, review, or presentation output
```

For reference-paper imitation:

```text
Upload Word/PDF reference paper
   |
   v
Extract structure, tone, argument rhythm, paragraph pattern, figure-description style
   |
   v
Summarize transferable writing features
   |
   v
Apply style to the user's own manuscript or draft
```

## What Was Customized

This skill was adapted from `paper-flow` and renamed to `paper-auto-flow`.

Main changes:

- Removed ES&T-specific writing and reference-screening modules.
- Added a dedicated Word/PDF reference-paper imitation module.
- Added explicit routing for "upload a reference paper, extract writing style, then rewrite/polish my draft".
- Added copyright-aware rules: imitate structure, scholarly tone, and argument logic, not original wording.
- Kept the original cross-stage academic workflow structure.

## Repository Structure

```text
paper-auto-flow/
└── SKILL.md
```

Only `SKILL.md` is required inside the actual skill folder.

## Installation

Clone this repository:

```powershell
git clone https://github.com/wsz2981-sys/paper-auto-flow.git
cd paper-auto-flow
```

Install for Claude-style local skills:

```powershell
Copy-Item -Recurse ".\paper-auto-flow" "$env:USERPROFILE\.claude\skills\paper-auto-flow" -Force
```

Install for Codex-style local skills, if applicable:

```powershell
Copy-Item -Recurse ".\paper-auto-flow" "$env:USERPROFILE\.codex\skills\paper-auto-flow" -Force
```

Restart the relevant AI assistant so it can reload local skills.

## Example Prompts

```text
请使用 paper-auto-flow，帮我把这段中文 Results 写成高水平英文论文表达。
```

```text
我上传一篇 PDF，请先提取它的 Discussion 写作风格，然后模仿这种结构改写我的讨论部分。
```

```text
请读取这个 Word 参考论文，提取摘要和结果部分的写法，再帮我润色我的摘要。
```

```text
请帮我从文献检索、论文结构设计、Results 写作和英文润色四个环节规划一条最小工作流。
```

## Recommended GitHub Topics

```text
academic-writing
ai-workflow
research-assistant
paper-writing
literature-review
manuscript-polishing
pdf-processing
docx-processing
chinese-academic-writing
prompt-engineering
```

## Share This Project

Project link:

```text
https://github.com/wsz2981-sys/paper-auto-flow
```

One-line English description:

```text
paper-auto-flow is a Chinese-first academic writing workflow skill that routes literature search, manuscript writing, polishing, reviewer response, and Word/PDF reference-paper style imitation into compact AI workflows.
```

One-line Chinese description:

```text
paper-auto-flow 是一个中文优先的学术论文工作流 Skill，可自动调度文献检索、论文写作、英文润色、审稿回复，并支持基于 Word/PDF 参考论文的写作风格提取与仿写。
```

Resume-ready version:

```text
Built paper-auto-flow, a Chinese-first academic paper workflow skill that orchestrates literature search, manuscript drafting, polishing, reviewer-response preparation, and Word/PDF reference-paper style imitation through compact AI-assisted workflows.
```

## Notes

This skill is designed for research assistance and academic workflow orchestration. It should imitate transferable academic structure and scholarly style, not copy protected content from reference papers.
