---
name: homework-solver
description: Use when given an engineering or mathematics homework PDF and asked to solve all or selected questions, answer sketch or plot problems, redraw assignment figures, or work from noisy OCR-extracted homework text.
---

# Homework Solver

Turn a homework PDF into a solved answer document with verified mathematics and verified output artifacts.

**Core principle:** treat homework completion as a gated controller workflow: read first, solve one question group, produce any required figures for that group, verify that same group with a fresh verifier, pass the question gate, then assemble, build, and verify the final artifacts.

Default mode unless the user says otherwise:

- language: English
- style: detailed
- output: `.tex` and compiled `.pdf`
- subset: all questions

## When to Use

Use this skill when the user wants any of the following from a homework PDF:

- full worked solutions
- selected-question solutions
- a LaTeX answer sheet
- a compiled PDF answer sheet
- clean redraws of assignment figures

Do not use this skill for essays or research-writing tasks where outside sources are the main work.

## Non-Negotiables

- Read the PDF before solving.
- If text, formulas, or figures are unreadable, stop and ask. Never guess missing prompt content.
- If the user says to finish or complete the homework, treat that as a deliverable request, not chat-only help.
- Unless the user explicitly opts out, the default deliverable is a written answer document plus files: `.tex` and compiled `.pdf`.
- Unless the user explicitly opts out, any question that asks for a sketch, plot, graph, waveform, phasor diagram, Bode sketch, Bode plot, root-locus plot, Nyquist plot, circuit redraw, geometry figure, free-body diagram, timing diagram, state diagram, or any other visual result must include that visual in the deliverable. Words alone are not sufficient.
- Do not wait for the user to separately remind you to draw figures that the assignment already asks for.
- Do not stop at a chat-only answer when file generation is still expected by default.
- Use deterministic filenames.
- If multiple questions are requested, decompose by question or tightly coupled question group.
- Every included question group must go through a distinct solver phase and a distinct verifier phase. If subagents exist, use a fresh `solver` subagent and a separate fresh `verifier` subagent.
- With subagents available, each included question group must follow this exact sequence before moving on: `dispatch solver -> collect solver output -> produce required figures for that group -> dispatch fresh verifier for the same group -> resolve verifier notes -> pass question gate`.
- Do not solve all groups first and verify them later in a batch. Per-group solve-then-verify is required.
- Do not assemble any question until it has passed the question gate.
- Do not treat successful compilation as mathematical verification.
- Do not claim completion before both per-question verification and final artifact verification are done.

## Portability

This skill is designed to work as a standalone GitHub skill.

- Do not assume any other custom skills are installed.
- The only companion skill this skill may invoke is `pdf`.
- Do not invoke any other skill while following this workflow.
- If a separate `pdf` skill exists, use it. Otherwise, use the platform's native PDF-reading tools.
- If subagents are available, use the solver/verifier split described here. If subagents are not available, the main agent must still run a distinct solver phase and a distinct verifier phase before assembly.

## Workflow Model

This skill uses a strict separation-of-roles workflow:

- the `controller` owns intake, decomposition, dispatch, gating, normalization, build choice, and final reporting
- one `solver` handles one question group
- one fresh `verifier` independently checks that same question group
- the `assembler` role writes the final document using only approved question groups
- final verification happens only after build artifacts exist

If your platform does not support subagents, emulate the same workflow sequentially:

1. solve one question group
2. produce any required figures for that group
3. perform a separate verification pass using the verifier checklist
4. only then allow that question into the final document

The controller is never allowed to treat solver output as already trusted work.

## Execution Flow

```dot
digraph homework_solver_flow {
    "Intake" [shape=box];
    "Read PDF" [shape=box];
    "Question decomposition" [shape=box];
    "Dispatch solver for one group" [shape=box];
    "Produce required figures for same group" [shape=box];
    "Dispatch fresh verifier for same group" [shape=box];
    "Question gate" [shape=box];
    "All groups verified?" [shape=diamond];
    "Normalize notation and style" [shape=box];
    "Assemble document" [shape=box];
    "Build artifacts" [shape=box];
    "Final artifact verification" [shape=box];
    "Report result" [shape=doublecircle];

    "Intake" -> "Read PDF";
    "Read PDF" -> "Question decomposition";
    "Question decomposition" -> "Dispatch solver for one group";
    "Dispatch solver for one group" -> "Produce required figures for same group";
    "Produce required figures for same group" -> "Dispatch fresh verifier for same group";
    "Dispatch fresh verifier for same group" -> "Question gate";
    "Question gate" -> "All groups verified?";
    "All groups verified?" -> "Dispatch solver for one group" [label="no, next group or fix and re-verify"];
    "All groups verified?" -> "Normalize notation and style" [label="yes"];
    "Normalize notation and style" -> "Assemble document";
    "Assemble document" -> "Build artifacts";
    "Build artifacts" -> "Final artifact verification";
    "Final artifact verification" -> "Report result";
}
```

