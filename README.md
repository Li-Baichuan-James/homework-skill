# Homework Solver Skill

<div align="center">

## 作业.SKILL

"把时间从重复劳动里抢回来，把挂科风险留给门禁系统。"

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://claude.ai/code)
[![AgentSkills](https://img.shields.io/badge/AgentSkills-Standard-green)](https://agentskills.io)
[![Bilingual](https://img.shields.io/badge/README-中文%20%7C%20English-4c9a2a)](#中文)

[中文](#中文) | [English](#english)

</div>

---

## 中文

> “作业，我有四不写。  
> 会的我不写：我写作业是为了提升自己，我都会了还写干嘛？  
> 不会的我不写：我都不会，为啥要写？  
> 要交的我不写：我写作业是为了提升自己，不是给你看的。  
> 不交的我不写：我写完作业你都不看一眼，那我写了干嘛？”

Agent 时代了，还一题一题复制粘贴去问网页版 AI？别当“古法 AI 匠人”了。

`homework-solver`（你也可以叫它“作业.SKILL”）就是把这套流程工程化：
丢一份作业 PDF，说一句“帮我完成这份作业”，然后按门禁流程推进。

并行 solver 分题解答，独立 verifier 逐题验算，最后由组装流程产出 LaTeX + PDF。
丝滑不代表糊弄，自动化不代表不验证。

把时间从重复粘贴和来回返工里省下来，留给真正的学习和真正的摸鱼。

作者亲测写完 114514 份作业后，身心俱疲终开窍，才打磨出这个懒人福音 skill。
当别人还在吭哧吭哧埋头赶作业时，作业已经被你露头就秒，主打一个降维打击。

### 适用场景

你到底什么时候该把这个 skill 拉出来干活：

- 完整解答一份作业 PDF
- 只解指定题目（例如 Q2、Q4）
- 生成提交用答案文档（通常是 `.tex` + `.pdf`）
- 题目要求画图、伪代码、表格时，输出可读且可提交的版本

### 核心特性

它不仅帮你优雅摆烂，而且帮你少死几次：

- **题级门禁**：每个题组都必须经过 solver phase 和 verifier phase
- **严格顺序**：读题 -> 分题 -> 解题 -> 验题 -> 归一化 -> 组装 -> 构建 -> 最终验收
- **可移植**：不强依赖其他自定义 skills；没有 subagent 也能顺序执行
- **防“假完成”**：编译成功不等于数学正确

### 快速开始

别再古法复制粘贴，一句话开工：

1. 安装 skill（把整个 `homework-solver/` 目录放到你的 skills 路径）
2. 在会话里直接说你要完成作业即可，不必显式提到这个 skill 名称。比如：

```text
帮我完成这份作业。
```

3. 若你只做部分题，也直接自然描述需求即可。例如：

```text
只帮我做第 2 题和第 4 题，并生成答案文件。
```

### 用户侧流程（你会看到什么）

你会看到它先读题、再验题，而不是先自信：

1. 代理先读 PDF，不会直接“开算”
2. 若题面不清楚，会先问，不会硬猜
3. 每题先解再验，没过 gate 的题不会进入最终文档
4. 最后输出文件并报告验证结果（而不是只贴一句“Done”）

### 安装与兼容性说明

平台条件不完美也能跑，别总拿环境当借口：

- 如果平台有独立 PDF skill，会优先使用
- 如果没有，就用平台原生 PDF 读取能力
- 如果平台支持 subagent，按 solver/verifier 分角色执行
- 如果不支持，也必须执行“先解后验”的两阶段流程

---

## English

`homework-solver` is a workflow-driven skill for engineering and mathematics homework PDFs.
It enforces a strict sequence: parse the assignment, solve by question group, verify independently, then assemble and validate deliverables.

### Intended Use

- Solve a full homework PDF
- Solve selected questions only (for example, Q2 and Q4)
- Produce submission-ready solution artifacts (typically `.tex` and `.pdf`)
- Include required figures, pseudocode, and structured tables when requested

### Design Principles

- **Question-level gates**: each included question group must pass both solver and verifier phases
- **Deterministic workflow**: read -> decompose -> solve -> verify -> normalize -> assemble -> build -> final verification
- **Portable operation**: does not require any specific companion skill to function
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
3. Each question group is solved and then independently verified before assembly.
4. Final output includes artifacts plus explicit verification status.

### Portability Notes

- If a dedicated PDF skill exists, it may be used; otherwise native PDF tooling is sufficient.
- If subagents are available, solver and verifier roles are split.
- If subagents are unavailable, the same two-phase discipline is executed sequentially.

### Repository Contents

- `SKILL.md`: canonical skill specification and workflow gates
- `README.md`: publication-facing overview and usage guide

### License

Use the same license as your skill repository. If you have no license yet, add one before public release.
