# Отчёт о ките: sdlc 0.1.1

- Дата: 2026-10-08
- Кит: worktree рана `improve/sdlc` (`.lado/worktrees/kit-comet/improve-sdlc`), путь дан задачей. В `kit.yaml` пока стоит `version: 0.1.0`: версию поднимают при релизе, отчёт назван по плановой версии из плана.
- Коммит: 1422184
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.1.0-2026-10-08.md` (артефакт `assessment`). База — коммит из плана, 1b8b2a9: `git diff 1b8b2a9 -- kit.yaml README.md BLUEPRINT.md agents flows skills` (6 файлов, +61 −44).
- Проходы: три независимых суб-агента, у каждого рубрика, папка кита, зависимые скилы и diff. Начинали с разных мест: 1 — роли, 2 — флоу по шагам, 3 — скилы. Вырезанные правила я проверил сам (`git diff --word-diff` и таблица автора). Полный проход (Re-evaluation 5) по всем шести файлам diff сделал четвёртый суб-агент. Каждую его находку я подтвердил в файлах. Из находок одного прохода 2 отброшены, 3 оставлены как подтверждённые.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4). analyst.md — 799 слов из 800 зелёных |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 8 находок в изменённом тексте (0 high, 4 medium, 4 low); 6 из 12 критериев без находок. Ещё 4 в «Пропущено раньше» (3 medium, 1 low) |
| Охват | повторная оценка изменённого текста и полный проход по 6 файлам, которые затронул diff |
| Правило остановки | не выполнено: 0 high, 4 medium. Это совет для гейта релиза, не блок |

Счёт относится только к тому, что указано в строке «Охват»: он не сравним со счётом полной оценки.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
expects skills: tracker
```

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.1.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 5 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 799 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 578 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 622 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 392 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 676 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.1.1-2026-10-08/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

1. Вернуть аналитику явный канал к человеку: при сокращении он потерян (F7.2).
2. Указать, куда пушит интегратор: голый `push` не попадает в ветку PR (F2.6).
3. Однозначно сказать, с какими правками едет minor `fix`, и что с ним при сходимости (F3.8).
4. Вопрос про возврат статуса из In Progress задать до `dropped`, а не в записке конечного состояния (F12.3).
5. Убрать расхождение между `archive` и `openspec-changes` о задаче, не отмеченной с причиной (F3.10, пропущено раньше).

## Находки

### 1. Границы ролей

Права не изменились. Интегратор не трогает запушенную историю: «already on the remote: leave it, never reset or rewrite it.» (`agents/integrator.md:49`). Супервизор при отмене спрашивает человека, прежде чем что-то закрыть (`agents/supervisor.md:71`).

### 2. Передача между шагами

- **F2.6** [medium] `agents/integrator.md:45`
  > whose cause is outside the work follows "A red check". With every check green, push.
  Это голый `push`. Ветка рана — ветка LADO, а PR живёт на ветке, имя которой задаёт дизайн. Разработчик пушит явно: «`git push origin HEAD:refs/heads/<branch>` (no force, ever).» (`agents/developer.md:49`), без upstream. Поэтому голый `git push` интегратора либо упадёт, либо уйдёт в другую ветку. Тогда CI PR не увидит мерж цели, и шаг 2 будет ждать старый зелёный CI. Строка изменена в этом diff, хотя голый push был и раньше («Otherwise push»).
  Fix: «With every check green, push it as the developer does: `git push origin HEAD:refs/heads/<the design's branch>`, never force»; то же в «Archive» шаг 2 (`:25`). Passes: full pass, confirmed

- **F2.5** [low] `agents/analyst.md:33`
  > supervisor its name and the target, and wait. Name the pull request's branch by the
  Сказано «wait», но не сказано, что делать после ответа. Файл попадёт в ветку рана только после нового `git fetch origin` и `git reset --keep origin/<target>`. Без этого аналитик пойдёт дальше без файла, и следующий шаг остановится на «When the file is missing» (`skills/sdlc-settings/SKILL.md:18`).
  Fix: «…and wait; once it answers, fetch, put the branch on the target again and check again.» Учтите: analyst.md на 799 словах из 800 зелёных. Passes: 3/3

### 3. Готовность и исходы

- **F3.8** [medium] `skills/review-loop/SKILL.md:52`
  > at the merge gate. A refuted finding: `drop`. A minor `fix` rides with the next round's
  «The next round's fixes» читается двояко: правки, которые этот раунд отправляет назад, или правки следующего раунда. При втором чтении minor `fix` из раунда k ждёт. Если раунд k+1 сходится, а cleanup ничего не находит, этот minor так и остаётся с fate `fix`: его не исправили и не отклонили. Правило отклонения покрывает только финальный раунд: «Minor findings do not start another round: a minor finding of the final round gets» (`:128`). Ревьюеры поступят по-разному.
  Fix: «A minor `fix` goes to the developer with the fixes of its round; when its round sends nothing back, with the next fixes, or it gets `decline` when the loop converges first.» Passes: 1/3, confirmed (`:52` и `:128`; разработчик правит «each ledger finding whose fate is "fix"», `agents/developer.md:62`, только когда ревью вернуло `changes`)