## Step 1. Intake

- Confirm the source PDF path if unclear.
- Determine full assignment vs subset.
- Determine requested output: `.tex`, `.pdf`, or both.
- Determine language and style. If unspecified, use defaults.
- Identify every question that requires a drawn figure, sketch, or plot. Treat explicit verbs like `sketch`, `plot`, `draw`, `graph`, and `redraw` as mandatory visual deliverables, not optional presentation choices.

After intake and PDF read, announce the concrete deliverable plan.

If the user asked to complete the homework and did not narrow the format, proceed toward `.tex` and `.pdf` generation without waiting for a second prompt.

## Step 2. Read the Assignment

- Extract the question text before solving.
- If the PDF contains garbled or partial text, stop and ask.
- Minor OCR noise may be summarized only when the mathematical meaning is still fully clear. If any symbol, number, condition, label, or figure detail is missing or ambiguous in a way that could affect the solution, stop and ask instead of summarizing.
- Record any required metadata such as student name, ID, email, course title, due date, or required formatting notes.

## Step 3. Decompose the Work

- Split independent questions into separate question groups.
- Keep tightly coupled multi-part questions together.
- The controller owns consistency across notation, assumptions, and presentation.

## Step 3A. Visual Deliverables

Treat visual outputs as first-class deliverables, not optional polish.

- For every included question group, explicitly decide whether the prompt requires a visual artifact.
- Required visual artifacts include any requested sketch, plot, graph, waveform, phasor diagram, Bode sketch, Bode plot, root-locus plot, Nyquist plot, circuit redraw, geometry figure, free-body diagram, timing diagram, state diagram, or comparable visual result.
- If the question asks for a visual artifact, the controller must record that requirement immediately and carry it through solver, verifier, gate, assembly, and final verification.
- A prose description of a required figure does not satisfy the requirement.
- If the assignment asks the student to sketch something, the default behavior is to include the sketch in the answer document unless the user explicitly opts out.
- Visual-deliverable status must be known before the question group can pass the gate.

## Step 4. Solver Phase

If subagents are available, dispatch one fresh solver subagent per question group.

If subagents are not available, the controller performs this phase directly but must still produce the same structured output for later verification.

Use the `Solver Contract` below as the required output format.

## Step 4A. Subagent Contracts

When subagents are available, the controller must keep prompts narrow, role-specific, and self-contained.

Do not make a subagent infer its role from surrounding conversation. State the role, scope, required output format, and forbidden actions directly in the prompt.

### Solver Contract

Give each `solver` subagent only the context needed to solve one question group.

The controller must provide:

- question ids covered
- exact extracted prompt text whenever available
- if OCR noise is minor and does not change the mathematical meaning, an additional faithful prompt summary may be provided as a convenience, but it must not replace the extracted text
- any required notation or assumptions already fixed by the controller
- the required solver return format below
- explicit constraints: solve only; do not verify; do not assemble; do not claim completion; do not answer questions outside the assigned group

The `solver` must return exactly these sections:

- `question ids covered`
- `extracted prompt summary`
- `assumptions or theorems used`
- `derivation or proof outline`
- `final answers`
- `figure requirements`
- `notation introduced`
- `uncertainty` or `needs manual review`

The `solver` is responsible for solving, not for granting trust.

If the prompt for that group asks for any visual result, `figure requirements` must say exactly what figure will be produced in the final deliverable. It must never say `none` for a sketch/plot/draw/redraw question.

Use section labels exactly as written above. Do not merge sections, rename them, or replace them with free-form prose.

If the solver omits a required section, adds assembly work, or blends verification into the answer, the controller must reject that output and re-dispatch.

