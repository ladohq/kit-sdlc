# Backlog

- Rule freeze (R15 of BLUEPRINT.md): from 0.4.0 no new rule without a fact from a real run
  of the kit, not a critic's report; the findings below wait for such a fact.

- Findings of kit-reports/sdlc-0.1.3-2026-10-09.md left out of 0.2.0 (the human, 2026-10-09:
  "handle later"): F2.1, F3.1, F4.1, F5.6, F6.1, F6.2, F7.1, F8.1, F9.1, F11.1.
- A shared contract of status words between `sdlc` and tracker kits (R16 of BLUEPRINT.md);
  until then statuses go by common meaning.
- tracker-jira-server: from 0.2.0 sdlc hands comments to `tracker` in Markdown; tracker-jira-server
  1.0.0 sends text as wiki markup, so it needs to convert Markdown to wiki markup.
- Low findings of kit-reports/sdlc-0.2.0-2026-10-09.md (visit 2) left out of 0.2.0 (the human
  at release_ok, 2026-10-09: "fix only F5.11"): F3.3 (read-back after a tracker write: what to
  compare, given formatting and the `[LADO: …]` mark), F6.4, F7.2, F9.5, F10.3.
- F5.1 of kit-reports/sdlc-0.3.0-2026-10-09.md (the human at release_ok of `improve/sdlc-3`,
  2026-10-09: "write the requirements for tracker in the artifacts; we will release a new
  version"): the session artifact `tracker-requirements` (a "who am I" action for `tracker`).
  Once a tracker kit gives it: sdlc's README names that version, `sdlc-settings` gets the
  fallback "when `tracker` cannot say which account it works as and the task has an
  assignee, ask the human first", R16 names the version.

