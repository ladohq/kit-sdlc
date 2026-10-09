# sdlc settings

The project's settings for the LADO kit `sdlc`. The human keeps this file; agents read it
at the start of every step.

## Languages

- Chat with the human: English
- Pull request description and comments: English
- Tracker comments: English
- Commit messages: English, `<type>(<KEY>): <subject>`; the body says why, not what;
  code and documentation in separate commits
- openspec documents and design docs: English

## Git

- Remote: `origin`
- Base branch: `main`
- Epic branches (when the tracker has epics): `epic/<key>-<slug>`
- `<key>` is the task key as a name (`openspec-changes`, "Layout"): `ABC-123` → `abc-123`
- Pull request branch: full `feat/<key>-<slug>`, hotfix `hotfix/<key>-<slug>`,
  tweak `fix/<key>-<slug>`; no date in the name
- Code host: GitHub, command-line tool `gh`

## Tracker

- Waiting for release label: `waiting_for_release`
- Skipped issue types: none
- Merge comment (Markdown):

  ```markdown
  **Merge report for PR #<n>**

  **For QA: what to check**
  - <2-4 points: visible changes, scenarios, edge cases, regression risks>

  **For developers: what was done**
  - <by layer: key decisions, new API fields, migrations yes/no>
  ```

## Checks

| Command | Run from | Notes |
|---|---|---|
| `make lint` | root | |
| `make test` | root | report suites and tests as numbers |
| `openspec validate --specs --strict` | root | blind to contradictions between capabilities: grep `openspec/specs/` |

- Excluded from a normal round: `make test-live` (vendor quota)
- What CI proves: CI runs the tests and the linter on every push
- CI time ceiling: 30 minutes

## External reviewer

- Request: a pull request comment that starts with `@reviewer-bot` and holds the delta
- Finished: the bot's comment contains `Review finished`
- Time ceiling: 25 minutes
- Round cap: 3

## Review traps

- <symptom> — <how to spot it> — <how to get round it>

## Extra instructions

- <free text, by role or for all>