### Verifier Contract

Give each `verifier` subagent only the context needed to verify the same question group.

The controller must provide:

- the same question ids
- the original extracted prompt text whenever available
- if OCR noise is minor and does not change the mathematical meaning, an additional faithful prompt summary may be provided, but it must not replace the extracted text
- the full solver output for that question group
- any produced figure artifacts for that question group, or an explicit statement that no figure is required
- the verifier checklist from this skill
- the required verifier return format below
- explicit constraints: verify only; do not re-solve the whole assignment; do not assemble; do not claim completion; do not ignore missing sections in the solver output

The `verifier` must return exactly these sections:

- `verdict` with one of: `APPROVED`, `APPROVED_WITH_NOTES`, `REJECTED`
- `findings by question group`
- `corrected result if needed`
- `residual uncertainty if any`

The `verifier` is responsible for independent checking, not for assembly or final reporting.

Use section labels exactly as written above. Do not collapse them into a narrative summary.

Inside `findings by question group`, cover the verifier checklist in this order:

- question interpretation
- derivation or proof correctness
- numerical or symbolic result correctness
- recurrence correctness, if applicable
- pseudocode consistency with the described algorithm, if applicable
- agreement between solver working and final answer
- agreement between any required figure and the stated conclusion
- missing cases, hidden assumptions, or unjustified steps

If the verifier returns a verdict without findings, changes scope, or silently approves malformed solver output, the controller must reject that output and re-dispatch.

## Step 4B. Verdict Handling

Treat verifier verdicts as workflow states, not suggestions.

**APPROVED:**

- the group may pass the question gate only after all Step 6 conditions are satisfied
- `corrected result if needed` should be empty or explicitly say none
- proceed to normalization only after the gate is satisfied

**APPROVED_WITH_NOTES:**

- the group does not pass the gate until the controller resolves the notes
- incorporate the notes into the answer or explicitly preserve the residual uncertainty
- if the notes imply a real mathematical error, send the group back through fix-and-re-verify instead of treating it as done

**REJECTED:**

- the group must not enter assembly
- send the group back through fix-and-re-verify
- do not downgrade `REJECTED` to a note just to keep moving

### Controller Rules

The controller must treat subagent outputs as typed workflow artifacts, not casual notes.

- Do not paraphrase away missing sections.
- Do not let the verifier fix formatting gaps that belong to the solver.
- Do not let the assembler consume subagent output that failed the contract.
- If a subagent returns extra material outside its role, ignore it unless the controller explicitly re-dispatches for that purpose.
- Do not reuse one solver subagent across multiple question groups; each group needs a fresh solver context.

## Step 4C. Produce Required Figures

After a question group's mathematics has been solved, produce any required figures for that same group before it reaches the verifier or the question gate.

- If the question group has no required figure, record that explicitly.
- If the question group requires a figure, create the figure artifact at this stage rather than deferring figure creation until whole-document assembly.
- The produced figure must match the solved mathematics for that group and be ready for verifier review.
- If figure creation reveals a mathematical inconsistency, fix the group and re-run the figure production step before verification.
- The controller must know, per group, whether the figure artifact is complete and ready for assembly.

## Step 5. Verifier Phase

For every solved question group, run a separate verifier phase after any required figures for that group have been produced.

If subagents are available, dispatch a separate fresh verifier subagent.

If subagents are not available, the controller must perform an explicit second-pass verification against the checklist below before assembly.
In that fallback mode, the controller must still write down a verifier artifact using the same four sections required by the Verifier Contract: `verdict`, `findings by question group`, `corrected result if needed`, and `residual uncertainty if any`.
When extracted text is noisy, the controller should preserve the raw extraction alongside any summary so the verifier can challenge the controller's interpretation instead of inheriting it silently.

The verifier must check:

- question interpretation
- proof or derivation correctness
- numerical or symbolic results
- recurrence correctness, if applicable
- pseudocode consistency with the described algorithm, if applicable
- agreement between solver working and final answer
- agreement between any required figure and the stated conclusion
- missing cases, hidden assumptions, or unjustified steps

The verifier must return one of the verdicts defined in `Verdict Handling` above.

If the verifier returns `REJECTED`, do not assemble that question. Send it back through a fix-and-re-verify loop.

