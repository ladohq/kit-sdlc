# Review lenses

A lens is one question asked of the whole diff. Run each lens as its own pass, alone or
by a helper agent given the diff, the change's spec and this lens only, and told to edit
nothing and to leave git's state alone (no checkout, reset or stash): helpers left
unbriefed "fix" files and move HEAD. Their output is findings to verify, never verdicts.

## Breadth pass: the first round

Lenses a general reviewer never asks. Run the ones the diff gives a reason for, and say
which you skipped and why.

- **Spec.** Does each requirement and scenario of the change's delta have code that does
  it and a test that would fail without it? Does the code do anything the spec does not
  say, or the design left out of scope? The design's out-of-scope list is a source of
  candidates, not a list of closed topics.
- **Tests.** For each behaviour the diff adds or changes: is there a test, does it test
  the behaviour or only its wiring, could it fail (`false-greens.md`), and does it reach
  the edges (empty, many, error, permission, concurrent)? Name the gaps that matter most.
- **Silent failures.** Every catch, fallback, default value and retry: does an error
  surface to someone who can act on it, or is it swallowed, logged without context, or
  turned into a plausible empty result?
- **Comments and descriptions.** Each comment, docstring and the pull request's
  description against the code: does it say what the code does now, does a general word
  ("each", "every", "only") hold for every case, does a number in it still hold? A
  comment that promises more than the code delivers is a defect.
- **Types and invariants.** For new or changed types: do they make invalid states
  impossible or at least visible, are invariants checked where values enter, is anything
  exposed that callers could break?
- **General.** Bugs, the project's own rules (its agent instruction files and **Extra
  instructions**), security at the boundaries, data that existing rows may hold, and the
  contracts other modules rely on: a breaking change names every caller.

## Precision pass: every later round

Signal, not volume: the delta since the last round, read closely, each finding verified
by the one who raises it and given a confidence. Look first where fixes break things: the
other instances of a fixed defect, code moved between files, a docblock left behind, an
error path the fix added, a test changed to pass.

## Cleanup: the developer, once, before the first review

The developer asks these of its own diff and changes only the diff, never a test or
intended behaviour; the review then reads the cleaned code. Four questions:
- **Reuse**: does code repeat something the codebase already has?
- **Simplification**: can a branch, a layer or a parameter go without changing behaviour?
- **Efficiency**: work done twice, in a loop, or on data that is never used?
- **Altitude**: does each piece sit at the level of the code around it, not a framework
  for one caller nor a detail leaking out of its module?
