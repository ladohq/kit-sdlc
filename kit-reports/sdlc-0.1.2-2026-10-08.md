# Отчёт о ките: sdlc 0.1.2

- Дата: 2026-10-08
- Кит: worktree рана `improve/sdlc-2` (`.lado/worktrees/kit-comet/improve-sdlc-2`), путь дан задачей. В `kit.yaml` пока стоит `version: 0.1.1`: версию поднимают при релизе, отчёт назван по плановой версии из плана.
- Коммит: 9802ff5 (визит 2 шага `evaluate`; визит 1 оценивал f5f61be, эта версия отчёта заменяет ту)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.1.1-2026-10-08.md` (артефакт `assessment`) и моего отчёта визита 1. База — коммит из плана, 1422184: `git diff 1422184 -- kit.yaml README.md BLUEPRINT.md agents flows skills` (8 файлов; в `kit.yaml` только строка `version`). Правки этого визита: `git diff f5f61be 9802ff5` (6 файлов, +38 −23).
- Проходы: три независимых суб-агента, у каждого рубрика, папка кита, зависимые скилы, оба diff и отчёт визита 1. Начинали с разных мест: 1 — роли, 2 — флоу по шагам, 3 — скилы. Вырезанное автором проверили я и проход 1. Полный проход (Re-evaluation 5) по 6 файлам из diff сделал четвёртый суб-агент. Каждую его находку я подтвердил в файлах. Из находок одного прохода 1 отброшена: «where you propose dropping it» не покрывает случай, когда снять задачу предлагает человек. Аналитик всё равно отвечает человеку, и вреда в файлах я не нашёл. Оставленных одиночных нет.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4). analyst.md — 798 слов из 800 зелёных |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 5 находок в тексте, изменённом на визите 2 (0 high, 2 medium, 3 low); 8 из 12 критериев без находок. Ещё 14 в «Пропущено раньше» (1 medium, 13 low) |
| Охват | повторная оценка изменённого текста и полный проход по 6 файлам, которые затронул diff |
| Правило остановки | не выполнено: 0 high, 2 medium. Это совет для гейта релиза, не блок |

Счёт относится только к тому, что указано в строке «Охват»: с полной оценкой он не сравним.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
expects skills: tracker
```

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.1.1

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 5 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 798 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 599 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 644 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 392 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 770 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.1.2-2026-10-08/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

1. Сказать, каким будет запасной ревьюер супервизора: только читает, ничего не меняет и не постит, получает от ревьюера дифф (`git diff origin/<target>...HEAD`) (F12.7).
2. Сказать, действует ли разрешение человека пропустить внешний раунд на один раунд или до конца цикла (F3.13).
3. Пропущено раньше, но заметно: повторный визит `build` после `redesign` и нового `design_ok` (F2.10).

## Находки

### 1. Границы ролей

Права ролей в этом визите не менялись. Права запасного агента супервизора — F12.7.

### 2. Передача между шагами

Ветка пуша интегратора теперь однозначна: «design's pull request branch>`, never force.» (`agents/integrator.md:26`). Откуда берётся дифф для запасного ревьюера — в F12.7.

### 3. Готовность и исходы

- **F3.13** [medium] `skills/review-loop/SKILL.md:133`
  > external round the human allowed you to skip counts as run, once the ledger records
  Не сказано, на что действует «да» человека: на один раунд или на весь оставшийся цикл. Внешний раунд нужен после каждого чистого локального прохода до лимита и ещё раз в финальном раунде после cleanup. В проекте без внешнего ревьюера, где свежего агента не запустить, одни ревьюеры будут спрашивать человека через супервизора каждый раунд, другие возьмут первое «да» на весь цикл. Проходы оценили низко, средне и низко; взято medium.
  Fix: «…counts as run; their answer holds for the rest of the loop unless they say otherwise, recorded in the ledger.» Passes: 3/3

### 4. Независимая проверка

