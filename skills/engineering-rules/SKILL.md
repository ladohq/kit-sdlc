---
name: engineering-rules
description: >-
  Lessons from a long project history that hold on any codebase: a behaviour fix starts
  from a test seen red, a load-bearing check must be shown able to fail, results are
  reported as numbers with what was not run, and git is used so no one else's work is
  lost. Use whenever you change code, run checks, report them or commit. The review
  routine itself is `review-loop`.
---

# Engineering rules

Each rule below was learned from a defect that reached a pull request or a branch it
should not have.

## Red first

- A change of behaviour, or a bug fix, starts with a test that fails for the right
  reason, and you see it fail. A green you never saw red proves only that covered
  behaviour did not change.
- A refactor whose tests must pass unchanged gets its red by temporary revert: put the
  old line back, watch the test fail, restore it, and check with `git diff` that the file
  came back byte for byte.
- A test of absence ("X is not sent") needs the reverse probe: make the code send X and
  watch the test fail.
- Docs, comments and renames with no behaviour need no test.
- Commit before you probe. `git checkout -- <file>` restores the whole file from the
  last commit and takes your uncommitted fix with the probe.

## Checks that can fail

A check that cannot fail proves nothing, and it looks exactly like one that passed. When
a check is load-bearing for your claim and you have not seen it fail in this repository,
introduce a deliberate error, see the check catch it, then remove the error. The common
shapes, and the project's own, are in the skill `review-loop` (`references/
false-greens.md`) and in the project's **Review traps** (`sdlc-settings`).

## Evidence

- Report results as numbers: "214 suites, 3512 tests passed, lint clean", not "tests
  pass".
- Name every check you did not run, with the reason. Silence about a skipped check reads
  as a claim that it passed.
- Delete what a run created (generated files, temporary configs, probes) before you
  commit; restore tracked files a build rewrote.

## Git

- Work only inside the run's worktree, with absolute paths; leave it only by path, never
  by `cd ..`.
- Stage files by name; never `git add -A` or `git add .`, which take other sessions'
  files. Commit with a pathspec (`git commit -m <msg> -- <paths>`) and check the commit
  with `git show --stat HEAD`.
- Keep the stash stack alone: it is shared by every worktree. To set work aside, commit
  it.
- Never force-push, never rewrite pushed history, never `--no-verify`: reviewers and
  their comments depend on the commits they saw.
- A gating command piped into `tail`, `head` or `grep` reports the pipe's exit code, not
  its own: write the output to a file and read the status and the summary line.
- Numbers that must be unique across branches (migration revisions, debt entries) are
  taken as the highest across all refs plus one, at the time of writing, and the entry
  says so.