If the verifier returns `APPROVED_WITH_NOTES`, the controller must either:

- incorporate the notes into the answer and figure before assembly, or
- explicitly preserve the residual uncertainty in the final document

Do not silently ignore verifier notes.

The verifier phase must be distinct from the solver phase. A quick reread of the same draft is not enough; use the checklist deliberately and record the result.

The controller must resolve verifier output for one group before dispatching assembly work that depends on that group.

## Step 6. Question Gate

No question may enter the final document until all of the following are true:

- the solver output exists
- the verifier output exists
- the current verifier verdict is `APPROVED`, or `APPROVED_WITH_NOTES` with all notes already resolved for this group
- any verifier notes have been incorporated or explicitly preserved
- notation and assumptions are understood by the controller
- visual-deliverable status is explicit for that question group
- every required figure for that question group has already been produced in a form ready for assembly; a queued specification alone is not enough for the group to pass the gate
- any residual uncertainty is explicit

Skipping this gate is a workflow violation.

## Step 7. Normalize Before Assembly

Before writing LaTeX, normalize across all approved question groups:

- notation and symbols
- theorem names and assumptions
- answer style and level of detail
- figure references and captions
- uncertainty markers
- pseudocode formatting

The main agent is responsible for this step. Do not make assembly a blind concatenation of subagent outputs.

## Step 8. Assemble the Document

- Put all requested questions into one answer document unless the user asks for separate files.
- Include the required header fields on page 1 if the assignment asks for them.
- Show enough derivation to be submit-ready.
- Prefer compact prose around formulas.
- For any question that requests a sketch, plot, graph, or redraw, include the actual figure in the assembled document by default. Do not substitute a prose-only description.
- Use TikZ, pgfplots, circuitikz, or another clean reproducible method for required figures.
- Do not let the assembler invent, repair, or silently reinterpret mathematics. Assembly is for approved content only.

## Output Naming Rule

Use:

`<pdf-stem><subset-if-any>_solution<language-if-nondefault><style-if-nondefault>.<ext>`

Rules:

- subset format: `_q2_q5`
- nondefault language: `_cn` or `_bilingual`
- nondefault style: `_exam`
- default English detailed mode adds no suffix

Examples:

- `hw2.pdf` -> `hw2_solution.tex`, `hw2_solution.pdf`
- `hw2.pdf` with Q4 and Q5 -> `hw2_q4_q5_solution.tex`
- `hw2.pdf` bilingual exam brief -> `hw2_solution_bilingual_exam.tex`

Fallback only if the PDF stem is unavailable:

- `assignment_solution.tex`
- `assignment_solution.pdf`

## Build Workflow

### 1. Detect local environment first

- check for `latexmk`, `pdflatex`, and `xelatex`
- prefer an existing working engine over installation
- on Windows, assume MiKTeX may exist even if `latexmk` is unhealthy

### 2. Choose the cheapest working path

Preferred order:

1. existing direct engine with current document
2. simplified document plus existing direct engine
3. existing `latexmk` if healthy
4. installation or configuration only if no working engine exists

### 3. Build recovery for constrained environments

If the first build fails:

- if `latexmk` is blocked by MiKTeX state or update warnings, switch to direct `pdflatex -interaction=nonstopmode -halt-on-error <file.tex>`
- if the document genuinely needs broader Unicode or font support and `xelatex` is available, use direct `xelatex` instead of forcing extra packages into a `pdflatex` path
- if optional packages are missing, simplify the preamble first
- for plain homework answer sheets, a minimal preamble is usually enough: `article`, `geometry`, `amsmath`, `amssymb`
- distinguish warnings from fatal errors; a MiKTeX update reminder is not itself a failed build if the PDF was produced
- if noninteractive package installation blocks progress, simplify the document or report the exact missing package

### 4. Escalation Rule

- If no working TeX engine exists and setup requires intrusive machine changes, stop and report the blocker clearly.
- Still deliver the `.tex` file if `.pdf` compilation is blocked.

### 5. Blocked Artifact Outcome

If the mathematics is verified and the `.tex` file is complete, but `.pdf` generation is blocked by environment or toolchain issues, report the result as a blocked artifact outcome rather than full completion.

