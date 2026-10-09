# Отчёт о ките: sdlc 0.2.1

- Дата: 2026-10-09
- Кит: worktree рана `improve/sdlc-3` (`.lado/worktrees/kit-sdlc/improve-sdlc-3`), путь дан задачей
- Коммит: 60d650c
- Оценил: критик kit-builder (слои a и b)
- Режим: полная оценка (шаг `assess` рана `improve/sdlc-3`), не повторная. Формат LADO и kit-builder изменился (`expects.commands`), поэтому кит оценён заново, а не по прошлому отчёту. Прежний текст этого файла — отчёт визита 2 `improve/sdlc-2` — в git, коммит b0573e6; его находки F3.7 и F5.3, названные задачей, ниже в разделе «Низкие находки из задачи». Находки отчёта sdlc 0.2.0 — коммит 29a4c8f.
- Проходы: три независимых суб-агента. Каждому дали рубрику, `lado-kit-format`, папку кита и папки зависимых скилов: superpowers v6.4.1 (5 скилов), grilling из mattpocock-skills v1.2.3, `tracker` из tracker-jira-server v1.1.0. Все скилы найдены и прочитаны. Старые отчёты, `BACKLOG.md` и `BRIEF.md` агентам не давали. Порядок обхода: проход 1 — kit.yaml и роли; проход 2 — флоу по состояниям; проход 3 — скилы, потом роли. Потом критик свёл находки и сверил каждую цитату с файлом (`grep -nF`). Одиночных находок: 2. Одна оставлена как подтверждённая (F6.3), одна отброшена: повтор правила выбора проходов в `agents/reviewer.md:31`. Там роль добавляет своё исключение для hotfix, а `review-loop` сам отсылает к роли («the role and the design's review depth decide»), так что это указатель, а не копия. Ещё две находки разных проходов слиты в F2.2: «у интегратора нет `review-loop`» (проходы 1 и 3, F10.2) и «итоговый отчёт пишется только при сходимости». Уровни взяты по максимуму.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | нарисован 1; совпадает со скелетом блупринта |
| b. Рубрика | 9 находок (2 high, 3 medium, 4 low); 7 из 12 критериев без находок |
| Охват | полная оценка всего кита, 14 файлов: `kit.yaml`, `README.md`, `BLUEPRINT.md`, 5 ролей, `flows/task.yaml`, 4 `SKILL.md` и их файлы (`references/lenses.md`, `references/false-greens.md`, `sdlc.example.md`) |

Счёт относится только к тому, что указано в строке «Охват». Со счётом повторной оценки его сравнивать нельзя.

### `lado kits check .` (LADO 0.31.0)

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
expects skills: tracker
```

Expects: `expects skills: tracker`. Строки `expects commands` нет: кит не объявляет ни одной команды (см. F10.1).

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.2.1

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 5 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 797 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 606 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 646 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 419 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 753 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.2.1-2026-10-09/task.svg)

```
same as the plan in BLUEPRINT.md
```
(код выхода 0)

## Исправить в первую очередь

1. Провал `openspec validate --specs --strict` или grep противоречий после `openspec archive`: вернуть незакоммиченные файлы, ничего не коммитить, сказать об этом в начале `merge-report`. Grep противоречий лучше делать до архива (F3.1)
2. Одно правило для `merge_ok`: принять можно только несошедшееся ревью. Неотмеченная задача или дельта, противоречащая живой спеке, возвращается в `build`. Вопрос гейта говорит, что «approve» при «nothing archived» означает принять находки, а у `merge` есть путь на случай, когда архив снова не прошёл (F3.2)
3. `merge` читает `ledger` (F2.1). Если в ledger нет итогового отчёта, интегратор пишет его сам: из строк ledger, остаточного риска и согласия человека на `merge_ok` (F2.2)
4. `expects.commands: [git, openspec]`, `dependencies.lado: ">=0.30"`, README и раздел 3 блупринта — так же (F10.1)

## Находки

### 1. Границы ролей

Без находок. Права названы в каждой роли. Ревьюер: «You are read-only on the code: you read, run checks and comment on the pull request, and» (`agents/reviewer.md:15`). Аналитик: «mock-ups; code is the developer's.» (`agents/analyst.md:14`). Супервайзер: «and keep the human informed. You change no code and write to the tracker only when the» (`agents/supervisor.md:11`). Архив и мерж только у интегратора.

### 2. Передача между шагами

- **F2.1** [medium] `flows/task.yaml:113` (state `merge`)
  > `    reads: [design, merge-report]`
  На пути «nothing archived, the human accepted the findings» шаг `merge` архивирует сам («archive first ("Archive"), then merge.», `:118`), а архив делается «with the closing report from the» ledger (`agents/integrator.md:24`). При этом `ledger` нет в `reads` шага, и к заметке гейта его не прикладывают. Интегратор пишет отчёт без источника или берёт артефакт, которого шаг не называет. Роль при этом считает ledger своим входом и в `merge` («Your inputs are the approved change (`design`), the reviewer's `ledger` and, in `merge`, your `merge-report`», `agents/integrator.md:16-17`).
  Fix: `reads: [design, ledger, merge-report]`, и так же строка `task.merge` в разделе 3 блупринта (`BLUEPRINT.md:202`). Passes: 3/3

- **F2.2** [medium] `agents/reviewer.md:49`
  > `converged, the closing report. The integrator archives from it and the human decides the`
  Ревьюер пишет итоговый отчёт только при сходимости. Шаг 4 архива требует его в `docs/superpowers/reports/` («4. Write the review's closing report (`review-loop`, "Closing report") to», `skills/openspec-changes/SKILL.md:77`). На пути, где человек принял несошедшееся ревью на `merge_ok`, отчёта нет, и никто не обязан его написать. Интегратору, который архивирует, не дан `review-loop`, где описан этот отчёт: в его `skills:` только `sdlc-settings`, `openspec-changes`, `engineering-rules`, `tracker` (`agents/integrator.md:4-8`). Описание `review-loop` при этом зовёт себя «or archive one that converged» (`skills/review-loop/SKILL.md:8`). Проходы 1 и 3 подали это отдельной находкой F10.2 [low]; она слита сюда, критерий выбран ближе к правке, уровень — максимальный.
  Fix: в «Archive» интегратора: «without a closing report in the ledger, write it from the ledger's rows, the residual risk and the human's acceptance at `merge_ok`, in the shape of `review-loop`, "Closing report"». Или добавить `review-loop` в `skills:` интегратора. Passes: 3/3

### 3. Готовность и исходы

- **F3.1** [high] `skills/openspec-changes/SKILL.md:75`
  > `3. `openspec validate --specs --strict` passes, and the grep for contradictions (above)`
  Проверка идёт после шага 2, когда `openspec archive` уже переместил файлы в worktree, но коммита ещё нет. Ни скил, ни `do` шага `archive` не говорят, что делать, если проверка красная. `do` говорит «archive nothing and say so at the top of `merge-report`» (`flows/task.yaml:99`), но к этому моменту архив уже сделан. Исход у `archive` один, `ready`, а условие готовности «`openspec validate --specs --strict` passes» (`:94`) выполнить нельзя. Интегратор оставит грязный, наполовину заархивированный worktree. После `merge_ok` → `build` разработчик не найдёт коммита архива для «revert the archive first» (`agents/developer.md:66`, «After the archive»). Противоречие с живой спекой видно только после архива, хотя `do` относит его к причинам не архивировать.
  Fix: в «Archive» скила: grep противоречий — до шага 2. В шаге 3: «When either fails, restore the files the archive changed (they are not committed yet), commit nothing, and say so at the top of `merge-report`.» Passes: 3/3

- **F3.2** [high] `flows/task.yaml:117` (state `merge`)
  > `      When `merge-report` says nothing was archived and the human accepted the findings at`
  `archive` отказывается архивировать по трём причинам: «the ledger does not show the review converged, a task not checked without a reason, a delta that contradicts a living spec». Для всех трёх он даёт одну развилку: «the human sends it back, or accepts the open findings there, at `merge_ok`» (`:97-100`). «Принять» можно только открытые находки ревью. Неотмеченная задача или противоречие — не находки: если `merge` архивирует поверх них, шаги 1 и 3 «Archive» снова красные. Исходов у `merge` только два: «outcomes: {merged: done, conflicts: build}» (`:128`). Интегратор мержит без архива (нарушая R9) или застревает. Вопрос гейта («Merge the pull request into its target branch? The integrator brings the target in,», `:108`) не говорит, что «approved» при «nothing archived» значит принять находки. Простое «approve» без комментария шаг `merge` не покрывает: он ждёт «the human accepted the findings». Проход 1 дал medium, проходы 2 и 3 — high; взят high.
  Fix: в `archive`: «only an unconverged review can be accepted at `merge_ok`; an unchecked task or a delta that contradicts a living spec the human rejects back to `build`». В `merge_ok.ask`: «approving a merge-report that says nothing was archived accepts its open findings». В `merge`: «when the archive still fails, restore, send it to the supervisor and stay in `merge`». Passes: 3/3

### 4. Независимая проверка

Без находок. Работу проверяет отдельная роль-ревьюер, а оба гейта показывают свидетельства: `merge_ok` — «    reads: [ledger, merge-report]» (`flows/task.yaml:106`), `design_ok` — «    reads: [design]» (`:37`).

### 5. Противоречия

Без находок, кроме противоречия «archive nothing» с порядком шагов скила (оно в F3.1). Аналитик отключает одобрения из зависимых скилов: «the approval, so ask for none in the chat; no implementation, no execution handoff» (`agents/analyst.md:67`). Разработчик направляет «ask your human partner» супервайзеру (`agents/developer.md:35`). Гейты остаются за человеком: «The human answers in LADO.» (`agents/supervisor.md:50`).

### 6. Дублирование

- **F6.1** [low] `flows/task.yaml:124` (state `merge`)
  > `      note_body. A CI red for a cause in the work is `conflicts` too, but the merge is`
  Строки `:121-127` пересказывают шаги 1–2 раздела «Merge» роли (`agents/integrator.md:43-57`): откат мержа до пуша, запушенный мерж не трогать, внешние причины — супервайзеру. Две копии разойдутся. Это F6.1 отчёта 0.2.0, которую задача относит к низким.
  Fix: в `do` оставить условия исходов и ссылку «(your role, "Merge")». Passes: 3/3

- **F6.2** [low] `flows/task.yaml:79` (state `review`)
  > `      limit is rare. In the visit where correctness converges, run the cleanup pass too;`
  Строки `:79-80` пересказывают критерий остановки `review-loop`: «  the cleanup leaves nothing to fix, the round that converged is the final one. An» (`skills/review-loop/SKILL.md:136`). Это F6.3 отчёта 0.2.0, которую задача относит к низким.
  Fix: заменить ссылкой «(`review-loop`, "Stop criterion")». Passes: 2/3 (проходы 2 и 3)

- **F6.3** [low] `flows/task.yaml:95` (state `archive`)
  > `      and `merge-report` holds the archive's result, the pull request's state, what the`
  Состав `merge-report` перечислен дважды, и копии уже расходятся. В `do` (`:95-97`) нет «declined findings», а в роли они есть: «Write the artifact `merge-report` whole, in the settings' chat language: the archive» … «the ledger's residual risk and declined findings.» (`agents/integrator.md:34`, `:39`). Интегратор, который сверяется с `do`, может не включить отклонённые находки, а без них человек на `merge_ok` их не взвесит.
  Fix: в `do` написать «`merge-report` holds what your role lists ("Archive")». Passes: 1/3, confirmed (обе цитаты на месте; в `do` нет слова «declined»: `grep -n declined flows/task.yaml` ничего не находит)

### 7. Когда звать человека

Без находок. Аналитик говорит с человеком сам (`agents/analyst.md:16-18`), остальные работники — через супервайзера: «A question for the human, also where a skill says "ask your human partner", goes to the» (`agents/developer.md:35`). Если цикл не сходится: «fails, ask the human with the open findings and your recommendation, and do as they say.» (`skills/review-loop/SKILL.md:146`), а при `max_visits: 8` супервайзер советует `continue` или `cancel` (`agents/supervisor.md:51-53`).

### 8. Циклы на повторном визите

Без находок. Ревьюер: «On a later visit your previous ledger is your `ledger` artifact: mark each of its» (`flows/task.yaml:75`). `design`, `build-report`, `ledger` и `merge-report` пишутся целиком на каждом визите («Write the artifact `build-report` whole on every visit», `agents/developer.md:73`). Цикл ограничен: `max_visits: 8` и два гейта.

### 9. Краткость и причины

Без находок, кроме повторов в критерии 6. Неочевидные правила объяснены, например «the command's exit code: the merge often succeeds while a local step of the tool» (`agents/integrator.md:59`).

### 10. Описания скилов

- **F10.1** [medium] `kit.yaml:12`
  > `  skills: [tracker]`
  У `expects` нет `commands`, хотя `openspec` и `git` выполняются в каждом ране на любом проекте: «The openspec CLI is a prerequisite: when `openspec --version` fails, stop and tell the» (`skills/openspec-changes/SKILL.md:19`). README требует `openspec` для всех: «You need LADO 0.29 or newer, the `openspec` CLI, the code host's CLI (e.g. `gh`) logged» (`README.md:17`). Это известная дыра 6 (a): сессия стартует без `openspec`, и ран падает только на шаге `design`. `lado kits check` не печатает строки `expects commands`.
  Fix: в `kit.yaml` добавить `commands: [git, openspec]` и `dependencies.lado: ">=0.30"`. В README: «LADO 0.30 or newer», те же команды как обязательные, CLI кодового хостинга — по настройкам проекта. В разделе 3 блупринта — строки вида «command (expects)» для `openspec` (R3) и `git`. `gh` в `expects.commands` не включать (дыра 6 (b)). Passes: 3/3

- **F10.2** [low] `agents/developer.md:50`
  > `2. When no pull request is open, open one against the design's target branch with the`
  CLI кодового хостинга правильно оставлен вне `expects.commands`, но ни одна роль не сверяет его с **Git** настроек проекта, когда читает их (`lado-kit-format`: «The kit's text checks for such a CLI against the project's settings, when it reads them»). Если CLI нет или в нём не выполнен вход, это выяснится только при открытии pull request, после всей сборки. Проход 2 отнёс это к критерию 11; взят критерий 10 — он ближе к `expects`.
  Fix: в `sdlc-settings` («Sections», **Git**) или в «Taking the task» аналитика: «check that the code host's tool of **Git** runs and is logged in; when not, tell the supervisor». Passes: 3/3

### 11. Нейтральность к провайдеру

Без находок. Названы только инструменты LADO. Кодовый хостинг берётся из настроек: «it with a merge commit, never squash or rebase: `gh pr merge <n> --merge` or the» … «settings' tool's equivalent» (`agents/integrator.md:50-51`). Трекер — по смыслу: «URL. Name no tracker's commands, settings files or markup yourself, and use them only as» (`skills/sdlc-settings/SKILL.md:52`). Шаблон подключён через `${SKILL_DIR}` (`:20`). R16 соблюдён: `grep -rniE "jira|jql|wiki|tracker\.yaml"` по ролям, флоу и скилам ничего не находит (в README — только пример tracker-jira-server).

### 12. Безопасность и границы

Без находок. Мерж, удаление ветки и метка в трекере идут только после `merge_ok`. Пуш — «never force» (`agents/integrator.md:26`). Новая задача в трекере — только после «explicit "yes, create"» (`skills/sdlc-settings/SKILL.md:67`). Файл настроек: «Write and commit it only with their yes» (`agents/supervisor.md:22`).

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка без учёта причины в окружении | Закрыта: «Find its cause before anyone acts on it: read the failure, and run the failing check alone» (`skills/engineering-rules/SKILL.md:40`). Это используют ревьюер (`skills/review-loop/SKILL.md:73-74`), разработчик (`agents/developer.md:37-38`) и `merge` (`agents/integrator.md:45-48`). Не покрыт провал проверки после архива — F3.1 |
| 2. Работа вне флоу, мерж без гейта | Закрыта: мерж только в состоянии `merge` после гейта `merge_ok`; супервайзер «never start a run for a task they did not name» (`agents/supervisor.md:38`) |
| 3. Путь вне worktree рана | Закрыта: «Work only inside the run's worktree, with absolute paths; leave it only by path, never» (`skills/engineering-rules/SKILL.md:58`); шаблон — через `${SKILL_DIR}` |
| 4. Вердикт без порога серьёзности | Закрыта: «the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:131`); minor финального раунда получает `decline` |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: «ask your human partner» из TDD и systematic-debugging идёт к супервайзеру (`agents/developer.md:35`); аналитик говорит с человеком по замыслу, без «execution handoff» (`agents/analyst.md:67-68`); у ревьюера таких скилов нет |
| 6. Команды не совпадают с `expects.commands` | (a) F10.1: `openspec` и `git` не объявлены. (b) Соблюдена: `gh` не объявлен, README пишет «e.g. `gh`»; проверки по настройкам нет — F10.2. (c) Пока не применима: `expects.commands` нет; правка F10.1 требует `">=0.30"` |

## Не прослежено

- Все роли, шаги, гейты, свои и зависимые скилы и `tracker` есть в разделе 3 блупринта с номерами R. Единственная жёлтая мера (Worker roles = 4) обоснована (R12, R4). `--compare` различий не печатает.
- В разделе 3 нет строк «command (expects)», хотя R3 говорит «The openspec CLI is a prerequisite» (`BLUEPRINT.md:31`). Источника, из которого формат берёт `expects.commands`, нет (F10.1).
- Строка `task.merge` (`BLUEPRINT.md:202`): «Reads `design`, `merge-report`.» — поправить вместе с F2.1.

## Низкие находки из задачи

Задача называет и две низкие находки отчёта визита 2 `improve/sdlc-2` (коммит b0573e6). Ни один проход их сейчас не нашёл, но текст не изменился, обе ОТКРЫТЫ (STILL OPEN):

- **F3.7** [low] (b0573e6) `skills/sdlc-settings/SKILL.md:78`, текст на месте: «  human in your note to do it by hand. Assign to the account `tracker` works as; when». Правило «when someone else has the task, ask the human first» требует знать, что текущий исполнитель — не аккаунт `tracker`.
- **F5.3** [low] (b0573e6) `skills/sdlc-settings/SKILL.md:56`, текст на месте: «task, and read the task back after each write: a reported success is not proof that the». Пункты `:62-63` при этом принимают собственный ответ `tracker`.

Из отчёта 0.2.0 (29a4c8f): F3.2 → здесь F3.1, F3.3 → F3.2, F2.1 → F2.1, F10.2 → F2.2, F6.1 → F6.1, F6.3 → F6.2. Все ОТКРЫТЫ (STILL OPEN).

## Вопросы человеку

Нет вопросов.
