# Blueprint: sdlc

A process kit for one developer's day-to-day work on a project whose tasks live in a task
tracker (any tracker a kit of the session brings the skill `tracker` for; Jira Server through
`tracker-jira-server` is the one in use now): one tracker task becomes one run that takes the task, designs the change with the human
as an openspec change, builds it, drives the pull request through local and external review,
archives the change into the project's spec archive and merges. It carries over the human's
long-standing process on the project Clens (built on the Comet framework, Classic mode:
OpenSpec + Superpowers), without Comet's runtime, so that the kit can be improved on its own
and reused on other projects. It leads the session; a tracker kit gives its roles the skill
`tracker`, for example: `lado start <repo> --kit sdlc --kit tracker-jira-server`.

Sources: the interview in LADO session `kit-comet` (rounds 1–7, 2026-10-08), the brief
`BRIEF.md` of this repository, and the human's process dump `clens-process-dump.md`
(session artifact, 2026-10-08: memory routines, the `pr-converge` skill, 326 session
transcripts, 147 archived changes), cited below as "dump §N"; for 0.2.0, the human's answers
in LADO session `kit-sdlc` (2026-10-09), cited as "session kit-sdlc".

## 1. Requirements

- **R1** One tracker task is one run: from taking the task to a merged pull request and the
  task updated in the tracker. Several tasks run in parallel, each run in its own worktree.
  *Source:* round 2 answer 3 (the process from task to merge); dump §3.4 (a worktree per task).
- **R2** The kit is universal, not for Clens only. Everything a project differs in lives in
  the project's file `.lado/sdlc.md`: languages, the tracker's labels, branch naming
  and base branch, check commands, the external reviewer, and free-form extra instructions
  that change the kit's work. *Source:* round 1 answer 5; round 3 answer 7; dump §8.
