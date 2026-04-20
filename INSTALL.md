# Installation Guide

This guide is the source of truth for installing and validating the `homework-solver` skill from scratch.

Repository:

- `https://github.com/Li-Baichuan-James/homework-skill`

Raw files for this skill:

- `https://raw.githubusercontent.com/Li-Baichuan-James/homework-skill/main/SKILL.md`
- `https://raw.githubusercontent.com/Li-Baichuan-James/homework-skill/main/README.md`
- `https://raw.githubusercontent.com/Li-Baichuan-James/homework-skill/main/LICENSE`

## For Humans

Copy and paste this prompt to your LLM agent:

```text
Install and validate the `homework-solver` skill by following this guide exactly:
https://raw.githubusercontent.com/Li-Baichuan-James/homework-skill/main/INSTALL.md
```

## For LLM Agents

Follow this order exactly.

1. Detect the host agent family.
Use one of these families: `OpenCode`, `Claude Code`, or `Codex/OpenAI Agents style setup`.

2. Resolve the default local skills directory for that family.
If you cannot determine the family, stop and report the blocker instead of guessing.

3. Create `<skills-dir>/homework-solver/`.

4. Download these three files into that folder with these exact filenames:
- `SKILL.md`
- `README.md`
- `LICENSE`

Do not rely on repo metadata like `.git/` to make the skill work.

5. Verify that `<skills-dir>/homework-solver/SKILL.md` exists directly inside the folder.

6. Check whether the companion `pdf` skill is available.
Treat it as available only if either:
- the platform exposes an installed or bundled skill list that includes `pdf`, or
- a local file exists at `<skills-dir>/pdf/SKILL.md`

7. If `pdf` is not available, report that the setup is incomplete for full `homework-solver` use and stop.
Do not guess an installation method from this guide.

8. Only if `pdf` is available, check the local LaTeX environment by running:
- `pdflatex --version`
- `xelatex --version`
- `latexmk --version`

9. If none of those LaTeX tools are available, report that compiled `.pdf` output will be blocked, although `.tex` output can still work.

10. Report the final result with:
- detected agent family
- resolved skills directory
- installed skill path
- file list in `<skills-dir>/homework-solver/`
- whether `pdf` is available
- LaTeX tool availability status

## Validation Standard

An installation counts as validated by this guide only if all of the following are true:

- `<skills-dir>/homework-solver/SKILL.md` exists
- `<skills-dir>/homework-solver/README.md` exists
- `<skills-dir>/homework-solver/LICENSE` exists
- companion `pdf` skill is available

LaTeX is recommended for full `.pdf` output, but not required for the skill itself to be installed. If LaTeX is missing, report a blocked `.pdf` build capability rather than a failed skill install.

## Notes

- This guide is intentionally generic. It does not prescribe platform-specific marketplace or plugin commands.
- Runtime native PDF capability does not count as a successful install/verification state for this guide. The `pdf` skill itself must be available.
