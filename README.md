# paper-auto-flow

`paper-auto-flow` is a Chinese-first academic paper workflow skill. It routes paper-related requests to a small, appropriate set of sub-skills across the research lifecycle, including literature search, paper reading, reference-paper style extraction, manuscript writing and polishing, figure design, self-review, reviewer response, and presentation.

## What This Skill Does

- Routes natural-language academic writing requests to 1-3 relevant sub-skills.
- Keeps the workflow lightweight and avoids activating every paper-related skill at once.
- Supports manuscript writing, polishing, literature search, citation checking, figure preparation, reviewer response, and presentation workflows.
- Adds a dedicated reference-paper imitation module.
- Allows users to upload a reference paper in Word/DOCX or PDF format.
- Extracts transferable writing features such as structure, argument rhythm, cautious wording, paragraph pattern, and figure-description style.
- Uses the extracted style summary to guide later writing or polishing.
- Avoids copying original sentences, paragraphs, data, figures, or copyrighted expression from reference papers.

## Key Customization

This version was adapted from `paper-flow` and renamed to `paper-auto-flow`.

Compared with the original version:

- ES&T-specific writing and screening modules were removed.
- A new module was added for reference-paper upload and style imitation.
- The workflow now explicitly supports reading Word/DOCX and PDF reference papers before writing or polishing.

## Repository Structure

```text
paper-auto-flow/
└── SKILL.md
```

Only `SKILL.md` is required inside the skill folder.

## Installation

Copy the `paper-auto-flow` folder into your local skills directory.

For Claude-style local skills:

```powershell
Copy-Item -Recurse ".\paper-auto-flow" "$env:USERPROFILE\.claude\skills\paper-auto-flow" -Force
```

For Codex-style local skills, if applicable:

```powershell
Copy-Item -Recurse ".\paper-auto-flow" "$env:USERPROFILE\.codex\skills\paper-auto-flow" -Force
```

Then restart the relevant AI coding or writing assistant so it can reload local skills.

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

## Notes

This skill is designed for research assistance and academic writing workflow orchestration. It should be used to imitate transferable writing structure and scholarly style, not to copy protected content from reference papers.