- **R3** Specifications go on in the openspec format, compatible with the project's archive:
  `openspec/changes/<name>/`, `openspec/changes/archive/<date>-<name>/`, the living
  `openspec/specs/`, design and plan documents in `docs/superpowers/{specs,plans,reports}/`,
  `openspec validate --specs --strict` passing. The openspec CLI is a prerequisite; the
  Comet state file `.comet.yaml` is not written. *Source:* round 2 answer 1 ("very important:
  compatibility with the existing specifications"); round 3 answer 1.
- **R4** Work goes in phases; after any interruption the run goes on from the phase it
  stopped in, and each phase is done by an agent focused on it, with a fresh context.
  *Source:* round 2 ("Comet splits work into phases and always knows what to take next …
  survives unexpected stops"); dump §5 pattern 8 (long tasks exhaust the context).
- **R5** The task's scale — full, hotfix or tweak — is proposed by the agent and approved by
  the human; it sets how deep design and build go, not which flow runs.
  *Source:* round 2 (Comet's three scales); round 4 answer 3 (one flow, branching allowed).
- **R6** Nothing starts the code without the human's approval of the design (scope and
  design together), and nothing merges without the human's approval. On a critical
  disagreement or a change of scope an agent asks the human during its step; there is no
  other stop. *Source:* round 3 answer 2; round 4 answer 4; dump §7.1 C.
- **R7** Design is a dialogue with the human: brainstorming, then grilling for edge cases;
  UI options are shown rendered (an HTML mock-up), never as ASCII; the agent asks only about
  what the human can see and decides invisible engineering forks itself, listing them.
  *Source:* round 1 (grill-me at design); dump §6 rules 33–34, §7.3 items 8 and 11.
- **R8** Review is a loop of a local review pass and an external reviewer's round with a
  delta comment, recorded in a findings ledger. Confirmed blockers and important findings,
  and cheap minor ones, are fixed in the same branch; no follow-up tasks are filed. At most
  3 external rounds; once correctness converges, a cleanup (simplify) pass and one final
  round. The review know-how is the kit's own, provider-neutral (no one CLI's review
  commands). *Source:* round 1 (the review routine); round 4 answer 4; round 6 ("our own
  skills for code review"); dump §2 (`pr-converge`), §6 rules 10–16.
- **R9** The openspec change is archived after review converges and before the merge, by a
  commit inside the pull request. *Source:* round 4 answer 2; dump §5 pattern 1 (35 of 53
  tweak changes were review-fix rounds caused by archiving before review).
- **R10** Merge follows the human's routine: bring the base branch in, merge with a merge
  commit (never squash), check the merge by the pull request's state, delete the branch and
  the worktree; in the tracker, the label for "waiting for release" and a two-part comment
  (for QA, for the developer) in Markdown (R16). A task merged into an epic branch gets the comment but
  no label and stays in review. *Source:* round 1 (merge procedure); dump §1.2 «Мерж PR»,
  «Мерж PR в эпик»; round 5 answer 1. When the skill `tracker` cannot label an existing
  task, the integrator asks the human, in its note after the merge, to set the label by hand
  (R11). *Source:* round 5 answer 2; session kit-sdlc (R16).
- **R11** Agents write to the tracker only as the routine says: take the task (assign, "in
  progress"), link each pull request, move to "review" only on the task's last pull request,
  the merge label and comment. A new tracker task only after the human's explicit yes. All of
  it through the skill `tracker`. One rule for what `tracker` may lack: when it has the
  action (assign a task, label an existing one, link a URL), the routine does it; otherwise
  the human does it by hand, asked in the agent's note (a link goes in as a comment). The kit does not bring `tracker` itself: another kit of the
  session does (now `tracker-jira-server`; another tracker kit with a skill of the same
  name fits as well), so `kit.yaml` declares it in `expects.skills` and the roles that use
  it name it in their `skills:`. `expects` needs LADO 0.29 or newer, so `kit.yaml` sets `dependencies.lado: ">=0.29"`
  (round 7: a LADO change made for this kit, released in LADO 0.29.0). *Source:* brief
  ("Known so far: the tracker"); dump §1.1, §6 rules 3–5; round 7 (B1).
- **R12** Specialised roles, each good at one kind of work with its own rights: analyst,
  developer, reviewer, integrator. *Source:* round 4 ("specialised roles … that fit the
  budget and do only their task well"; answer "4 roles").
- **R13** The lessons of the human's history that hold on any project are rules for the
  agents: a behaviour fix starts from a test seen red; a load-bearing check must be shown
  able to fail; check results are reported as numbers with what was not run; no `git add
  -A`, no bare stash, no force push. *Source:* round 1 ("extract useful routines and turn
  them into rules for our agents"); dump §6.
- **R14** Everything the human reads is in the language the project's settings name, and
  whatever they must read is the last message before a question, not hidden before it.
  *Source:* dump §6 rules 32, 35.
- **R16** The kit works with any task tracker. Its roles never reach a tracker themselves and
  name no tracker's commands, settings files, markup or key format; they use them only as
  the skill `tracker` says. They know only that the skill `tracker` is there and ask it for actions by meaning (read the task with its
  comments, change its status, comment, link the pull request). Text for the tracker is
  Markdown; the tracker skill formats it for its tracker. A tracker kit used with `sdlc`
  must therefore accept Markdown: `tracker-jira-server` gets this in its next release (in
  work, 2026-10-09), and `sdlc` 0.2.0 is published after it. Statuses are named by their
  common meaning ("in progress", "review"); the agent may ask `tracker` which statuses the
  task has and picks by meaning (a shared contract of status words between `sdlc` and
  tracker kits is left for later). Run, change and branch names are made from the task key
  converted to lowercase letters, digits and `-` (`ABC-123` → `abc-123`, `#123` →
  `task-123`; the rule is in `sdlc-settings`); an epic only when the tracker has them. *Source:* session kit-sdlc, 2026-10-09
  ("sdlc must not go to any tracker itself … only know it has the skill tracker and ask it";
  "markup in universal Markdown … the tracker formats it"; "use common statuses … adapt by
  meaning"; "convert the key to the format we need"; on F5.7 of the 0.2.0 check: "we are
  implementing it in the tracker right now"). No upgrade path from 0.1.x: no project uses
  the kit yet (session kit-sdlc, 2026-10-09).
- **R15** The kit grows in versions; the plan of versions is kept in this blueprint
  (section 5). *Source:* round 1 ("break the build into phases … save the plan and follow it").

## 2. Starting point

The transferred process (round 1 answer 3, dump §3 and §5), as the human runs it on Clens:

| # | Step | Who | Hands on | Read later by |
|---|---|---|---|---|
| 0 | Rank the Jira backlog (rarely) | agent, human picks | ranked list | human |
| 1 | Take the task: assign, In Progress, read description, comments, attachments, links; skim the code; confirm it is still relevant | agent | understanding of the task | step 2 |
| 2 | Comet open: scale (full/hotfix/tweak), openspec proposal and delta spec; grilling | agent, **human confirms** | openspec change | steps 3–6 |
| 3 | Comet design (full only): brainstorming, grilling, design doc, plan | agent, **human confirms** | design doc, plan | step 4 |
| 4 | Comet build: TDD, subagents or inline | agent | code, tasks.md | step 5 |
| 5 | Comet verify: checks, verify report | agent | report | merge gate |
| 6 | Comet archive, push, open the PR; Jira link + Code Review | agent, human confirms | PR | step 7 |
| 7 | Review loop (`pr-converge`): local review, triage with the human, fixes, `@claude` bot round; cleanup, final round | agent, **human triages** | ledger, closing report | human |
| 8 | Merge: pull master, conflicts, merge commit, cleanup | agent, **on the human's command** | merged PR | — |
| 9 | Jira: label `waiting_for_stage`, two-part report comment | agent | Jira comment | QA, developers |

It is closest to the archetype **Feature with design gate** (designer, implementer,
reviewer; gates before the work and before the merge). What changed and why:
- Step 0 and the QA hand-off (release) stay with the human for v1 (section 5, plan).
- Steps 1–3 are one step of one role (analyst): they are one dialogue with the human, and
  Comet's open and design confirmations become one gate, `design_ok` (R6).
- Comet's verify (an artifact check) moves into the review: the reviewer checks the change
  against its spec as well as the code (R8).
- Archive moves after the review, so review fixes belong to the original change (R9).
- Per-round triage with the human becomes a rule (R8) plus the merge gate.
- An integrator takes archive, merge and the tracker: other rights (merge, tracker writes) and a fresh
  context after the review loop (R4, R12).
- Comet's state file, guards and resume are LADO's run state, step outcomes and `max_visits`.

```yaml
name: task
start: design
states:
  design:
    agent: analyst
    outcomes: {ready: design_ok, dropped: dropped}
  design_ok:
    gate: approval
    outcomes: {approved: build, rejected: design}
  build:
    agent: developer
    outcomes: {ready: review, redesign: design}
  review:
    agent: reviewer
    max_visits: 8
    outcomes: {changes: build, converged: archive}
  archive:
    agent: integrator
    outcomes: {ready: merge_ok}
  merge_ok:
    gate: approval
    outcomes: {approved: merge, rejected: build}
  merge:
    agent: integrator
    outcomes: {merged: done, conflicts: build}
  done:
    end: true
  dropped:
    end: true
```

![task](blueprint-flows/task.svg)

`review` visits (`max_visits: 8`): the first local round, the external rounds up to the
round cap of the project's settings, the cleanup and final rounds, and a re-entry after a
rejection at `merge_ok` or `conflicts`; the limit is a rare stop. On a
later visit the reviewer marks its previous findings RESOLVED or STILL OPEN. `build` on a
later visit fixes what the ledger, the human's rejection, the merge conflict or a red CI
from `merge` names; after `redesign` it builds the newly approved design.

## 3. Traceability

| Element | Kind | Covers | Why it exists / why nothing simpler |
|---|---|---|---|
| `supervisor` | role (lead) | R1, R6, R14, R16 | Starts one run of `task` per tracker task the human names (the run named after its key, converted), in the human's language; answers blocked workers; brings the human to the gates. |
| `analyst` | role | R5, R7, R11, R12, R16 | Takes the task in the tracker and runs the design dialogue with the human directly (the other workers reach the human through the supervisor); writes documents only, never code. |
| `developer` | role | R4, R8, R12, R13 | Builds from the approved artifacts with a fresh context; fixes what the review, a rejection or a conflict names. |
| `reviewer` | role | R8, R12, R13 | A fresh look the developer cannot give itself: fixes and cleanups introduce defects (dump §5 pattern 3). Read-only on code. |
| `integrator` | role | R9, R10, R11, R12, R16 | Other rights (merge, tracker writes) and a fresh context after the long review loop; follows the merge routine with its known traps. |
| `task` | flow | R1, R4 | One run per task; LADO keeps its state, so it resumes after any stop. |
| `task.design` | work step | R3, R5, R7, R11, R16 | Takes the task ("in progress"; assigns it through `tracker`, or asks the human to), checks it is still relevant (`dropped` otherwise), proposes the scale, writes the openspec change, and for full the design doc and plan; names the target branch (base or epic) and checks `.lado/sdlc.md` is on it. Reads `build-report` on a return. Produces `design`. |
| `task.design_ok` | gate | R5, R6 | Code starts only on the human's approval of scope, scale and design. Reads `design`. |
| `task.build` | work step | R8, R11, R13 | TDD and the change; the project's checks; push; the pull request, its link in the tracker, "review" on the last PR. `redesign` when the work needs a scope change. Reads `design`, `ledger`. Produces `build-report`. |
| `task.review` | work step | R8, R13 | Local review pass, external round with a delta, findings ledger, check against the spec; `max_visits: 8` (the first local round, up to the round cap of external rounds, cleanup, final round and a re-entry after `merge_ok` or `conflicts`; the limit is a rare stop, not a step of an ordinary run). Reads `design`, `build-report`. Produces `ledger`. |
| `task.archive` | work step | R3, R9, R10 | Archives the openspec change inside the PR, validates the specs, drafts the tracker comment in Markdown. Reads `design`, `ledger`. Produces `merge-report`. |
| `task.merge_ok` | gate | R6, R10 | The merge to the base branch is hard to undo (stage deploys from it on Clens). Reads `ledger`, `merge-report`. |
| `task.merge` | work step | R10, R11 | Brings the base in, merges with a merge commit, checks by state, cleans up, tracker label and comment; `conflicts` back to build when bringing the target in conflicts or a local check is red for a cause in the work; a red CI after the push leaves the pushed merge: `conflicts` for a cause in the work, the supervisor otherwise. Pushes to the pull request's branch as the developer does. Reads `design`, `merge-report`. |
| `sdlc-settings` | skill | R2, R11, R14, R16 | The format of `.lado/sdlc.md` and how every role reads it, and the routine of writes to the tracker ("Writing to the tracker"): what the roles ask `tracker` for, by meaning, and what the human does by hand when `tracker` lacks an action; one place instead of five roles. |
| `openspec-changes` | skill | R3, R9, R16 | How to create, validate and archive a change compatibly with the project's archive (analyst, developer, reviewer, integrator). |
| `review-loop` | skill | R8, R13 | The human's `pr-converge` routine, provider-neutral: passes, verdicts, ledger, delta comment, stop rule, false-green traps; its references hold the review lenses (breadth, precision, cleanup), written in the kit's own words from the review tools the human uses now (session artifact with their texts, round 6), never naming one CLI's commands. Project-specific traps (Clens's `.claude/pr-converge.md`) belong in the project's `.lado/sdlc.md`, not here. |
| `engineering-rules` | skill | R13 | The history's general lessons (red test first, checks that can fail, evidence as numbers, git safety), shared by developer, reviewer and integrator. |
| superpowers skills (brainstorming, writing-plans, test-driven-development, systematic-debugging, verification-before-completion) | dependency skills | R5, R7, R8 | The methods Comet Classic used; published, so declared in `dependencies.skills`, not copied. |
| `grilling` | dependency skill | R7 | The human's grilling step at design. |
| `tracker` (a tracker kit, now `tracker-jira-server`) | skill of another kit | R2, R11, R16 | The task tracker, whatever it is; the roles ask it for actions by meaning and never use its syntax or settings files; declared in `kit.yaml` `expects.skills` (LADO >= 0.29), named in the `skills:` of analyst, developer, integrator and supervisor; the session adds a kit that provides it. |

Reverse check:
- R1: `supervisor`, `task`. R2: `sdlc-settings`. R3: `task.design`, `openspec-changes`,
  `task.archive`. R4: `task`, `developer`, the four roles' fresh contexts. R5: `analyst`,
  `task.design`, `task.design_ok`. R6: `task.design_ok`, `task.merge_ok`, `supervisor`.
  R7: `analyst`, `task.design`, brainstorming, `grilling`. R8: `reviewer`, `task.review`,
  `task.build`, `review-loop`. R9: `task.archive`, `openspec-changes`. R10: `integrator`,
  `task.archive`, `task.merge_ok`, `task.merge`. R11: `analyst`, `task.design`, `task.build`,
  `task.merge`, `tracker`, `sdlc-settings`. R12: the four roles. R13: `engineering-rules`, `review-loop`,
  `developer`, `reviewer`. R14: `sdlc-settings`, `supervisor`. R15: section 5. R16:
  `sdlc-settings`, `tracker`, `supervisor`, `analyst`, `integrator`, `openspec-changes`.

## 4. Complexity budget

Counted by the `kit-budget` script on the built kit (sdlc 0.2.0, 2026-10-09).

| Measure | Where | Value | Green / yellow up to | Zone | Reason, when not green |
|---|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow | R12, R4: the human asked for specialised roles; a run keeps one worker per role, so with three roles the developer would carry the whole review loop's context into archive and merge, and merging and tracker writes are rights the developer does not otherwise need. |
| Work steps in a flow | flows/task.yaml | 5 | 5 / 8 | green | |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green | |
| Words in a role prompt | agents/analyst.md | 798 | 800 / 1500 | green | |
| Words in a role prompt | agents/developer.md | 613 | 800 / 1500 | green | |
| Words in a role prompt | agents/integrator.md | 640 | 800 / 1500 | green | |
| Words in a role prompt | agents/reviewer.md | 419 | 800 / 1500 | green | |
| Words in the lead's prompt | agents/supervisor.md | 753 | 1000 / 1500 | green | |
| Own skills | kit | 4 | 5 / 10 | green | |
| MCP servers | kit | 0 | 2 / 4 | green | |
| Similar paragraphs | kit | none | | green | |

## 5. Change log

| Date | Version | Change | ← Fact (session, run, metric or report) |
|---|---|---|---|
| 2026-10-08 | 0.1.1 | `merge`: undo the merge only before the push; a red CI after it leaves the pushed merge commit: `conflicts` for a cause in the work, the supervisor otherwise; push only with every check green | kit-reports/sdlc-0.1.0-2026-10-08.md F5.5, F3.7 |
| 2026-10-08 | 0.1.1 | `review-loop`: a minor `fix` rides with the next round's fixes and does not count as something to fix for the external round or the stop criterion | same report, F3.6 |
| 2026-10-08 | 0.1.1 | The settings files are checked on the run's target branch (base or epic), not only the base | same report, F2.4 |
| 2026-10-08 | 0.1.1 | `task.review` `max_visits` 5 → 8 | same report, F8.1 |
| 2026-10-08 | 0.1.1 | Supervisor at `cancel` tells the human what is left and asks before closing anything; analyst at `dropped` after In Progress asks whether to move the task back | same report, F12.2, F12.3 |
| 2026-10-08 | 0.1.1 | Analyst leaves the execution header of `writing-plans` out of the plan file | same report, F5.6 |
| 2026-10-08 | 0.1.2 | Integrator pushes to the pull request's branch as the developer does; developer's "Fixing" covers a red CI from `merge` | kit-reports/sdlc-0.1.1-2026-10-08.md F2.6, F3.9 |
| 2026-10-08 | 0.1.2 | Analyst talks to the human directly again (rule lost in 0.1.1); after the settings answer it fetches and checks again | same report, F7.2, F2.5 |
| 2026-10-08 | 0.1.2 | `review-loop`: one reading of when a minor `fix` is fixed or declined; when the reviewer cannot start a fresh agent, it asks the human whether to go on without the external round | same report, F3.8, F11.1 |
| 2026-10-08 | 0.1.2 | `dropped`: the analyst asks about moving the task back before dropping; the supervisor names what is left when a pull request exists | same report, F12.3, F12.4 |
| 2026-10-08 | 0.1.2 | `archive` and `merge-report` match `openspec-changes` and the manual label | same report, F3.10, F5.8 |
| 2026-10-08 | 0.1.2 | Blueprint prose on `merge` and `review` visits matches the flow | same report, F5.7, F8.2 |
| 2026-10-08 | 0.1.2 | No fallback reviewer from the supervisor: the reviewer without a fresh agent asks the human; their "go on without the external round" holds for the rest of the loop and counts for the stop criterion | kit-reports/sdlc-0.1.2-2026-10-08.md F12.7, F3.13, F7.4 |
| 2026-10-08 | 0.1.2 | `build` after `redesign` builds the newly approved design; `design` quotes either reason for a return; the pull request is opened when none is open | same report, F2.10, F4.3, F2.9 |
| 2026-10-08 | 0.1.2 | `dropped` after a rejection at `design_ok` or after `redesign`: the supervisor names the committed work before asking to discard | same report, F12.8, F2.8 |
| 2026-10-08 | 0.1.2 | Integrator reads convergence from the ledger and archives nothing when it is not shown; the developer fixes a red CI from `merge` on top of the branch | same report, F10.2, F3.12, F3.11 |
| 2026-10-08 | 0.1.2 | `review-loop`: breadth first only on a full change; no external round over an unpushed head. Settings file put on the run's target branch. Analyst names `grilling` with the method-only rule | same report, F5.14, F3.14, F12.9, F5.11 |
| 2026-10-08 | 0.1.2 | Blueprint prose: `build` revisits, `task.design` assign, users of `openspec-changes` and `engineering-rules` | same report, F8.3, F5.15, F5.16, F5.13 |
| 2026-10-08 | 0.1.2 | Developer's "Fixing" covers a red local check after the target was brought in; a task may stay unchecked with a reason in `build` and the review | kit-reports/sdlc-0.1.2-2026-10-08.md (visit 3) F2.11, F5.17 |
| 2026-10-08 | 0.1.2 | `merge_ok` over an unconverged ledger: the human sends it back (the developer quotes it and reports `ready`) or accepts the findings (the integrator archives first, then merges) | same report, F3.15 |
| 2026-10-08 | 0.1.2 | `review-loop`: a remote head ahead or diverged goes to the developer with both heads; a return from `merge_ok` or `conflicts` gets a precision pass and an external round; the human's answer on a skipped round is followed either way | same report, F3.16, F8.4, F7.5 |
| 2026-10-08 | 0.1.2 | Reviewer makes no probe edits: a check it cannot see fail goes into the ledger as unverifiable | same report, F5.18 |
| 2026-10-08 | 0.1.2 | `dropped` asks about moving the task back from "review" too; `build` wording on the first visit; blueprint `task.merge` prose | same report, F12.10, F9.4, F5.19 |
| 2026-10-09 | 0.1.3 | README installs the kit from the official marketplace (`lado kits add sdlc -m official`) | human, session kit-sdlc (publishing) |
| 2026-10-09 | 0.2.0 | Any task tracker (R16): no `.lado/tracker.yaml` check; roles name tracker actions by meaning, never `jira.py` syntax; one rule for an action `tracker` lacks (assign, label, link); "Writing to the tracker" and settings section **Tracker** | kit-reports/sdlc-0.1.3-2026-10-09.md F5.1, F10.1, F5.4, F9.2 |
| 2026-10-09 | 0.2.0 | Merge comment in Markdown, the tracker skill formats it; statuses by common meaning, no status words in `.lado/sdlc.md`; names from the task key converted to lowercase letters, digits and `-`, epics only when the tracker has them | same report, F5.2, F5.3, F5.5; session kit-sdlc answers 1–3 |
| 2026-10-09 | 0.2.0 | kit.yaml description, README and the flow's description: a task tracker, `tracker-jira-server` as the example | same report, F10.2 |

Plan of versions (R15), revisited after v1 works on Clens:
- **v1** (this blueprint): R1–R16, flow `task`.
- **v2**: ranking the tracker's backlog; a shared contract of status words between `sdlc` and tracker kits (R16); the QA hand-off (release) as its own flow; checking the
  change on a preview environment with e2e as part of the review; what v1's use shows.
- **Later, if needed**: from the reference's SDLC process — checking that the plan covers
  every acceptance criterion of the task; HTML reports at the gates.
