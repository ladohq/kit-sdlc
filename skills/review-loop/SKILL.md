---
name: review-loop
description: >-
  The review routine that drives a pull request to no blockers: rounds of a local review
  pass and an external reviewer, every finding verified and kept in a ledger with a
  verdict, a tier and a fate, a delta comment per external round, a stop rule, a cleanup
  pass and a final round, then a closing report. Use when you review a pull request, fix
  what a review found, or archive one that converged. The general engineering lessons it
  relies on are `engineering-rules`.
---

# Review loop

The loop takes a pull request to *no blockers and no confirmed important findings* by
alternating a **local** review pass with an **external** round: a reviewer that has not
seen the author's reasoning. It never merges; merging is the human's decision.

## Findings are evidence, not verdicts

Check every finding against the code before anyone acts on it, whoever raised it: about a
quarter of findings turn out to rest on a misread mechanism, and real defects go
unmentioned. Accepting a finding because it sounds sure and defending the code by reflex
fail the same way: neither looks at the code. Read the diff yourself as well as running
the lenses: the best findings come from reading by hand.

## The ledger

Every finding gets a row, and the row lives across rounds. It is the one source for the
fixes, the delta comment, the archive and the closing report.

| ID | Round | Source | Finding | Verdict | Tier | Fate | Evidence | Status |
|---|---|---|---|---|---|---|---|---|
| F1 | 1 | breadth: tests | submit skips the blur normalisation | confirmed | blocker | fix | `form.tsx:212` | RESOLVED in `a1b2c3d`, test `form.test.tsx` |
| F2 | 1 | external | assumes a debounce on keystrokes | refuted | — | drop | no debounce in `use-field.ts:40-58` | — |

Below the table: one line per round (its passes, the external reviewer, the commit
range), and the open residual risk.

**Verdict.** `confirmed`: reproduced in the code, with `file:line`. `refuted`: the code
does not do what the finding assumes, with the reason. `unverifiable`: needs a run, a
stack or data not at hand; make it verifiable (a test, the stack) or carry it as residual
risk, never drop it quietly.

**Tier** (confirmed only). `blocker`: breaks behaviour, loses data, opens a hole.
`important`: a real defect, or code that contradicts its spec. `minor`: style,
readability, a defect older than the change. Several lenses often flag one line: merge
them into one row.

**Fate.** Blockers and important findings: `fix`, in this branch. Minor: `fix` when it is
cheap (the file is already open, no new test needed), otherwise `decline` with the
reason. No finding becomes a new task or a debt entry: the human can still decide that
at the merge gate. A refuted finding: `drop`. A minor `fix` rides with the next round's
fixes: it is not something to fix for the external round or the stop criterion. The
cleanup is the exception: its minor `fix` findings go to the developer, and the round
after their fix is the final one.

## A round

1. **Scope.** The diff is the pull request's target branch against the head:
   `git diff origin/<target>...HEAD`. A pushed branch's `@{upstream}` diff is empty, and a
   review of nothing looks clean.
2. **Local pass.** Breadth on the first round, and on a round after a fix that reshaped
   the design; precision on every other round, on the delta since the last round. The
   lenses are in `${SKILL_DIR}/references/lenses.md`. Check the change against its spec:
   every requirement and scenario of the delta has code and a test, every task of
   `tasks.md` is checked, the design's decisions are kept, nothing out of scope was done.
3. **Verify, tier, fate** each new finding; mark each earlier one RESOLVED or STILL OPEN.
4. **Checks.** Run the project's checks (`sdlc-settings`, **Checks**) and the pull
   request's CI state; a red check whose cause is in the work is a finding
   (`engineering-rules`, "A red check"). A check must be able to fail; the traps are in `${SKILL_DIR}/references/false-greens.md`.
5. **External round**, only when the local pass leaves nothing to fix: see below.
6. **Stop** when the stop criterion holds; otherwise the findings to fix go back to the
   developer.

## Fixing a finding

Rigor follows what is at risk: a behaviour fix gets a test seen red, a refactor a red by
temporary revert, a comment fix none (`engineering-rules`, "Red first"). After fixing one
instance, grep for the others of its kind: a defect class fixed in one of four places
comes back next round. One commit per round with a message that says what it fixes; the
pull request's history is the story of the review. Re-read the pull request's
description after the fixes: reviewers most often find it stale.

## External round

The external reviewer reads what is pushed, and the developer pushes in `build`: check
that the remote head (`git ls-remote origin <branch>`) is the head you reviewed, and
never push yourself. Request the round as the
project's **External reviewer** says, with a comment rendered from the ledger:

```
Review round <n>, the delta <last reviewed sha>..<head sha>.

Changed since the last round
- <sha>: <what and why>

Findings closed
- F1 "<finding>": fixed in <sha>, test <path>
- F2 "<finding>": not a defect: <mechanism, file:line>

Deliberately not done
- F5 "<finding>": declined: <reason>

Please review the delta; the findings above are triaged.
```

Without "Deliberately not done" the reviewer reports those findings every round and the
loop never ends. Answer every open thread on the pull request.

Note the time before you post. Wait with a time ceiling, and take only a response newer
than that time: an older one is the previous round's. A timeout is a timeout, never a
clean round. When the project has no external reviewer, or it does not answer, use a
fresh agent given only the diff and the pull request's description, and label the round
"substitute" in the ledger and the closing report: it shares your priors.

Treat the external reviewer's findings like any other: verify, tier, fate.

## Stop criterion

The loop has converged when all hold:
- the last round found no blocker and no confirmed important finding;
- the pull request's CI is green: no findings over a red pipeline is not mergeable;
- the pull request's title and description match its diff;
- the cleanup pass ran after correctness converged, and one final round (precision pass
  and external round) after the cleanup found no blocker and no important finding. When
  the cleanup leaves nothing to fix, the round that converged is the final one.

Minor findings do not start another round: a minor finding of the final round gets
`decline`, with "found in the final round" as its reason, and the human weighs it at the
merge gate. A round that brings back only findings already declined is a stop signal.
After the round cap of **External reviewer** (`sdlc-settings`), if the criterion still
fails, ask the human with the open findings and your recommendation, and do as they say.

## Cleanup pass

Once correctness has converged, and only then, run the cleanup lens
(`references/lenses.md`) on the whole diff: cleaning earlier only polishes code about to
change. Skip a cleanup finding that would change intended behaviour, reach far outside
the diff or need a check you cannot run, and give the reason in its row. Cleanup edits
code that was already approved and brings its own defects, so the final round follows
it.

## Closing report

Rendered from the ledger, for the human's merge decision and the archive:
- the rounds, each with its passes and its external reviewer (a substitute named as
  such);
- the confirmed findings with their fixing commits, the refuted ones with the mechanism;
- what was declined and why;
- what was not verified: checks not run and why, findings still unverifiable. This is
  the residual risk, and the part no reader can rebuild alone;
- that nothing was merged.
