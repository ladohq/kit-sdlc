# Brief: a process kit for the human's day-to-day development (working name `comet`)

Written by the supervisor of LADO session `kit-tracker-jira-server` on 2026-10-08, from
the human's messages there, so that the session that builds this kit starts from what is
already known. It is input for the interview, not a decision: the human settles everything
below in the `design` step of `create`.

## What the human asked for

The next kit after `tracker-jira-server`, needed for work on a real project. In their
words: "Я уже длительное время работаю с проектом Clens и использую устоявшийся подход,
который мне нравится. Я планирую собрать опыт работы с проектом с твоей помощью и
попробовать перенести этот опыт в новый кит. Так же, я бы хотел подсмотреть что-нибудь
интересное у референса."

So the interview takes the branch **Transfer** (`kit-interview`): the process exists and
the kit should follow it.

## Known so far

- **The process.** The human's long-standing way of working on the project Clens. It is
  built on the framework **Comet**, which uses **superpowers** (obra/superpowers skills)
  and **openspec** to keep an archive of the specifications of implemented features.
- **A reference** to borrow ideas from: not named yet; ask which one (a repository, a
  document) and read it with the rest of the material.
- **The tracker.** The project's tasks live in a corporate Jira Server 8.13. The kit
  `tracker-jira-server` 1.0.0 (github.com/ladohq/kit-tracker-jira-server, in the official
  marketplace's pull request #6) gives roles the skill `tracker`; this kit's roles say
  "use the tracker skill" and the session adds both kits:
  `lado start <repo> --kit <this kit> --kit tracker-jira-server`. Its write rule: agents
  read freely and write to Jira only when their role, their step or the human asks, so
  this kit's roles and steps say who moves a task, comments or links a commit, and when.
- **Name.** `comet` is a working name for the folder and the run; the human settles the
  kit's name during the design step. Should the kit be universal or for Clens only?
  `tracker-jira-server` was made universal; ask.

## Material to ask for first

- where Comet lives (repository or folder) and which parts of it are binding;
- the Clens repository's `CLAUDE.md` / `AGENTS.md`, its own skills and commands;
- how superpowers and openspec are set up there, and a sample of the spec archive;
- how one task goes today: from a Jira task to merge, who does what, the Jira statuses,
  who checks the work and against what;
- the reference.
