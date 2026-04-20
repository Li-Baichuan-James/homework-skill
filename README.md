# Homework Solver Skill

<div align="center">

## 作业.SKILL

---

"你以为你累得像条狗，你错了，狗并没有你这么累。
——鲁迅"



> 作业，我有四不写。  
> 会的我不写——落笔为提升，已然通晓，何需多费笔墨？  
> 不会的我不写——懵懂无解，强求落笔，不过是自欺欺人。  
> 要交的我不写——提笔为修行，非为取悦他人，何必迎合敷衍？  
> 不交的我不写——墨落皆心血，若无人问津，落笔便失了意义。
>  
>
> 

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://claude.ai/code)
[![AgentSkills](https://img.shields.io/badge/AgentSkills-Standard-green)](https://agentskills.io)
[![Bilingual](https://img.shields.io/badge/README-中文%20%7C%20English-4c9a2a)](#中文)

[中文](#中文) | [English](#english)

</div>

---

## 中文

当代大学生的作业困局，大抵是复制粘贴问AI，深度思考等答案，彻底沦为的“AI搬运工”，在无意义的循环里自我内耗，徒留一身疲惫与空洞。

作业.SKILL 横空出世✨ 予你一支 AI 专家天团，将写作业的繁琐流程尽数自动化：轻传一份作业 PDF，低语一句“帮我完成这份作业”，余下所有，皆有它从容代劳。

平行子 Agent 各司其职、分工解题，每题皆有独立 Agent 严谨验算，终有组装 Agent 凝智成篇，直接给你 PDF 格式答案，全程丝滑流畅、精准无误，恰如写作业本就该有的模样。

以科技破局“古法写作业”的困境，作业.SKILL 愿你不被琐事裹挟，往后作业无忧，步履皆欢。

作者亲力亲为，在完成 114514 份作业的疲惫征程中幡然醒悟，耗尽心力打磨出这份大学生懒人福音——作业.SKILL。
当他人仍在案前埋首，与作业苦苦纠缠、步履维艰时，你早已弹指间秒完任务，煮一壶清茶，赴一场悠闲下午茶之约，尽显降维打击的从容。

### 快速开始

别再古法复制粘贴/截图问网页版AI，现在一句话就能让CLI Agent帮你完成作业：

1. 安装 skill（把整个 `homework-solver/` 目录放到你的 skills 路径）
2. 在会话里直接说你要完成作业即可（不必显式提到这个 skill 名称）。比如：

```text
帮我完成这份作业。
```

3. 若你只做部分题，也直接自然描述需求即可。例如：

```text
只帮我做第 2 题和第 4 题，并生成答案文件。
```

4. 可以指定答案的语言/详略风格 （默认为英语+详细风格）。例如：

```text
使用中文/英文/中英双语作答。 使用考场式简练风格/讲解式详细风格。
```

### 用户侧流程（你会看到什么）

1. 主 Agent 先读 PDF，不会直接“闷头开算”
2. 若题面不清楚，会先问，不会硬猜
3. 每题先解再验，没通过验算的题不会进入最终文档
4. 最后输出文件并报告验证结果

### 核心特性

优雅摆烂：

- **题级核验**：每个题组都必须经过 solver phase 和 verifier phase
- **严格顺序**：读题 -> 分题 -> 解题 -> 验题 -> 组装 -> 构建 -> 最终验收
- **可移植**：不强依赖其他自定义 skills；没有 subagent 也能顺序执行
- **防“假完成”**：二次核验，独立验收

### 安装与兼容性说明

平台条件不完美也能跑，别总拿环境当借口：

- 若你希望 skill 默认产出可编译的 `.pdf`，本机需要可用的 LaTeX 环境。常见可用组合包括：`pdflatex`、`xelatex`、`latexmk`，Windows 上通常是 MiKTeX，macOS/Linux 上通常是 TeX Live。
- 若缺少可用 TeX 引擎，skill 仍可生成 `.tex`，但应把 `.pdf` 视为 blocked artifact outcome，而不是假装已经完整完成。
- 如果平台有独立 PDF skill，会优先使用
- 如果没有，就用平台原生 PDF 读取能力
- 如果平台支持 subagent，按 solver/verifier 分角色执行
- 如果不支持，也必须执行“先解后验”的两阶段流程

### 与 `pdf` skill 的配合

- `homework-solver` 默认把 `pdf` 视为唯一配合使用的 companion skill。
- `pdf` skill 负责 PDF 读取、提取、OCR、页面内容确认等输入侧工作。
- `homework-solver` 负责题目拆分、解题、画图、逐题验算、组装答案文档、构建 `.tex/.pdf`、以及最终交付验证。
- 换句话说：`pdf` 先把题读清楚，`homework-solver` 再把整份作业做完。

---

## English

`homework-solver` is a workflow-driven skill for engineering and mathematics homework PDFs.
It enforces a strict controller workflow: parse the assignment, solve one question group, verify that same group with a fresh verifier, pass the question gate, then assemble and validate deliverables.

### Intended Use

- Solve a full homework PDF
- Solve selected questions only (for example, Q2 and Q4)
- Produce submission-ready solution artifacts (typically `.tex` and `.pdf`)
- Include required figures, pseudocode, and structured tables when requested

### Design Principles

- **Question-level gates**: each included question group must pass both solver and verifier phases
- **Per-group sequencing**: do not solve all questions first and verify later in a batch
- **Deterministic workflow**: read -> decompose -> solve -> verify -> gate -> normalize -> assemble -> build -> final verification
- **Strict skill boundary**: only `pdf` may be used alongside this skill
- **Verification discipline**: successful compilation is not treated as proof correctness

### Quick Start

1. Install the skill by placing the `homework-solver/` folder in your platform's skills directory.
2. In your session, simply ask for the homework to be completed. You do not need to mention the skill name explicitly. For example:

```text
Help me finish this homework.
```

For partial scope, describe the subset naturally. For example:

```text
Please solve only Q2 and Q4 and generate the solution files.
```

### What Users Should Expect

1. The agent reads and extracts the PDF before solving.
2. Unreadable or ambiguous prompt content is escalated instead of guessed.
3. Each question group is solved and then independently verified by a fresh verifier before assembly.
4. If you ask to complete the homework and do not opt out, the default deliverable is a written answer document with `.tex` and compiled `.pdf` artifacts.
5. Final output includes artifacts plus explicit verification status.

### Portability Notes

- A working TeX environment is required if you want the skill to produce compiled `.pdf` output by default. Typical usable engines are `pdflatex`, `xelatex`, or `latexmk`; on Windows this is often MiKTeX, and on macOS/Linux it is often TeX Live.
- If no usable TeX engine is available, the skill can still deliver `.tex`, but the missing compiled `.pdf` should be reported as a blocked artifact outcome rather than full completion.
- `pdf` is the only companion skill this workflow should use.
- If a dedicated PDF skill exists, it should be used; otherwise native PDF tooling is sufficient.
- If subagents are available, solver and verifier roles are split and must remain separate for each question group.
- If subagents are unavailable, the same two-phase discipline is executed sequentially.

### How It Cooperates With `pdf`

- `pdf` handles PDF-side work such as extraction, OCR, and recovering readable question text.
- `homework-solver` then handles decomposition, solving, required figures, verification, answer-sheet assembly, and artifact validation.
- In short: `pdf` helps the agent read the assignment correctly, and `homework-solver` governs how the solved deliverable is produced.

### Repository Contents

- `SKILL.md`: canonical skill specification and workflow gates
- `README.md`: publication-facing overview and usage guide

### License

MIT License