- **F3.9** [low] `agents/integrator.md:50` (путь `merge` → `conflicts` → `build`)
  > work, report `conflicts` with the failing job in note_body; the developer fixes on
  Новый путь «красный CI после запушенного мержа» ведёт в `build`. Там `do` велит «and resolve it), as your role describes ("Fixing").» (`flows/task.yaml:52`), то есть влить цель, а она уже влита. «Fixing» разработчика описывает только конфликт: «the run's branch, resolve it, and run the checks again.» (`agents/developer.md:67`). Что чинить упавшую проверку поверх ветки, сказано лишь в роли интегратора, а её разработчик не читает.
  Fix: в «Fixing» разработчика: «For a red CI from `merge`, fix the failing job the note names on top of the branch, as a finding.» Passes: 1/3, confirmed (три цитаты выше)

### 4. Независимая проверка

Без изменений. Пуш мерж-коммита в ветку PR идёт только в `merge`, после гейта `merge_ok` (`flows/task.yaml:97-103`). Ревью делает отдельная роль.

### 5. Противоречия

- **F5.7** [low] `BLUEPRINT.md:175` (строка `task.merge`); то же в журнале изменений, `:216`
  > a red CI after the push leaves the pushed merge and goes to the supervisor
  В флоу красный CI с причиной в работе — это `conflicts` (back to build): «note_body. A CI red for a cause in the work is `conflicts` too, but the merge is» (`flows/task.yaml:115`). К супервизору идёт только причина вне работы. `--compare` этого не видит, потому что расхождение в прозе, а не в скелете.
  Fix: «a red CI after the push leaves the pushed merge: `conflicts` for a cause in the work, the supervisor otherwise» в обеих строках. Passes: full pass, confirmed

Противоречие F5.5 снято. Остальное согласовано: правило про minor (`review-loop:52-55`) сходится с `:128` и с «Fixing» разработчика.

### 6. Дублирование

Скрипт похожих абзацев ничего не нашёл. В `merge` `do` повторяет условия исходов и указывает на роль: «already pushed: leave it (your role, "Merge").» (`flows/task.yaml:116`). Это короткий указатель, а не копия правила. Находку одного прохода об этом я отбросил.

### 7. Когда звать человека

- **F7.2** [medium] `agents/analyst.md:16-17` в базе 1b8b2a9 (вырезанное правило), сейчас `agents/analyst.md:16`
  > -Read `.lado/sdlc.md` first (`sdlc-settings`). Talk to the human with `send_message` and
  > -`ask_human` as `sdlc-settings` says ("Talking to the human").
  Вырезанные слова были единственным местом, где сказано, что аналитик сам говорит с человеком через `send_message` и `ask_human`. Остался текст «Talk to the human as `sdlc-settings` says» (`:16`). А «Talking to the human» в `sdlc-settings` упоминает эти инструменты лишь как места для материала: «(the body of `send_message`, the `details` of `ask_human`, or an attached artifact),» (`skills/sdlc-settings/SKILL.md:42`). Канала там нет. При этом разработчик и ревьюер ходят к человеку через супервизора (`agents/developer.md:35-36`, `agents/reviewer.md:38-39`), а по правилу LADO с человеком говорит только лид, если кит не сказал иначе. Одни аналитики будут вести диалог дизайна напрямую, другие — через супервизора, каждый раунд вопросов. Таблица автора называет это «pointer kept», но указатель ведёт к теме, а не к правилу.
  Fix: вернуть «with `send_message` (to the human) and `ask_human`» или одну строку «You talk to the human directly; the other workers go through the supervisor.» Passes: cut-rule check, full pass, confirmed (моя проверка вырезанного это пропустила)

На лимите цикла и при отмене путь к человеку есть (`agents/supervisor.md:51-53`, `:69-71`).

### 8. Циклы на повторном визите

- **F8.2** [low] `BLUEPRINT.md:155`
  > `review` visits: up to 3 external rounds, the cleanup round and the final round; on a
  Эта фраза давала 5, ровно под старый `max_visits: 5`. После изменения скелет говорит 8 (`BLUEPRINT.md:136`), а строка трассировки и `do` перечисляют ещё первый локальный раунд и возврат после `merge_ok` или `conflicts`. Сама строка в diff не попала, но устарела именно из-за этой правки, поэтому она здесь, а не в «Пропущено раньше».
  Fix: переписать её как строку `task.review` в разделе 3. Passes: 1/3, confirmed (`BLUEPRINT.md:136` «max_visits: 8» и `:173`)

