---
name: sdlc-settings
description: >-
  The project's settings for the sdlc kit in `.lado/sdlc.md` (languages, branches, the
  tracker's label and skipped issue types, checks, the external reviewer, extra
  instructions, review traps), how each role reads them, how to talk to the human, and
  how an agent writes to the tracker through the skill `tracker`. Use at the start of
  every step, and before any message to the human or any tracker write.
---

# Project settings: `.lado/sdlc.md`

Everything a project differs in lives in one file of its repository, `.lado/sdlc.md`,
written by the human. Read it whole at the start of your step: its **Extra
instructions** change how the kit works on this project and win over this kit's
defaults, except over a gate or the rules in "Writing to the tracker".

When the file is missing, or lacks a section your step needs, stop and send the
supervisor the section's name with `send_message`; never fill it in with a guess. The
supervisor drafts a missing file with the human from `${SKILL_DIR}/sdlc.example.md`.

## Sections

| Section | What it holds | Who needs it |
|---|---|---|
| **Languages** | the language of the chat with the human, of pull requests and their comments, of tracker comments, of commit messages and of the openspec documents | everyone |
| **Git** | the remote, the base branch, how epic branches are named, the pull request branch name for each scale, the code host and its command-line tool | analyst, developer, integrator |
| **Tracker** | the label for "waiting for release", the issue types the routine skips, the merge comment's template | analyst, developer, integrator |
| **Checks** | the commands that must pass, where each runs, what is excluded from a normal round and why, what CI proves, how long to wait for CI | developer, reviewer, integrator |
| **External reviewer** | how to request a round, how to tell it finished, a time ceiling, the round cap | reviewer |
| **Review traps** | the project's known false greens and pitfalls, one per entry: symptom, how to spot it, how to get round it | developer, reviewer |
| **Extra instructions** | free text that changes the kit's work here, by role or for all | everyone |

A check command that no longer exists, or a trap that is no longer true, is a finding:
name it in your report so the human fixes the file.

## Talking to the human

- Write everything the human reads in the chat language of **Languages**: messages,
  questions, notes, artifacts meant for them, and short progress lines too.
- What the human must read before answering goes into the same message as the question
  (the body of `send_message`, the `details` of `ask_human`, or an attached artifact),
  never into an earlier message: a question that arrives without its material gets an
  answer without a decision.
- Name things in plain words, not in internal names: what the user of the product will
  see, not "variant A2".

## Writing to the tracker

You reach the tracker only through the skill `tracker`, asking it for each action by its
meaning: read the task with its comments, move it to a status, post a comment, link a
URL. Name no tracker's commands, settings files or markup yourself, and use them only as
the skill `tracker` says: the project may use any tracker.

Reading is always fine. You write only what your step's routine names, on the run's
task. A reported success is not proof that the tracker took it, so check each write by
what `tracker` gives back for it:
- a comment by the id `tracker` reports, among the task's comments; when it reports no
  id, find the comment by `tracker`'s mark as the newest in your name, and say so in
  your note;
- a status as the task now shows it, by meaning;
- an assignee or label as `tracker` reports it after the write, or its "already so";
- a write the task does not show (a link) by `tracker`'s own result; say so in your note.

Never compare the text you sent with the text the tracker keeps: it formats it and adds
its mark. Creating a new task, or any write the routine does not name, needs the human's
explicit "yes, create" for that very action; having shown them a draft or asked
"confirm?" earlier is not that yes. Skip the routine for an issue type **Tracker** lists
as skipped, and say so in your note.

- **Statuses** go by meaning: "in progress", "review". Ask `tracker` to move the task to
  the status that means it. When you are unsure which one that is, ask `tracker` which
  statuses the task can move to and pick by meaning; when none fits, ask the human.
- **Comments** are written in Markdown as the skill `tracker` asks text to be written,
  and handed to it as they are; the tracker skill formats them for its tracker.
- **A link** to a pull request: when `tracker` cannot link a URL, post it as a comment.
- **Assigning the task or labelling it**: when `tracker` can, do it; otherwise ask the
  human in your note to do it by hand. Assign to the account `tracker` works as: ask
  `tracker` which account that is and compare it with the task's assignee. Only when the
  task is assigned to another account, ask the human first.

## The task key as a name

Runs and openspec changes start with the task key as a name, and branches carry it as
`<key>`. Convert the key yourself, in this order: drop everything up to the last `/`,
lowercase it, turn every other character that is not a letter or digit into `-`, collapse
repeated `-` and trim it from both ends, and put `task-` before a key of digits only
(`ABC-123` → `abc-123`, `#123` → `task-123`, `owner/repo#123` → `repo-123`). Every role
converts the same way, so the run, the change and the branch get the same name.
