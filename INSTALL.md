# Installation Guide

This guide is the source of truth for installing and validating the `homework-solver` skill from scratch.
It is intentionally platform-generic, but it does define concrete default paths, concrete download sources, and concrete validation rules.

Repository:

- `https://github.com/Li-Baichuan-James/homework-skill`

Raw files for this skill:

- `https://raw.githubusercontent.com/Li-Baichuan-James/homework-skill/main/SKILL.md`
- `https://raw.githubusercontent.com/Li-Baichuan-James/homework-skill/main/README.md`
- `https://raw.githubusercontent.com/Li-Baichuan-James/homework-skill/main/LICENSE`

Companion `pdf` skill source:

- Repository path: `https://github.com/anthropics/skills/tree/main/skills/pdf`
- Minimum raw files:
  - `https://raw.githubusercontent.com/anthropics/skills/main/skills/pdf/SKILL.md`
  - `https://raw.githubusercontent.com/anthropics/skills/main/skills/pdf/LICENSE.txt`
- Recommended extra files:
  - `https://raw.githubusercontent.com/anthropics/skills/main/skills/pdf/forms.md`
  - `https://raw.githubusercontent.com/anthropics/skills/main/skills/pdf/reference.md`

Treat the raw `pdf` file URLs in this guide as the authoritative install source for a local `pdf` skill install.

State model used in this guide:

- `pdf status` is one of: `discoverability-confirmed`, `path-validated`, `unavailable`
- `install result` is one of: `strictly validated`, `path-validated`, `incomplete`

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

Use observable signals rather than guesswork:
- choose `OpenCode` if the environment, docs, or local paths clearly refer to `OpenCode` or `opencode`
- choose `Claude Code` if the environment, docs, or local paths clearly refer to `Claude Code` or `.claude`
- choose `Codex/OpenAI Agents style setup` if the environment uses the `.agents/skills` convention and does not present itself as `OpenCode` or `Claude Code`
- if the signals conflict or no family can be identified, stop and report the blocker instead of guessing

2. Resolve the default local skills directory for that family.
Use these defaults:
- `OpenCode` -> `~/.config/opencode/skills/`
- `Claude Code` -> `~/.claude/skills/`
- `Codex/OpenAI Agents style setup` -> `~/.agents/skills/`

3. Create `<skills-dir>/homework-solver/`.

4. Install `homework-solver` from the raw URLs listed in this guide.
Treat the raw URLs in this file as the authoritative install source for the skill contents.

Download these three files into `<skills-dir>/homework-solver/` with these exact filenames:
- `SKILL.md`
- `README.md`
- `LICENSE`

Do not rely on repo metadata like `.git/` to make the skill work.

5. Verify that `<skills-dir>/homework-solver/SKILL.md` exists directly inside the folder.

6. Check the companion `pdf` status.
Treat it as:
- `discoverability-confirmed` if the platform exposes an installed or bundled skill list that includes `pdf`
- `path-validated` if a local file exists at `<skills-dir>/pdf/SKILL.md`
- `unavailable` if neither of the above is true

7. If `pdf` is not available, install it from the companion source listed above.

Create `<skills-dir>/pdf/` and download at least:
- `SKILL.md`
- `LICENSE.txt`

Recommended additional files for fuller compatibility:
- `forms.md`
- `reference.md`

The helper scripts mentioned by the `pdf` skill are optional for `homework-solver` installation. They can be added later if needed.

8. Re-check the `pdf status`.
If it is still `unavailable`, report the `install result` as `incomplete` for full `homework-solver` use and stop.

9. Only if `pdf` is available, check the local LaTeX environment by running:
- `pdflatex --version`
- `xelatex --version`
- `latexmk --version`

10. Treat the LaTeX check as a capability probe, not proof that end-to-end PDF compilation is healthy.
If at least one command returns a usable version string, report that a TeX engine appears available.
If none of those tools are available, report that compiled `.pdf` output will be blocked, although `.tex` output can still work.

11. Verify installation state.
If the platform exposes a skill list or installed-skill view, confirm that `homework-solver` appears there. That counts as discoverability-confirmed.
If the platform does not expose such a mechanism, confirm that:
- the selected `<skills-dir>` matches the host-family rule in this guide, and
- `homework-solver/SKILL.md` is directly inside that directory.

That counts only as path-validated, not discoverability-confirmed.

12. Report the final result with:
- detected agent family
- resolved skills directory
- installed skill path
- file list in `<skills-dir>/homework-solver/`
- whether `pdf` is `discoverability-confirmed`, `path-validated`, or unavailable
- LaTeX tool availability status
- whether the `install result` is `strictly validated`, `path-validated`, or `incomplete`

## Validation Standard

An installation counts as strictly validated by this guide only if all of the following are true:

- `<skills-dir>/homework-solver/SKILL.md` exists
- `<skills-dir>/homework-solver/README.md` exists
- `<skills-dir>/homework-solver/LICENSE` exists
- companion `pdf` skill is `discoverability-confirmed`, or is `path-validated` with the required local files present
- if `pdf` was installed locally from this guide, then `<skills-dir>/pdf/SKILL.md` and `<skills-dir>/pdf/LICENSE.txt` both exist
- the installation is discoverability-confirmed under Step 11

If the platform does not expose a skill list or installed-skill view, but all file-placement checks pass, the `install result` may be reported as `path-validated` rather than `strictly validated`.

LaTeX is recommended for full `.pdf` output, but not required for the skill itself to be installed. If LaTeX is missing, report a blocked `.pdf` build capability rather than a failed install state.

If `homework-solver` and `pdf` are installed correctly but LaTeX is missing, the `install result` can still be `strictly validated` or `path-validated`, with blocked compiled-PDF capability depending on the validation level reached above.

If the host platform can run with native PDF tooling even when `pdf` is absent, that may still be a runtime fallback, but it does not count as a strictly validated installation under this guide.

## Notes

- This guide is intentionally platform-generic in workflow, but not vague: it defines concrete default paths, concrete raw download sources, and concrete validation requirements.
- It does not prescribe platform-specific marketplace or plugin commands when direct file installation is sufficient.
- Runtime native PDF capability does not count as a successful strict-install verification state for this guide. The `pdf` skill itself must be available for strict validation.