- State clearly that the mathematical work is verified but the requested `.pdf` artifact could not be produced.
- Deliver the `.tex` file and any intermediate build logs or blocker details needed for the user to continue.
- Do not claim the assignment is fully complete when the requested `.pdf` is still missing.
- Do claim partial completion when the answer document content is complete and the remaining blocker is purely artifact generation.

## Final Artifact Verification

Do this only after the output files exist.

If `.pdf` generation was requested but blocked, do not run this checklist as though the final artifact set were complete. Instead, report the blocked artifact outcome from the Build Workflow and verify only the artifacts that do exist.
In a blocked artifact outcome, treat figure verification as follows: required figures must still exist in the available artifacts, and their source or intermediate form must be inspectable enough for the controller to confirm that each required figure is present and corresponds to the solved question group, even if final PDF readability could not be confirmed.

Check this exact list:

- requested questions are present
- every included question passed the question gate
- requested language and style were applied
- notation is consistent across questions
- visual-deliverable status was determined for every included question group
- required figures are included and readable
- every sketch/plot/draw/redraw question has its corresponding figure in the output files
- filenames follow the naming rule
- `.tex` exists
- `.pdf` exists if requested
- build completed without fatal errors
- any remaining uncertainty is explicitly marked

## Quick Reference

| Situation | Action |
|---|---|
| Full assignment PDF | Read, decompose, solve, produce required figures, verify, gate, assemble, build, verify |
| Question says sketch/plot/draw/redraw | Produce the corresponding figure by default; do not wait for a second user request |
| Selected questions only | Keep subset in one document and encode it in filename |
| Many independent questions | Keep one-group-at-a-time `solve -> produce required figures -> verify -> gate` sequencing; do not batch-solve or batch-verify |
| Unreadable PDF text or figure | Stop and ask |
| No custom PDF skill installed | Use native PDF extraction tools and continue |
| No subagent support | Run solver phase, then a separate verifier phase manually, and still write the verifier artifact in contract form |
| Solver finished but no verifier yet | Do not assemble that question |
| Verifier rejects a question | Fix and re-verify before proceeding |
| Compilation succeeded | Continue to artifact verification; do not infer math correctness |
| No working TeX engine or blocked PDF build | Report a blocked artifact outcome and still deliver `.tex` |

## Red Flags

- solving before reading the PDF
- treating the user request as chat-only when they asked to finish the homework
- treating `.tex` and `.pdf` generation as optional when the user asked to complete the homework and did not opt out
- parallelizing tightly coupled parts that need shared reasoning
- using a solver phase without a separate verifier phase
- solving all question groups first and verifying them later in a batch
- reusing one solver subagent across multiple question groups
- letting the same subagent both solve and verify the same question group
- skipping verification because the platform has no subagent feature
- assembling a document from unverified question outputs
- assuming compilation success means the answers are correct
- treating figure-producing questions as satisfied by prose-only descriptions
- accepting inconsistent notation from subagents without normalization
- invoking unrelated skills during the homework workflow
- claiming completion after build verification but before question-level verification

## Common Mistakes

| Mistake | Fix |
|---|---|
| Solver output is copied directly into LaTeX | Produce required figures, run verifier review on the solved work and figures, then normalize before assembly |
| One question is verified but others are not | Keep per-question gates; verify every included question |
| All questions are solved first, then verified later | Enforce `solve -> produce required figures -> verify -> gate` for each group before moving on |
| Build success is reported as overall success | Separate mathematical verification from artifact verification |
| Minor OCR damage is treated as license to guess missing math | Summarize only when meaning is fully clear; otherwise stop and ask |
| Figures are mentioned but not drawn | Create the figure during Step 4C before verifier review and before the question gate; sketch/plot/draw/redraw questions always require the figure by default |
| Manual fallback says verification happened but leaves no verifier artifact | Write the same verifier sections even without subagents |
| Different questions use conflicting notation | Normalize centrally before assembly |
| Other installed skills get pulled in automatically | Restrict companion skills to `pdf` only |

## Invocation Pattern

When the user gives a homework PDF and wants a solved deliverable, use this skill first. Only `pdf` may be used alongside it. Follow the strict order: read the PDF, decompose the work, run `solve -> produce required figures -> verify -> gate` for one group at a time, normalize, assemble, build, then perform final artifact verification or report a blocked artifact outcome.

If you skip a gate, you are not following this skill.
