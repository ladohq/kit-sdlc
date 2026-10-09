---
name: openspec-changes
description: >-
  How an openspec change is laid out, written, validated, archived and reopened so that it
  stays compatible with the project's spec archive (`openspec/changes/`,
  `openspec/changes/archive/`, `openspec/specs/`, `docs/superpowers/`). Use when you
  create or edit a change, check code against its spec, archive it, or must fix something
  after its archive. Needs the openspec CLI; writes no `.comet.yaml`.
---

# openspec changes

The project's specifications are an openspec archive: every implemented change is kept,
and the living specs are the sum of them. Agents read that archive for years, so a change
must look like the ones before it. Before you write one, read one or two recent changes
under `openspec/changes/archive/` and follow their shape and language (`sdlc-settings`,
**Languages**).

The openspec CLI is a prerequisite: when `openspec --version` fails, stop and tell the
supervisor. `openspec --help` names the commands of the installed version. Never write a
`.comet.yaml` or other state files of a workflow tool: the run keeps the state.

## Layout

| Path | Holds |
|---|---|
| `openspec/changes/<name>/proposal.md` | why, what changes, scope and out of scope (with the reason for each item left out), impact |
| `openspec/changes/<name>/specs/<capability>/spec.md` | the delta: requirements added, modified, removed |
| `openspec/changes/<name>/design.md` | technical decisions, numbered D1, D2, … (full scale, or when a hotfix needs one) |
| `openspec/changes/<name>/tasks.md` | the build's checklist, `- [ ] 1.1 …`, grouped by numbered sections |
| `openspec/changes/archive/<YYYY-MM-DD>-<name>/` | the change after archive |
| `openspec/specs/<capability>/spec.md` | the living spec |
| `docs/superpowers/specs/<YYYY-MM-DD>-<topic>-design.md` | the design doc (full scale) |
| `docs/superpowers/plans/<YYYY-MM-DD>-<topic>.md` | the implementation plan (full scale) |
| `docs/superpowers/reports/<YYYY-MM-DD>-<name>-review.md` | the review's closing report, written at archive |

`<name>` is kebab-case and starts with the task key as a name (`sdlc-settings`, "The task
key as a name"): `abc-123-export-csv`.

## Writing the delta

- Sections `## ADDED Requirements`, `## MODIFIED Requirements`, `## REMOVED
  Requirements`, `## RENAMED Requirements`; each requirement `### Requirement: <name>`
  whose first line states the rule with SHALL or MUST (strict validation looks only
  there), then at least one `#### Scenario: <name>` with **WHEN** / **THEN** lines.
- A MODIFIED requirement is copied whole from the living spec and then changed, every
  scenario kept under its old name: archive replaces the requirement with what you
  wrote, so whatever you leave out is deleted.
- Name requirements, never point at them by position ("the requirement above"): archive
  reorders them.
- A new rule can contradict a living spec of another capability, and validation does not
  see it: grep `openspec/specs/` for the nouns of your rule and resolve every hit in the
  delta.
- A change with no behaviour rule (tooling, docs) has no delta; it is archived with
  `--skip-specs`.

Validate the change with `openspec validate <name> --strict` after every edit.

## Tasks

Each task of `tasks.md` is small enough for one commit and names how it is checked. The
developer checks a task (`- [x]`) when it is done and committed; a task dropped on the
way stays unchecked with a line saying why, and the build report names it.

## Archive

Archive after the review has converged and before the merge, on the pull request's
branch, so the archive rides inside the pull request and every review fix belongs to the
change it fixes.

1. Every task of `tasks.md` is checked, or its line says why not, and the grep for
   contradictions (above) finds none. When either fails, do not archive.
2. `openspec archive <name> --yes` (add `--skip-specs` for a change without a delta). It
   merges the delta into `openspec/specs/` and moves the change to
   `openspec/changes/archive/<today>-<name>/`.
3. `openspec validate --specs --strict` passes. When it fails, undo the archive before
   any commit: `git restore --staged --worktree -- openspec/`, then delete every
   untracked path under `openspec/` that `git status --short -- openspec/` lists (the
   folder under `archive/`, the spec of a new capability); `git status` then shows no
   change under `openspec/`. Commit nothing and say so at the top of `merge-report`.
4. Write the review's closing report (`review-loop`, "Closing report") to
   `docs/superpowers/reports/`.
5. Commit these paths alone, message `archive(<KEY>): <name>`, and push. The pull
   request's diff against its target then shows the change under `archive/` and no
   `openspec/changes/<name>/` left.

## After the archive

A fix that comes after the archive (the human rejected the merge, the target branch
conflicts) belongs to the same change. Undo the archive with `git revert <archive
commit>`, a new commit that brings back the active change and the living specs as they
were; never rewrite pushed history. Fix the change and its code as usual; the integrator
archives it again after the review.