Без изменений: мерж только после `merge_ok`. Находки запасного ревьюера возвращаются ревьюеру, и он проверяет их как любые другие («Treat the external reviewer's findings like any other: verify, tier, fate.», `skills/review-loop/SKILL.md:122`).

### 5. Противоречия

- **F5.13** [low] `BLUEPRINT.md:165`
  > | `supervisor` | role (lead) | R1, R6, R14 | Starts one run of `task` per Jira key the human names, in the human's language; answers blocked workers; brings the human to the gates. |
  У супервизора появилась новая обязанность: запускать запасного ревьюера вне рана (`agents/supervisor.md:54-58`). Строка трассировки её не называет. В журнале нет строки о правках этого визита по отчёту 0.1.2 (`BLUEPRINT.md:224-229` ссылаются только на отчёт 0.1.1).
  Fix: дописать в строку «starts a substitute reviewer when the reviewer cannot (`review-loop`)» и добавить строку журнала со ссылкой на `kit-reports/sdlc-0.1.2-2026-10-08.md`. Passes: 2/3

### 6. Дублирование

Скрипт похожих абзацев ничего не нашёл. Пункт супервизора и строка `review-loop` — две стороны одной передачи, а не повтор правила.

### 7. Когда звать человека

- **F7.4** [low] `agents/supervisor.md:57`
  > reviewer as that round's external response. When you cannot either, tell the reviewer
  Получив такой ответ, ревьюер должен «ask the human through the supervisor whether to / go on without the external round.» (`skills/review-loop/SKILL.md:119-120`). Вопрос возвращается к тому же супервизору: один лишний круг сообщений.
  Fix: «When you cannot either, ask the human whether the review goes on without the external round and send the reviewer their answer.» Passes: 2/3

### 8. Циклы на повторном визите

Без новых находок в изменённом тексте. Новое правило о пропущенном внешнем раунде закрывает зацикливание `review` до `max_visits` (часть F1.4); его неоднозначность — F3.13.

### 9. Краткость и «зачем»

Причина правила о фактах вернулась: «question, so no answer rests on an unchecked premise.» (`agents/analyst.md:51`). analyst.md — 798 слов. Вырезанное ради неё не теряет правил (раздел «Вырезанные правила»).

### 10. Описания скилов

Описания и списки `skills:` не менялись. То, что интегратор не грузит `review-loop`, отмечено в «Пропущено раньше» (F10.2).

### 11. Нейтральность к провайдеру

Новый пункт супервизора называет действие, а не инструмент CLI, и даёт запасной путь для CLI, которое не умеет запускать агентов: «When you cannot either, tell the reviewer» (`agents/supervisor.md:57`).

### 12. Безопасность и рамки

- **F12.7** [medium] `agents/supervisor.md:55-56`
  > one): start a fresh agent yourself, outside the run and not with `spawn_worker`, given
  > only the diff and the pull request's description, and send its findings back to the
  Это проверка вне рана (известная дыра 2) в папке супервизора, то есть в основном чекауте человека (известная дыра 3). Пример: «in your» (`agents/supervisor.md:65`, уборка в собственном репозитории). Прав агента никто не задаёт: «You change no code» связывает самого супервизора, а не того, кого он запустит. Не сказано и то, откуда дифф. Супервизор не грузит `review-loop` (`skills:` — только `sdlc-settings` и `tracker`) и не знает правила области: «`git diff origin/<target>...HEAD`. A pushed branch's `@{upstream}` diff is empty, and a» (`skills/review-loop/SKILL.md:62`). Поэтому дифф, снятый в его чекауте, может оказаться пустым или не тем. Тогда «чистый» запасной раунд пойдёт в критерий остановки. Проходы оценили средне, средне и низко; взято medium.
  Fix: в `review-loop:117`: ревьюер посылает с просьбой дифф, который он ревьюил, и описание PR. В `supervisor.md:55-56`: «start a fresh read-only agent yourself (it changes no file, runs nothing in your repository and posts nothing), … given only the diff and the description the reviewer sent». Passes: 3/3

