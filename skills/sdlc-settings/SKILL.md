---
name: sdlc-settings
description: >-
  The project's settings for the sdlc kit in `.lado/sdlc.md` (languages, branches, Jira
  words and label, checks, the external reviewer, extra instructions, review traps), how
  each role reads them, how to talk to the human, and when an agent may write to Jira.
  Use at the start of every step, and before any message to the human or any Jira write.
  Jira's own settings (project key, statuses) are the tracker's `.lado/tracker.yaml`.
---

# Project settings: `.lado/sdlc.md`

Everything a project differs in lives in one file of its repository, `.lado/sdlc.md`,
written by the human. Read it whole at the start of your step: its **Extra
instructions** change how the kit works on this project and win over this kit's
defaults, except over a gate or the rules in "Writing to Jira".

When the file is missing, or lacks a section your step needs, stop and send the
supervisor the section's name with `send_message`; never fill it in with a guess. The
supervisor drafts a missing file with the human from `${SKILL_DIR}/sdlc.example.md`.

## Sections

| Section | What it holds | Who needs it |
|---|---|---|
| **Languages** | the language of the chat with the human, of pull requests and their comments, of Jira comments, of commit messages and of the openspec documents | everyone |
| **Git** | the remote, the base branch, how epic branches are named, the pull request branch name for each scale, the code host and its command-line tool | analyst, developer, integrator |
| **Jira** | the tracker's status words for "in progress" and "review", the label for "waiting for release", the issue types the routine skips, the merge comment's template | analyst, developer, integrator |
| **Checks** | the commands that must pass, where each runs, what is excluded from a normal round and why, what CI proves | developer, reviewer, integrator |
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

## Writing to Jira

Reading Jira is always fine. You write only what your step's routine names, on the run's
task, through the skill `tracker`, and read the task back with `get` after each write:
an exit code is not proof that Jira took it. Creating a new task, or any write the
routine does not name, needs the human's explicit "yes, create" for that very action;
having shown them a draft or asked "confirm?" earlier is not that yes. Skip the routine
for an issue type **Jira** lists as skipped, and say so in your note.

Until `tracker` can assign a task or set a label on an existing one, the human does both
by hand: the analyst asks them to assign the task, the integrator to set the label.
