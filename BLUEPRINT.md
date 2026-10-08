# Blueprint: sdlc

A process kit for one developer's day-to-day work on a project whose tasks live in Jira
Server: one Jira task becomes one run that takes the task, designs the change with the human
as an openspec change, builds it, drives the pull request through local and external review,
archives the change into the project's spec archive and merges. It carries over the human's
long-standing process on the project Clens (built on the Comet framework, Classic mode:
OpenSpec + Superpowers), without Comet's runtime, so that the kit can be improved on its own
and reused on other projects. It leads the session; the kit `tracker-jira-server` gives its
roles the skill `tracker`:
`lado start <repo> --kit sdlc --kit tracker-jira-server`.

Sources: the interview in LADO session `kit-comet` (rounds 1–6, 2026-10-08), the brief
`BRIEF.md` of this repository, and the human's process dump `clens-process-dump.md`
(session artifact, 2026-10-08: memory routines, the `pr-converge` skill, 326 session
transcripts, 147 archived changes), cited below as "dump §N".

## 1. Requirements

- **R1** One Jira task is one run: from taking the task to a merged pull request and the
  task updated in Jira. Several tasks run in parallel, each run in its own worktree.
  *Source:* round 2 answer 3 (the process from task to merge); dump §3.4 (a worktree per task).
- **R2** The kit is universal, not for Clens only. Everything a project differs in lives in
  the project's file `.lado/sdlc.md`: languages, Jira transitions and labels, branch naming
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
  the worktree; in Jira, the label for "waiting for release" and a two-part comment (for QA,
  for the developer) in wiki markup. A task merged into an epic branch gets the comment but
  no label and stays in Code Review. *Source:* round 1 (merge procedure); dump §1.2 «Мерж PR»,
  «Мерж PR в эпик»; round 5 answer 1. Until the skill `tracker` can label an existing task
  (round 5 answer 2: a patch of `tracker-jira-server`), the integrator asks the human, in its
  note after the merge, to set the label by hand.
- **R11** Agents write to Jira only as the routine says: take the task (assign, In Progress),
  link each pull request, move to Code Review only on the task's last pull request, the
  merge label and comment. A new Jira task only after the human's explicit yes. All of it
  through the skill `tracker`. *Source:* brief ("Known so far: the tracker"); dump §1.1,
  §6 rules 3–5.
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
- An integrator takes archive, merge and Jira: other rights (merge, Jira writes) and a fresh
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
    max_visits: 5
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

`review` visits: up to 3 external rounds, the cleanup round and the final round; on a
later visit the reviewer marks its previous findings RESOLVED or STILL OPEN. `build` on a
later visit fixes what the ledger, the human's rejection or the merge conflict names.

## 3. Traceability