- **F12.8** [low] `agents/supervisor.md:72-73`
  > - **At `dropped`**, tell the human why, from the run's `design`. Its branch holds no
  > new work unless the developer built on it (a `dropped` after `redesign`): then also
  Есть ещё один случай, когда на ветке уже есть работа: `dropped` после отказа на `design_ok`. Аналитик закоммитил изменение («Commit the files by path.», `agents/analyst.md:81`), и «outcomes: {approved: build, rejected: design}» (`flows/task.yaml:40`) вернул его в `design`. Человеку скажут «no new work» и попросят `discard=true`, а это удалит дизайн, который он правил. Задачу всё равно снимают, и супервизор спрашивает перед удалением.
  Fix: «…unless a step committed on it (the analyst's change after a rejection, the developer's work after `redesign`): then also tell them…». Passes: 2/3

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: в этом визите не менялась. Красный CI с причиной в работе уходит в `conflicts`, с причиной вне работы — супервизору (`agents/integrator.md:49-56`) |
| 2. Работа вне флоу, мерж без гейта | Мерж только после `merge_ok`. Запасной ревьюер работает вне рана намеренно, но без заданных прав — F12.7 |
| 3. Путь вне worktree рана | Пуш интегратора теперь в однозначный удалённый ref (`agents/integrator.md:25-26`). Запасной ревьюер в чекауте супервизора — F12.7 |
| 4. Вердикт без порога серьёзности | Закрыта: «the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:127`). Правило о пропуске касается только внешнего раунда, не порога |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: списки скилов не менялись, аналитику прямо разрешено говорить с человеком (`agents/analyst.md:16-18`). Подтверждение `grilling` — F5.11 в «Пропущено раньше» |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Единственная жёлтая мера (Worker roles = 4) обоснована (R12, R4). Раздел 4 блупринта совпадает с выводом скрипта (798 / 644 / 770). Новых ролей, шагов, гейтов и скилов нет. Новая обязанность супервизора без строки в блупринте — F5.13.

## Прежние находки

Отчёт 0.1.1 (12 находок): все RESOLVED на визите 1, ничего не откатилось. Цитаты — в версии этого отчёта на коммите d6e1e23.

Отчёт визита 1 (f5f61be):
- **F1.4** RESOLVED: супервизор получил правило: «- **The reviewer asks for a fresh reviewer** (`review-loop`, its CLI cannot start» (`agents/supervisor.md:54-58`). Ревьюер просит «a fresh agent», и супервизор возвращает его находки (`skills/review-loop/SKILL.md:117-118`). Пропущенный с согласия человека внешний раунд засчитывается (`:132-134`), так что `review` не крутится до `max_visits`. Новые хвосты: F12.7, F3.13, F7.4.
- **F2.7** RESOLVED: `agents/integrator.md:25-26` «`git push origin HEAD:refs/heads/<the / design's pull request branch>`, never force.»; совпадает с `agents/developer.md:48`.
- **F5.10** RESOLVED: `agents/integrator.md:35` «a request to the human to set the».
- **F9.3** RESOLVED: `agents/analyst.md:51` «question, so no answer rests on an unchecked premise.»
- **F12.6** RESOLVED: `agents/supervisor.md:72-76` «…unless the developer built on it (a `dropped` after `redesign`): then also / tell them what is committed but not pushed…». Остался случай отказа на `design_ok` — F12.8.
- **F12.5** RESOLVED: `flows/task.yaml:30-32` «ask, in / the message where you propose dropping it, whether to move it back and to which / status».
- Находки визита 1 из «Пропущено раньше» (F3.11, F2.8, F3.12, F2.9, F5.11, F8.3) автор не трогал, и правильно: их взвешивает человек. Все STILL OPEN, остаются в своём разделе.

Список автора в `build-report` (6 из 6 «fixed») сверен с файлами: все 6 подтверждены.

## Вырезанные правила

Вырезанные на визите 1 — в версии отчёта на d6e1e23. Вырезанные на визите 2:

