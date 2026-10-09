# Backlog

- Findings of kit-reports/sdlc-0.1.3-2026-10-09.md left out of 0.2.0 (the human, 2026-10-09:
  "handle later"): F2.1, F3.1, F4.1, F5.6, F6.1, F6.2, F7.1, F8.1, F9.1, F11.1.
- A shared contract of status words between `sdlc` and tracker kits (R16 of BLUEPRINT.md);
  until then statuses go by common meaning.
- tracker-jira-server: from 0.2.0 sdlc hands comments to `tracker` in Markdown; tracker-jira-server
  1.0.0 sends text as wiki markup, so it needs to convert Markdown to wiki markup.
- Low findings of kit-reports/sdlc-0.2.0-2026-10-09.md (visit 2) left out of 0.2.0 (the human
  at release_ok, 2026-10-09: "fix only F5.11"): F3.3 (read-back after a tracker write: what to
  compare, given formatting and the `[LADO: …]` mark), F6.4, F7.2, F9.5, F10.3.
- Next run (the human, 2026-10-09): the archive path at merge, one consistent rule for
  F2.1, F3.2, F3.3, F10.2 of kit-reports/sdlc-0.2.0-2026-10-09.md (commit 29a4c8f).
- Low findings of that report left out of 0.2.1: F6.1 (`merge` do retells the role's
  "Merge"), F6.3 (`review` do retells the stop criterion).
- Low findings of kit-reports/sdlc-0.2.1-2026-10-09.md (visit 2) left out of 0.2.1, for the
  archive-path run: F3.7 (the analyst cannot always tell the assignee is `tracker`'s account),
  F5.3 (the read-back lead sentence vs. the items that accept `tracker`'s report).

## Next run: the task for `improve` (the human, 2026-10-09)

Start `improve` in a new session, with the new kit-builder, on this task:

> Improve sdlc 0.2.1 (ladohq/kit-sdlc). (1) Bring the kit to the format of the current LADO
> and kit-builder, including `expects.commands` for the commands the kit needs outside
> LADO (`openspec`, the code host's CLI such as `gh`, `git`). (2) The archive path at
> merge, one consistent rule: only an unconverged review can be accepted at `merge_ok`
> (an unchecked task or a delta that contradicts a living spec is rejected back to
> `build`) (F3.3); when `openspec validate --specs --strict` or the contradiction grep
> fails after `openspec archive`, restore the uncommitted files, commit nothing, say so at
> the top of `merge-report` (F3.2); `merge` reads `ledger` (F2.1); without a closing
> report in the ledger, the integrator writes it from the ledger's rows, the residual
> risk and the human's acceptance at `merge_ok` (F10.2). Findings are of
> kit-reports/sdlc-0.2.0-2026-10-09.md at commit 29a4c8f (`git show
> 29a4c8f:kit-reports/sdlc-0.2.0-2026-10-09.md`). (3) The lows: F6.1, F6.3 of that report;
> F3.7, F5.3 of kit-reports/sdlc-0.2.1-2026-10-09.md. Evaluate the current version anew
> against the new kit-builder (the format changed), not from the last report. sdlc stays
> tracker-neutral (R16).