Остальное в порядке: `max_visits: 8` в флоу и скелете, `do` описывает повторный визит («mark each of its findings RESOLVED or STILL OPEN first», `flows/task.yaml:71`), а на лимите супервизор советует `continue` или `cancel`. Находку одного прохода о том, что должен делать раунд возврата после исчерпанного предела внешних раундов, я отбросил: `review-loop` на этот случай велит спросить человека (`:131-132`), и вреда в файлах я не нашёл.

### 9. Краткость и «зачем»

Вырезано пояснение, а не правила: «Two or three rounds are usual» убрано, правило о дроблении задачи осталось (`agents/analyst.md:54-55`). Новые правила с причиной: «A red CI comes after the push, so the merge commit is» (`agents/integrator.md:48`). analyst.md — 799 слов: любое добавление (F2.5, F7.2) сделает его жёлтым. Это решение автора, не находка.

### 10. Описания скилов

Без изменений: описания и списки `skills:` не менялись.

### 11. Нейтральность к провайдеру

Новые команды — только git (`git fetch`, `git reset --keep`). `gh pr merge` был и раньше, с «or the settings' tool's equivalent».

### 12. Безопасность и рамки

- **F12.3** [medium] `flows/task.yaml:30` (state `design`) — STILL OPEN (см. «Прежние находки»)
  > committed. When you already moved the task to In Progress, ask the human in the
  Вопрос уходит в записку исхода `dropped`, а это конечное состояние: следующего шага нет. Супервизор при `dropped` пересказывает только `design`: «- **At `dropped`**, tell the human why, from the run's `design`. Its branch holds no» (`agents/supervisor.md:67`). Писать в Jira он может только по просьбе человека. Задача может остаться In Progress — тот же вред, что в прошлом отчёте. Исправление в точности следует моему прошлому совету, и этот совет был ошибкой. Проходы оценили низко, низко и средне; взято medium.
  Fix: в `design`: «When the human agrees to drop it and you already moved the task, ask them in the same message whether to move it back, and do it on their yes (`sdlc-settings`, "Writing to Jira").» Passes: 3/3

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: «whose cause is outside the work follows "A red check". With every check green, push.» (`agents/integrator.md:45`); `engineering-rules` «A red check» (`:38-45`); красный CI с причиной вне работы уходит супервизору (`agents/integrator.md:51-54`). Остаток — F3.9 |
| 2. Работа вне флоу, мерж без гейта | Закрыта: пуш и мерж только в `merge` после `merge_ok`; при отмене «Ask before you close anything» (`agents/supervisor.md:70-71`) |
| 3. Путь вне worktree рана | Закрыта: файлы настроек проверяются на ветке рана после reset (`agents/analyst.md:31-33`). Остаток — F2.5 |
| 4. Вердикт без порога серьёзности | Закрыта: «it is not something to fix for the external round or the stop criterion» (`skills/review-loop/SKILL.md:53`). Двусмысленность — F3.8 |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: «(leave the plan's "For agentic workers" header out).» (`agents/analyst.md:67`), визуальный компаньон исключён (`:63`) |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Единственная жёлтая мера (Worker roles = 4) обоснована (R12, R4). Новых ролей, шагов, гейтов и скилов нет. Прозаические расхождения блупринта с китом — F5.7 и F8.2.

## Прежние находки

- **F5.5** RESOLVED: `agents/integrator.md:48-49` «A red CI comes after the push, so the merge commit is / already on the remote: leave it, never reset or rewrite it.»; `flows/task.yaml:113-116` ограничивает «push nothing» локальной проверкой.
- **F3.6** RESOLVED: `skills/review-loop/SKILL.md:52-55` «A minor `fix` rides with the next round's fixes: it is not something to fix for the external round or the stop criterion.» Новая двусмысленность — F3.8.
- **F2.4** RESOLVED: `agents/analyst.md:31-33` «Check that `.lado/sdlc.md` and `.lado/tracker.yaml` are on it; when one is missing, send the supervisor its name and the target, and wait.» Остаток — F2.5.
- **F8.1** RESOLVED: `flows/task.yaml:63` и `BLUEPRINT.md:136` «max_visits: 8»; фраза о визитах переписана (`flows/task.yaml:72-74`). Остаток в прозе блупринта — F8.2.
- **F12.2** RESOLVED: `agents/supervisor.md:69-71` «- **At cancel**, tell the human what is left: the pull request and whether it holds an archive commit, the task's Jira status, the run's worktree and workers.»
- **F3.7** RESOLVED: `agents/integrator.md:45` «With every check green, push.» Куда пушить — новая F2.6.
- **F5.6** RESOLVED: `agents/analyst.md:67` «(leave the plan's "For agentic workers" header out).»; совпадает с шаблоном writing-plans `SKILL.md:61`.
- **F12.3** STILL OPEN: текст добавлен (`flows/task.yaml:30-31`), но вопрос попадает в записку конечного состояния, и по нему никто не действует. См. раздел 12.

Список автора в `build-report` (8 из 8 «fixed») сверен с файлами: 7 подтверждены, F12.3 — нет.

## Вырезанные правила

| Вырезанное правило (файл:строка в базе) | Где оно теперь |
|---|---|
| `agents/analyst.md:16-17` «Talk to the human with `send_message` and `ask_human`» | **Потеряно** — F7.2. `sdlc-settings:42` называет эти инструменты только как место для материала, а не как канал аналитика |
| `agents/analyst.md:52-53` «Two or three rounds are usual» | Пояснение, не правило; правило о дроблении осталось (`agents/analyst.md:54-55`) |
| `agents/analyst.md:65-66` «mock-ups as artifacts, not a visual companion» | `agents/analyst.md:61-63` «written as an artifact (`mockup-<topic>.html`)… never as ASCII drawings or a visual companion.» |
| `agents/analyst.md:21-29` шаги 1–3 (формулировки) и метка «The branch.» | Те же пункты, правила на месте (`agents/analyst.md:21-25`) |
| `agents/integrator.md:44-45` «Otherwise push» | `agents/integrator.md:45` «With every check green, push.» (F3.7) |
| `agents/integrator.md:48` «A CI red for a cause in the work is `conflicts`, with the failing job in note_body.» | `agents/integrator.md:49-50` |
| `agents/supervisor.md:24-26` проверка `git cat-file -e origin/<base>:<path>` супервизором | Перенесена к аналитику на цель рана (`agents/analyst.md:31-33`), что покрывает и эпик-ветку; указатель — `agents/supervisor.md:24-25` |
| `flows/task.yaml:71-73` фраза о визитах | `flows/task.yaml:72-75` (F8.1) |
| `flows/task.yaml:111-113` «a check or the pull request's CI is red … undo the merge … push nothing» | `flows/task.yaml:113-116`: локальная проверка — откат; CI — `conflicts` без отката (F5.5) |

## Пропущено раньше

- **F3.10** [medium] `flows/task.yaml:92` (state `archive`)
  > risk. When the change cannot be archived as it is (a task not checked, a delta that
  Скил допускает неотмеченную задачу с причиной: «1. Every task of `tasks.md` is checked, or its line says why not.» (`skills/openspec-changes/SKILL.md:70`). `do` о причине молчит. Один интегратор заархивирует, другой ничего не заархивирует и отправит к `merge_ok`.
  Fix: «a task not checked without a reason». Passes: full pass, confirmed

- **F12.4** [medium] `agents/supervisor.md:67`
  > - **At `dropped`**, tell the human why, from the run's `design`. Its branch holds no
  `dropped` возможен и на повторном визите `design`, после `redesign` из `build`: «outcomes: {ready: review, redesign: design}» (`flows/task.yaml:60`). К этому моменту PR уже открыт и запушен. Супервизору сказано, что работы нет, и он завершит воркеров с `discard=true`, ничего не сказав про PR и удалённую ветку. Новый пункт «At cancel» перечисляет как раз эти остатки, но только для отмены.
  Fix: в «At `dropped`»: «when the run opened a pull request, tell the human what is left as at cancel». Passes: 1/3, confirmed (`flows/task.yaml:60` и `agents/supervisor.md:67-68`)

- **F11.1** [medium] `skills/review-loop/SKILL.md:113`
  > fresh agent given only the diff and the pull request's description, and label the round
  Запасной вариант, когда внешнего ревьюера нет, предполагает, что ревьюер может сам запустить свежего агента. Не каждый CLI это умеет, а `spawn_worker` в LADO есть только у супервизора. У линз альтернатива есть: «A lens is one question asked of the whole diff. Run each lens as its own pass, alone or» (`references/lenses.md:3`), а здесь — нет.
  Fix: «…or, when you cannot start one, ask the supervisor for a fresh worker» и сказать, что делать, если и это невозможно. Passes: full pass, confirmed

- **F5.8** [low] `agents/integrator.md:34`
  > will do in git and in Jira (base branch: comment and the waiting-for-release label; epic
  `merge-report` обещает человеку, что `merge` поставит метку. Но метку ставит человек вручную, интегратор только просит: «human in your note to set the waiting-for-release label by hand (`sdlc-settings`,» (`agents/integrator.md:62`).
  Fix: «base branch: the comment, and a request to you to set the waiting-for-release label by hand». Passes: full pass, confirmed

## Оставлено планом

План ничего не оставляет («Leave: none»).

## Вопросы к человеку

Нет вопросов.
