# sdlc

A LADO kit for one developer's day-to-day work on a project whose tasks live in Jira
Server. You name a Jira task; the kit takes it, designs the change with you as an
[openspec](https://github.com/Fission-AI/OpenSpec) change, builds it test-first, drives
the pull request through local and external review rounds, archives the change into the
project's spec archive inside the pull request and merges it, updating Jira as it goes.
You approve the design and the merge; everything in between runs on its own and stops to
ask you only when a decision is yours.

It grew out of a long-running process built on the Comet framework (OpenSpec and
Superpowers), carried over without Comet's runtime, and keeps the same archive layout, so
it works on a project that already has one.

## Install and start

You need LADO 0.29 or newer, the `openspec` CLI, the code host's CLI (e.g. `gh`) logged in,
and the Jira variables of [tracker-jira-server](https://github.com/ladohq/kit-tracker-jira-server).

```bash
lado kits add sdlc -m official
lado kits add tracker-jira-server -m official
lado start <repo> --kit sdlc --kit tracker-jira-server
```

The project keeps two settings files: `.lado/tracker.yaml` for Jira (see
tracker-jira-server) and `.lado/sdlc.md` for this kit: languages, branches, Jira status
words and label, check commands, the external reviewer, review traps and free-form extra
instructions. On the first start the supervisor offers to draft `.lado/sdlc.md` with you
from the repository; the template is `skills/sdlc-settings/sdlc.example.md`.

Then tell the supervisor which task to take: "take ABC-123". Several tasks can run at
once, each in its own worktree.

## The flow `task`

![task](blueprint-flows/task.svg)

| Step | Who | What happens |
|---|---|---|
| design | analyst | takes the task in Jira (in progress), checks it is still wanted, proposes the scale (full, hotfix or tweak), designs with you (brainstorming, then grilling; mock-ups rendered as HTML), writes the openspec change, and at full scale the design doc and the plan |
| design_ok | you | approve scope, scale, target branch and change |
| build | developer | builds test-first, runs the project's checks, opens or updates the pull request, links it in Jira, moves the task to review on its last pull request |
| review | reviewer | one round of the review loop per visit: local review pass, check against the spec, external reviewer with a delta comment; then a cleanup pass and a final round |
| archive | integrator | archives the openspec change inside the pull request, validates the specs, drafts the Jira comment |
| merge_ok | you | approve the merge, seeing the ledger and the merge report |
| merge | integrator | brings the target in, merges with a merge commit, deletes the remote branch, comments in Jira |

A rejected design goes back to the analyst; findings, a rejected merge or a conflict go
back to the developer. Until the skill `tracker` can label an existing task, the
integrator asks you to set the waiting-for-release label by hand.

## Roles and skills

- `supervisor` leads the session; `analyst`, `developer`, `reviewer`, `integrator` work
  in each run.
- Own skills: `sdlc-settings` (the settings file, talking to you, Jira writes),
  `openspec-changes` (layout, archive, compatibility), `review-loop` (the review routine,
  its lenses and false-green traps), `engineering-rules` (red test first, checks that can
  fail, evidence as numbers, git safety).
- From other packs: Superpowers' brainstorming, writing-plans, test-driven-development,
  systematic-debugging and verification-before-completion; grilling from
  mattpocock/skills; `tracker` from tracker-jira-server.

Why each part exists is in [BLUEPRINT.md](BLUEPRINT.md).