| Element | Kind | Covers | Why it exists / why nothing simpler |
|---|---|---|---|
| `supervisor` | role (lead) | R1, R6, R14 | Starts one run of `task` per Jira key the human names, in the human's language; answers blocked workers; brings the human to the gates. |
| `analyst` | role | R5, R7, R11, R12 | Takes the task in Jira and runs the design dialogue with the human; writes documents only, never code. |
| `developer` | role | R4, R8, R12, R13 | Builds from the approved artifacts with a fresh context; fixes what the review, a rejection or a conflict names. |
| `reviewer` | role | R8, R12, R13 | A fresh look the developer cannot give itself: fixes and cleanups introduce defects (dump §5 pattern 3). Read-only on code. |
| `integrator` | role | R9, R10, R11, R12 | Other rights (merge, Jira writes) and a fresh context after the long review loop; follows the merge routine with its known traps. |
| `task` | flow | R1, R4 | One run per task; LADO keeps its state, so it resumes after any stop. |
| `task.design` | work step | R3, R5, R7, R11 | Takes the task (assign, In Progress), checks it is still relevant (`dropped` otherwise), proposes the scale, writes the openspec change, and for full the design doc and plan; names the target branch (base or epic). Produces `design`. |
| `task.design_ok` | gate | R5, R6 | Code starts only on the human's approval of scope, scale and design. Reads `design`. |
| `task.build` | work step | R8, R11, R13 | TDD and the change; the project's checks; push; the pull request, its Jira link, Code Review on the last PR. `redesign` when the work needs a scope change. Reads `design`, `ledger`. Produces `build-report`. |
| `task.review` | work step | R8, R13 | Local review pass, external round with a delta, findings ledger, check against the spec; `max_visits: 5`. Reads `design`, `build-report`. Produces `ledger`. |
| `task.archive` | work step | R3, R9, R10 | Archives the openspec change inside the PR, validates the specs, drafts the Jira comment. Reads `design`, `ledger`. Produces `merge-report`. |
| `task.merge_ok` | gate | R6, R10 | The merge to the base branch is hard to undo (stage deploys from it on Clens). Reads `ledger`, `merge-report`. |
| `task.merge` | work step | R10, R11 | Brings the base in, merges with a merge commit, checks by state, cleans up, Jira label and comment; `conflicts` back to build when the base changes the code. |
| `sdlc-settings` | skill | R2, R14 | The format of `.lado/sdlc.md` and how every role reads it; one place instead of five roles. |
| `openspec-changes` | skill | R3, R9 | How to create, validate and archive a change compatibly with the project's archive (analyst, developer, integrator). |
| `review-loop` | skill | R8, R13 | The human's `pr-converge` routine, provider-neutral: passes, verdicts, ledger, delta comment, stop rule, false-green traps; its references hold the review lenses (breadth, precision, cleanup), written in the kit's own words from the review tools the human uses now (session artifact with their texts, round 6), never naming one CLI's commands. Project-specific traps (Clens's `.claude/pr-converge.md`) belong in the project's `.lado/sdlc.md`, not here. |
| `engineering-rules` | skill | R13 | The history's general lessons (red test first, checks that can fail, evidence as numbers, git safety), shared by developer and reviewer. |
| superpowers skills (brainstorming, writing-plans, test-driven-development, systematic-debugging, verification-before-completion) | dependency skills | R5, R7, R8 | The methods Comet Classic used; published, so declared in `dependencies.skills`, not copied. |
| `grilling` | dependency skill | R7 | The human's grilling step at design. |
| `tracker` (kit `tracker-jira-server`) | skill of another kit | R11 | Jira through one script; the session adds that kit, the roles name the skill. |

Reverse check:
- R1: `supervisor`, `task`. R2: `sdlc-settings`. R3: `task.design`, `openspec-changes`,
  `task.archive`. R4: `task`, `developer`, the four roles' fresh contexts. R5: `analyst`,
  `task.design`, `task.design_ok`. R6: `task.design_ok`, `task.merge_ok`, `supervisor`.
  R7: `analyst`, `task.design`, brainstorming, `grilling`. R8: `reviewer`, `task.review`,
  `task.build`, `review-loop`. R9: `task.archive`, `openspec-changes`. R10: `integrator`,
  `task.archive`, `task.merge_ok`, `task.merge`. R11: `analyst`, `task.design`, `task.build`,
  `task.merge`, `tracker`. R12: the four roles. R13: `engineering-rules`, `review-loop`,
  `developer`, `reviewer`. R14: `sdlc-settings`, `supervisor`. R15: section 5.

## 4. Complexity budget

Planned, counted by hand; the author replaces it with the script's output.

| Measure | Value | Zone | Reason, when not green |
|---|---|---|---|
| Worker roles (not supervisor) | 4 | yellow | R12, R4: the human asked for specialised roles; a run keeps one worker per role, so with three roles the developer would carry the whole review loop's context into archive and merge, and merging and Jira writes are rights the developer does not otherwise need. |
| Work steps in `task` | 5 | green | |
| Gates in `task` | 2 | green | |
| Words in the longest role prompt | ≤ 800 planned | green | |
| Own skills | 4 | green | |
| MCP servers | 0 | green | |

## 5. Change log

| Date | Version | Change | ← Fact (session, run, metric or report) |
|---|---|---|---|

Plan of versions (R15), revisited after v1 works on Clens:
- **v1** (this blueprint): R1–R15, flow `task`.
- **v2**: ranking the Jira backlog; the QA hand-off (release) as its own flow; checking the
  change on a preview environment with e2e as part of the review; what v1's use shows.
- **Later, if needed**: from the reference's SDLC process — checking that the plan covers
  every acceptance criterion of the task; HTML reports at the gates.
