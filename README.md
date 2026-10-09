# sdlc

A LADO kit for one developer's day-to-day work on a project whose tasks live in a task
tracker. You name a tracker task; the kit takes it, designs the change with you as an
[openspec](https://github.com/Fission-AI/OpenSpec) change, builds it test-first, drives
the pull request through local and external review rounds, archives the change into the
project's spec archive inside the pull request and merges it, updating the tracker as it goes.
You approve the design and the merge; everything in between runs on its own and stops to
ask you only when a decision is yours.

It grew out of a long-running process built on the Comet framework (OpenSpec and
Superpowers), carried over without Comet's runtime, and keeps the same archive layout, so
it works on a project that already has one.

## Install and start

You need LADO 0.29 or newer, the `openspec` CLI, the code host's CLI (e.g. `gh`) logged
in, and a tracker kit: any kit that brings the skill `tracker`, set up as that kit says.
sdlc asks the skill, by meaning, to read a task with its comments, list and change its
status and post a comment, and, when it can, link a URL, assign the task and label it.
The skill must accept comments in Markdown and format them for its tracker. sdlc reads
no settings file of the tracker kit.

For example, with [tracker-jira-server](https://github.com/ladohq/kit-tracker-jira-server) 1.1.0 or newer:

```bash
lado kits add sdlc -m official
lado kits add tracker-jira-server -m official
lado start <repo> --kit sdlc --kit tracker-jira-server
```

The project keeps the settings of this kit in `.lado/sdlc.md`: languages, branches, the
tracker's label and skipped issue types, check commands, the external reviewer, review
traps and free-form extra instructions. On the first start the supervisor offers to
draft `.lado/sdlc.md` with you from the repository; the template is
`skills/sdlc-settings/sdlc.example.md`.

Then tell the supervisor which task to take: "take ABC-123". Several tasks can run at
once, each in its own worktree.

## The flow `task`

![task](blueprint-flows/task.svg)

| Step | Who | What happens |
|---|---|---|
| design | analyst | takes the task in the tracker (in progress), checks it is still wanted, proposes the scale (full, hotfix or tweak), designs with you (brainstorming, then grilling; mock-ups rendered as HTML), writes the openspec change, and at full scale the design doc and the plan |
| design_ok | you | approve scope, scale, target branch and change |
| build | developer | builds test-first, runs the project's checks, opens or updates the pull request, links it in the tracker, moves the task to review on its last pull request |
| review | reviewer | one round of the review loop per visit: local review pass, check against the spec, external reviewer with a delta comment; then a cleanup pass and a final round |
| archive | integrator | archives the openspec change inside the pull request, validates the specs, drafts the tracker comment |
| merge_ok | you | approve the merge, seeing the ledger and the merge report |
| merge | integrator | brings the target in, merges with a merge commit, deletes the remote branch, comments in the tracker |

A rejected design goes back to the analyst; findings, a rejected merge or a conflict go
back to the developer. What the skill `tracker` cannot do (with some trackers: assign, label) the agents
ask you to do by hand.

## Roles and skills

- `supervisor` leads the session; `analyst`, `developer`, `reviewer`, `integrator` work
  in each run.
- Own skills: `sdlc-settings` (the settings file, talking to you, tracker writes),
  `openspec-changes` (layout, archive, compatibility), `review-loop` (the review routine,
  its lenses and false-green traps), `engineering-rules` (red test first, checks that can
  fail, evidence as numbers, git safety).
- From other packs: Superpowers' brainstorming, writing-plans, test-driven-development,
  systematic-debugging and verification-before-completion; grilling from
  mattpocock/skills; `tracker` from the tracker kit you start with.

Why each part exists is in [BLUEPRINT.md](BLUEPRINT.md).
