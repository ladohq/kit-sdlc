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

You need LADO 0.42 or newer (the supervisor gives its advice on gate cards; the reviewer
is read-only, so the kit does not run on Codex CLI, which cannot take an agent's file-editing tools away), `git` and the `openspec` CLI on the agents' PATH, the code
host's CLI that the project's settings name (e.g. `gh`) logged in, and a tracker kit: any kit that brings the skill `tracker`, set up as that kit says.
sdlc asks the skill, by meaning, to read a task with its comments, list and change its
status and post a comment and report its id, and, when it can, link a URL, assign the task and label it,
and whatever the project's **After the merge** names (a status, a label, a field).
The skill must accept comments in Markdown and format them for its tracker. sdlc reads
no settings file of the tracker kit.

Setting a field (e.g. a release) needs tracker-yougile 0.5.0 or newer or
tracker-jira-server 1.6.0 or newer: `expects.skills` checks only the skill's name, not
its version.

For example, with [tracker-jira-server](https://github.com/ladohq/kit-tracker-jira-server) 1.6.0 or newer:

```bash
lado kits add sdlc -m official
lado kits add tracker-jira-server -m official
lado start <repo> --kit sdlc --kit tracker-jira-server
```

The project keeps the settings of this kit in `.lado/sdlc.md`: languages, branches, the
tracker's skipped issue types, what happens to the task after the merge (**After the
merge**; without it the task is left as it is), check commands, the external reviewer, review
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
| build | developer | builds test-first, cleans up its own diff before the first review, lists every existing test it changed, runs the project's checks, opens or updates the pull request, links it in the tracker, moves the task to review on its last pull request |
| review | reviewer | one round of the review loop per visit: local review pass, check against the spec, external reviewer with a delta comment; at most 2 external rounds (1 for hotfix and tweak), more only on your decision |
| merge_ok | you | approve archiving and merging, seeing the ledger with its closing report and the findings left open |
| merge | integrator | archives the openspec change inside the pull request, brings the target in, merges with a merge commit, deletes the remote branch, does what the project's **After the merge** says in the tracker |

A rejected design goes back to the analyst; findings, a rejected merge, a refused archive or a conflict go
back to the developer. A conflict only in the changelog the integrator resolves itself, keeping both
sides' entries, and merges without asking you again. What the skill `tracker` cannot do (with some trackers: assign, label) the agents
ask you to do by hand; an action of **After the merge** the tracker lacks or refuses the
integrator names in its note, having done the rest.

## Upgrading from 0.5

The **Tracker** lines «Waiting for release label» and the merge comment template are no
longer read. Move them into a section **After the merge** of `.lado/sdlc.md`, as
`skills/sdlc-settings/sdlc.example.md` shows: into the base branch, the comment by the
template, the label, and a status if you want one; into an epic branch, the comment.
Until you do, the integrator leaves the task as it is after a merge and says so.

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