| Вырезанное правило (файл:строка в f5f61be) | Где оно теперь |
|---|---|
| `agents/analyst.md:12-14` «You take its Jira task and design the change with the human, so that…» | `agents/analyst.md:12-14` «you design its Jira task's change with the human, so…» — то же правило |
| `agents/analyst.md:40` «with its reason in one line» | `agents/analyst.md:40` «with a one-line reason» — то же правило |
| `agents/analyst.md:26-27` шаг 4 «and read it back» | `skills/sdlc-settings/SKILL.md:51` «read the task back with `get` after each write»; шаг 4 указывает на этот раздел (`agents/analyst.md:29`) |
| `agents/supervisor.md:68-70` «When the run opened a / pull request (a `dropped` after `redesign`), also tell them what is left» | `agents/supervisor.md:73-76`, шире (F12.6) |
| `flows/task.yaml:31` «in the same message where they agree to drop it» | `flows/task.yaml:31` «in the message where you propose dropping it» (F12.5) |
| `skills/review-loop/SKILL.md:117` «a fresh worker» | `:117` «a fresh agent» (F1.4) |

## Пропущено раньше

С визита 1, все STILL OPEN:

- **F3.11** [low] `flows/task.yaml:52` (state `build`)
  > like a finding), or a conflict or red check from `merge` (merge the target branch in
  При красном CI после пуша цель уже влита, а скобка велит влить её снова. Роль говорит чинить поверх ветки (`agents/developer.md:67-68`).
  Fix: «…or a conflict or red check from `merge`, as your role describes ("Fixing")», без скобки. Passes: 3/3 и полный проход (визит 1)

- **F2.8** [low] `flows/task.yaml:29`
  > agreed the task is no longer wanted; `design` then says why, and nothing is
  «nothing is committed» верно только для первого визита.
  Fix: «nothing new is committed». Passes: 1/3, confirmed (визит 1)

- **F3.12** [low] `agents/integrator.md:22` (state `archive`)
  > 1. Check the review converged: the ledger's last round meets the stop criterion of
  Не сказано, что делать, если проверка не прошла.
  Fix: добавить в список причин `archive` «the ledger does not meet the stop criterion». Passes: full pass, confirmed (визит 1)

- **F2.9** [low] `agents/developer.md:50`
  > 2. On the first visit open the pull request against the design's target branch with the
  После `redesign` до пуша PR ещё нет, а «later visit» его только обновляет.
  Fix: «When no pull request is open, open one…». Passes: full pass, confirmed (визит 1)

- **F5.11** [low] `agents/analyst.md:67`
  > - From `brainstorming` and `writing-plans` take the method only. The gate `design_ok` is
  Подтверждение в конце `grilling` исключение не называет.
  Fix: назвать `grilling` в том же предложении. Passes: full pass, confirmed (визит 1)

- **F8.3** [low] `BLUEPRINT.md:159`
  > later visit fixes what the ledger, the human's rejection or the merge conflict names.
  Не назван красный CI из `merge`.
  Fix: «…the merge conflict or a red CI from `merge` names.» Passes: 1/3, confirmed (визит 1)

Новые на визите 2:

- **F2.10** [medium] `flows/task.yaml:48` (state `build`)
  > On the first visit the previous step's note is the human's answer at `design_ok`: a
  В `build` можно войти второй раз из `design_ok`: `build` → `redesign` → `design` → `design_ok` approved. Тогда записка — одобрение нового дизайна, а этот случай `do` не покрывает. Правило «a comment there binds like the design» дано только для первого визита. На «later visit» разработчик отправлен к `ledger`, если ревью уже было: «On a later visit, the note says what to fix. Fix each ledger finding whose fate is "fix",» (`agents/developer.md:62`). Но находки там — о дизайне, которого больше нет. Одни разработчики будут чинить устаревшие находки, другие пропустят комментарий человека с гейта.
  Fix: «On a visit after `design_ok` (the first, or after `redesign`) the note is the human's answer there: a comment binds like the design; build the revised change.» Случай `ledger` оставить для визитов после `review`. Passes: full pass, confirmed

- **F10.2** [low] `agents/integrator.md:23` (state `archive`)
  > `review-loop`, or the human accepted the open findings, recorded in the ledger.
  Интегратор сверяет ledger с критерием остановки `review-loop`, но этого скила нет в его `skills:` (sdlc-settings, openspec-changes, engineering-rules, tracker). Нового правила о пропущенном внешнем раунде он не видит. Риск снижает то, что ledger сам пишет, где цикл стоит относительно критерия (`agents/reviewer.md:45-46`).
  Fix: добавить `review-loop` в `skills:` интегратора или переписать шаг 1: «the ledger says the loop converged…». Passes: 2/3

- **F4.3** [low] `flows/task.yaml:20` (state `design`)
  > for rejecting the design at `design_ok` (quote it at the top of `design`), or the
  В `design` цитируют только отказ человека. `design_ok` читает только `design` (`reads: [design]`), поэтому после `redesign` человек одобряет новый дизайн, не видя, почему разработчик остановился.
  Fix: «(quote either reason at the top of `design`)». Passes: full pass, confirmed

- **F12.9** [low] `agents/supervisor.md:25`
  > sends you the missing file and the target. Then ask the human to push it.
  Если цель — эпик-ветка, «push it» не кладёт файл туда, где его ищет аналитик (`agents/analyst.md:32-33`). Коммит к тому же ляжет на ту ветку, на которой стоит чекаут супервизора. Аналитик снова не найдёт файл и спросит ещё раз.
  Fix: «…ask the human to put it on that target branch (the base, or the epic's branch the analyst named) and push it.» Passes: full pass, confirmed

- **F5.14** [low] `skills/review-loop/SKILL.md:64`
  > 2. **Local pass.** Breadth on the first round, and on a round after a fix that reshaped
  Роль ревьюера говорит иначе: «breadth on the first round» … «of a full change, precision otherwise» (`agents/reviewer.md:29-30`). Ревьюер, который следует скилу, прогонит на hotfix лишний проход вширь.
  Fix: «Breadth on the first round of a full change (the role and the design's review depth decide)…». Passes: full pass, confirmed

- **F3.14** [low] `skills/review-loop/SKILL.md:88`
  > The external reviewer reads what is pushed, and the developer pushes in `build`: check
  Не сказано, что делать, если удалённый head не тот, что ревьюили. Пушить ревьюер не может. Случай редкий: `build` готов только после пуша.
  Fix: «…when it differs, request no external round: report `changes` with "push the head <sha>"». Passes: full pass, confirmed

- **F5.15** [low] `BLUEPRINT.md:171`
  > | `task.design` | work step | R3, R5, R7, R11 | Takes the task (assign, In Progress), checks it is still relevant (`dropped` otherwise), proposes the scale, writes the openspec change, and for full the design doc and plan; names the target branch (base or epic) and checks the settings files are on it. Reads `build-report` on a return. Produces `design`. |
  Задачу шаг не назначает: по R11 и роли (`agents/analyst.md:27-28`) её назначает сам человек.
  Fix: «Takes the task (In Progress; asks the human to assign it)». Passes: full pass, confirmed

- **F5.16** [low] `BLUEPRINT.md:179`, `:181`
  > | `openspec-changes` | skill | R3, R9 | How to create, validate and archive a change compatibly with the project's archive (analyst, developer, integrator). |
  Списки пользователей неполны: `openspec-changes` грузит и ревьюер (`agents/reviewer.md:8`), а `engineering-rules` — и интегратор (`agents/integrator.md:7`).
  Fix: добавить «reviewer» в строку 179 и «and integrator» в строку 181. Passes: full pass, confirmed

## Оставлено планом

План ничего не оставляет («Leave: none»).

## Вопросы к человеку

1. Правило остановки снова не выполнено: в правках визита 2 — 2 medium (F12.7, F3.13), high нет. Обе — хвосты исправления F1.4 (запасной ревьюер), каждая правится одной фразой. Сделать ещё круг или выпустить 0.1.2 как есть? Рекомендация: один короткий круг только по F12.7 и F3.13, остальное отложить в бэклог. Каждый круг находит новое по краям: 14 находок «Пропущено раньше» были в тексте ещё до этого рана.
