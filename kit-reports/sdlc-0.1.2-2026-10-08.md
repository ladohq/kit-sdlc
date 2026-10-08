# Отчёт о ките: sdlc 0.1.2

- Дата: 2026-10-08
- Кит: worktree рана `improve/sdlc-2` (`.lado/worktrees/kit-comet/improve-sdlc-2`), путь дан задачей. В `kit.yaml` пока стоит `version: 0.1.1`: версию поднимают при релизе, отчёт назван по плановой версии из плана.
- Коммит: f5f61be
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.1.1-2026-10-08.md` (артефакт `assessment`). База — коммит из плана, 1422184: `git diff 1422184 -- kit.yaml README.md BLUEPRINT.md agents flows skills` (8 файлов, +59 −38; в `kit.yaml` — только строка `version`).
- Проходы: три независимых суб-агента, у каждого рубрика, папка кита, зависимые скилы и diff. Начинали с разных мест: 1 — роли, 2 — флоу по шагам, 3 — скилы. Вырезанные правила проверены по `git diff --word-diff` и таблице автора: сначала мной, потом проходами 1 и 3. Четвёртый суб-агент сделал полный проход (Re-evaluation 5) по 7 файлам из diff. Каждую его находку я подтвердил в файлах. Из находок одного прохода 2 отброшены: `decline` без причины в `review-loop:54` (формат ledger и так требует причину: «- F5 "<finding>": declined: <reason>», `:104`) и косметика переноса строк. Ещё 2 оставлены как подтверждённые.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4). analyst.md — 800 слов из 800 зелёных |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 6 находок в изменённом тексте (0 high, 3 medium, 3 low); 7 из 12 критериев без находок. Ещё 6 в «Пропущено раньше» (все low) |
| Охват | повторная оценка изменённого текста и полный проход по 7 файлам, которые затронул diff |
| Правило остановки | не выполнено: 0 high, 3 medium. Это совет для гейта релиза, не блок |

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
| Words in a role prompt | agents/analyst.md | 800 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 599 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 641 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 392 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 703 | 1000 / 1500 | green |
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

1. Сказать, кто и как даёт запасного ревьюера, или сразу спрашивать человека; и что делать с критерием остановки, если человек разрешил идти без внешнего раунда (F1.4).
2. Назвать ветку пуша интегратора однозначно: `<the design's pull request branch>`, а не `<the design's branch>` (F2.7).
3. При `dropped` после `redesign` не говорить человеку «no new work», если на ветке есть коммиты разработчика (F12.6).

## Находки

### 1. Границы ролей

- **F1.4** [medium] `skills/review-loop/SKILL.md:117` (с `agents/supervisor.md:35`)
  > start a fresh agent yourself, ask the supervisor for a fresh worker with only the diff and
  У супервизора нет правила для такой просьбы. Воркеров он запускает только по сигналу LADO и только с ролью шага: «worker for a step when LADO tells you one is needed, with `spawn_worker` and the step's» (`agents/supervisor.md:35`). Не сказано, какую роль получит запасной ревьюер. Роль `reviewer` («Write the artifact `ledger` whole on every visit», `agents/reviewer.md:44`) может прогнать целый раунд и перезаписать `ledger`. Без роли рана он работает вне флоу (известная дыра 2). Не сказано и то, как его находки вернутся к ревьюеру. Одни супервизоры откажут, другие что-то придумают. Есть и вторая половина. Если человек скажет «идём без внешнего раунда», критерий остановки по-прежнему требует его: «and external round) after the cleanup found no blocker and no important finding. When» (`:130`). `converged` в `review` допускает только «the human accepted the open findings» (`flows/task.yaml:79`). Ревьюер может отвечать `changes` до `max_visits`.
  Fix: убрать среднее предложение, чтобы ревьюер, не сумев запустить свежего агента, сразу писал это в ledger и спрашивал человека через супервизора. Или дать супервизору строку в «While runs go»: какую роль запустить, что ей дать и как вернуть находки ревьюеру. В критерий остановки добавить: внешний раунд, который человек разрешил пропустить, записан в ledger и считается выполненным. Passes: 3/3 (и полный проход)

### 2. Передача между шагами

- **F2.7** [medium] `agents/integrator.md:25-26` (state `archive`; `merge` ссылается сюда, `:47`)
  > ledger, and push as the developer does: `git push origin HEAD:refs/heads/<the
  > design's branch>`, never force.
  В `design` две ветки: «- the target branch, the pull request's branch, and whether this pull request is the» (`agents/analyst.md:85`). Прежние слова «push to the pull request's branch» вырезаны. «As the developer does» ссылается на промпт, которого интегратор не читает. У разработчика сказано точно: «1. Push the run's branch under the design's pull request branch name:» (`agents/developer.md:48`). Если прочесть `<the design's branch>` как целевую ветку, архивный (или мерж-) коммит уйдёт прямо в base или эпик-ветку мимо `merge_ok`, а это трудно отменить. Подсказывает верное чтение и «Find the pull request from the design's branch name» (`agents/reviewer.md:27`), поэтому medium, а не high. Проходы оценили низко и средне; взято medium.
  Fix: `git push origin HEAD:refs/heads/<the design's pull request branch>`. Passes: 2/3 (и полный проход отметил ту же формулировку)

### 3. Готовность и исходы

Изменённые `do` и правила говорят, когда шаг готов. Правило про minor однозначно и согласовано с критерием остановки: «A minor `fix` goes to the developer with / the fixes of its round; when its round sends nothing back, with the next fixes; it gets / `decline` when the loop converges first.» (`skills/review-loop/SKILL.md:52-54`). `archive` совпадает с `openspec-changes:70`: «(a task not checked without a reason, a delta that» (`flows/task.yaml:93`). Неоднозначность в строке `build`, которая осталась от F3.9, вынесена в «Пропущено раньше» (F3.11).

### 4. Независимая проверка

Без изменений. Пуш и мерж идут только в `merge`, после гейта `merge_ok` (`reads: [ledger, merge-report]`). Ревью делает отдельная роль.

### 5. Противоречия

- **F5.10** [low] `agents/integrator.md:35`
  > will do in git and in Jira (base branch: the comment, and a request to you to set the
  В промпте интегратора «you» — это сам интегратор, а метку ставит человек: «human in your note to set the waiting-for-release label by hand (`sdlc-settings`,» (`:64`). Текст `merge-report` может пообещать человеку, что метку поставит интегратор.
  Fix: «a request to the human to set the waiting-for-release label by hand». Passes: 2/3

Противоречие аналитика с шагом `design` по Jira снято: из шага 3 убрано «with nothing written to Jira», а перевод статуса назад разрешён только по «да» человека (`flows/task.yaml:30-32`).

### 6. Дублирование

Скрипт похожих абзацев ничего не нашёл. Команда пуша интегратора повторяет команду разработчика (`agents/integrator.md:25-26`, `agents/developer.md:49`). Так советовал прошлый отчёт: роли не читают друг друга, а `engineering-rules` команды пуша не держит. Это не находка.

### 7. Когда звать человека

F7.2 снята: «You talk to the human directly, with / `send_message` (to the human) and `ask_human`, as `sdlc-settings` says ("Talking to the / human"); the other workers reach the human through the supervisor.» (`agents/analyst.md:16-18`). Путь к человеку для запасного ревьюера описан в F1.4. На лимите цикла, при отмене и при `dropped` путь есть (`agents/supervisor.md:51-53`, `:67-73`).

### 8. Циклы на повторном визите

Без новых находок. Проза блупринта совпадает с флоу: «`review` visits (`max_visits: 8`): the first local round, the external rounds up to the» (`BLUEPRINT.md:155`). Остаток в соседней строке блупринта — F8.3 в «Пропущено раньше».

### 9. Краткость и «зачем»

- **F9.3** [low] `agents/analyst.md:49` в базе 1422184 (вырезанное «зачем»), сейчас `agents/analyst.md:50-51`
  > -question, so no answer rests on an unchecked premise.
  Правило осталось: «Facts are yours, decisions are the human's: look facts up yourself and put them into the» (`:50`). Ушла только его причина, а правило из тех, что агент может пропустить в спешке. Вырезано ради 800 слов.
  Fix: вернуть причину, если найдётся слово на замену. Иначе принять как цену бюджета. Passes: cut-rule check (проходы 1 и 3 и я)

### 10. Описания скилов

Без изменений: описания и списки `skills:` не менялись.

### 11. Нейтральность к провайдеру

Новые команды — только git (`git push origin HEAD:refs/heads/…`, `git fetch`, `git reset --keep`). Запасной ревьюер больше не требует, чтобы CLI умел запускать агентов (F11.1 снята), хотя механизм не описан (F1.4).

### 12. Безопасность и рамки

- **F12.6** [medium] `agents/supervisor.md:67-68`
  > - **At `dropped`**, tell the human why, from the run's `design`. Its branch holds no
  > new work; ask before you end its workers with `discard=true`. When the run opened a
  После `redesign` на ветке есть коммиты разработчика: `redesign` требует только «design does not hold: `build-report` says what and why, and nothing half done is» … «left uncommitted.» (`flows/task.yaml:59-60`), то есть закоммитить, а не запушить. Если `redesign` пришёл на первом визите `build` до открытия PR, условие «When the run opened a pull request» не срабатывает. Человеку скажут «no new work» и попросят разрешить `discard=true`, а это удалит незапушенные коммиты. Задачу всё равно снимают, поэтому вред невелик. Проход 2 оценил medium, полный проход — low; по правилу взято medium.
  Fix: «Its branch holds no new work unless the developer built on it (after `redesign`): then tell the human what is unpushed and what is left, as at cancel, before you ask.» Passes: 1/3, confirmed (`flows/task.yaml:59-61` `outcomes: {ready: review, redesign: design}`; полный проход нашёл то же)

- **F12.5** [low] `flows/task.yaml:31` (state `design`)
  > human in the same message where they agree to drop it whether to move it back, and
  Во-первых, «the same message where they agree» — это сообщение человека, а пишет аналитик. Во-вторых, условие «already in the settings' "in progress" status» покрывает и задачу, которая была In Progress до рана. Куда тогда «back», не сказано: в настройках есть только «in progress» и «review» (`skills/sdlc-settings/SKILL.md:28`). Скорее всего, ответ человека это решит.
  Fix: «…ask, in the message where you propose dropping it, whether to move it back and to which status, and move it only on their yes.» Passes: 3/3

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: красный CI с причиной в работе уходит в `conflicts`, с причиной вне работы — супервизору (`agents/integrator.md:49-56`). Разработчик чинит его поверх ветки: «For a red CI from `merge`, fix / the failing job the note names on top of the branch, as a finding.» (`agents/developer.md:67-68`). Остаток в `build` — F3.11 |
| 2. Работа вне флоу, мерж без гейта | Мерж только после `merge_ok`. Открыто через запасного ревьюера вне шага — F1.4. Неоднозначная ветка пуша — F2.7 |
| 3. Путь вне worktree рана | Закрыта: пуш идёт в удалённый ref, настройки перепроверяются на ветке рана после ответа: «and wait; once it answers, fetch, put the branch on the target again» (`agents/analyst.md:34`) |
| 4. Вердикт без порога серьёзности | Закрыта: «the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:126`), minor раунд не запускает (`:133`) |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: аналитику теперь прямо разрешено говорить с человеком, поэтому вопросы `brainstorming` и `grilling` уместны (`agents/analyst.md:16-18`). Подтверждение в конце `grilling` — F5.11 в «Пропущено раньше» |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Единственная жёлтая мера (Worker roles = 4) обоснована (R12, R4). Раздел 4 блупринта обновлён выводом скрипта (0.1.2). Новых ролей, шагов, гейтов и скилов нет.

## Прежние находки

- **F2.6** RESOLVED: `agents/integrator.md:25-26` «push as the developer does: `git push origin HEAD:refs/heads/<the / design's branch>`, never force.»; `:47` «push as in "Archive"». Формулировка ветки — новая F2.7.
- **F7.2** RESOLVED: `agents/analyst.md:16-18` «You talk to the human directly, with `send_message` (to the human) and `ask_human`…; the other workers reach the human through the supervisor.»
- **F3.8** RESOLVED: `skills/review-loop/SKILL.md:52-54` (цитата в разделе 3); согласовано с `:133-134`.
- **F12.3** RESOLVED: вопрос перенесён из записки конечного состояния в шаг `design` (`flows/task.yaml:30-32`), перевод только по «да» человека. Формулировка — новая F12.5.
- **F3.10** RESOLVED: `flows/task.yaml:93` «(a task not checked without a reason, a delta that».
- **F12.4** RESOLVED: `agents/supervisor.md:68-70` «When the run opened a / pull request (a `dropped` after `redesign`), also tell them what is left, as at / cancel, and ask before you close anything.» Случай без PR — новая F12.6.
- **F11.1** RESOLVED: `skills/review-loop/SKILL.md:116-119` — запасной вариант через супервизора, потом человек. Сторона супервизора — новая F1.4.
- **F2.5** RESOLVED: `agents/analyst.md:34-35` «once it answers, fetch, put the branch on the target again / and check again.»
- **F3.9** RESOLVED в роли: `agents/developer.md:67-68`. Строка `build` во флоу не тронута — F3.11 в «Пропущено раньше».
- **F5.8** RESOLVED: `agents/integrator.md:35-36` «a request to you to set the / waiting-for-release label by hand». Местоимение — новая F5.10.
- **F5.7** RESOLVED: `BLUEPRINT.md:177` «a red CI after the push leaves the pushed merge: `conflicts` for a cause in the work, the supervisor otherwise.»; строка журнала 0.1.1 исправлена так же.
- **F8.2** RESOLVED: `BLUEPRINT.md:155-157` (цитата в разделе 8); совпадает с `flows/task.yaml:73-75`.

Список автора в `build-report` (12 из 12 «fixed») сверен с файлами: все 12 подтверждены. У четырёх исправлений остались хвосты формулировок (F2.7, F12.5, F12.6, F5.10), а одно открыло новый пробел (F1.4).

## Вырезанные правила

| Вырезанное правило (файл:строка в базе) | Где оно теперь |
|---|---|
| `agents/analyst.md:13-14` «and the human knows what they approve» | Цель, а не правило; одобрение даёт гейт `design_ok` |
| `agents/analyst.md:16-17` «Talk to the human as `sdlc-settings` says» | `agents/analyst.md:16-18`, теперь с каналом (F7.2) |
| `agents/analyst.md:25` «with nothing written to Jira» | Заменено правилом `design` о возврате статуса по «да» человека (`flows/task.yaml:30-32`) |
| `agents/analyst.md:29-30` «when the task belongs to an epic that has one» | `agents/analyst.md:30` «when the task's epic has one» — то же правило |
| `agents/analyst.md:49` «so no answer rests on an unchecked premise» | Причина потеряна, правило на месте — F9.3 |
| `agents/analyst.md:59` «the human corrects what they disagree with» | Пояснение; правило «Decisions for the record» и проверка «Two rounds without a single correction» на месте (`:60-61`) |
| `agents/integrator.md:25` «and push to the pull request's branch» | `agents/integrator.md:25-26` — команда пуша, но имя ветки стало неоднозначным — F2.7 |
| `agents/integrator.md:34` «comment and the waiting-for-release label» | `agents/integrator.md:35-36` (F5.8; местоимение — F5.10) |
| `agents/supervisor.md:68` «holds no work» | `agents/supervisor.md:67-68` «holds no new work» + случай PR (F12.4; остаток — F12.6) |
| `flows/task.yaml:30-31` вопрос о возврате в записке `dropped` | `flows/task.yaml:30-32` (F12.3) |
| `skills/review-loop/SKILL.md:52-53` «A minor `fix` rides with the next round's fixes» | `skills/review-loop/SKILL.md:52-54` (F3.8) |

## Пропущено раньше

- **F3.11** [low] `flows/task.yaml:52` (state `build`)
  > like a finding), or a conflict or red check from `merge` (merge the target branch in
  При красном CI после пуша цель уже влита, а скобка велит влить её и разрешить конфликт. Роль теперь говорит «fix the failing job … on top of the branch» (`agents/developer.md:67-68`). Строка ссылается на «Fixing», так что вред мал, но `do` и роль говорят разное.
  Fix: «…or a conflict or red check from `merge`, as your role describes ("Fixing")», без скобки. Passes: 3/3 и полный проход

- **F2.8** [low] `flows/task.yaml:29`
  > agreed the task is no longer wanted; `design` then says why, and nothing is
  «nothing is committed» верно только для первого визита. После `redesign` на ветке уже есть коммиты. Аналитик может принять это за указание откатить их.
  Fix: «nothing new is committed». Passes: 1/3, confirmed (`flows/task.yaml:59-61`)

- **F3.12** [low] `agents/integrator.md:22` (state `archive`)
  > 1. Check the review converged: the ledger's last round meets the stop criterion of
  Не сказано, что делать, если проверка не прошла. Список причин не архивировать (`flows/task.yaml:93`) этого случая не содержит. Полный проход оценил medium. Я снизил до low: в `archive` ведёт только `converged` («outcomes: {changes: build, converged: archive}», `flows/task.yaml:82`), так что провал — это ошибка ревьюера.
  Fix: добавить в список причин `archive` «the ledger does not meet the stop criterion». Passes: full pass, confirmed

- **F2.9** [low] `agents/developer.md:50`
  > 2. On the first visit open the pull request against the design's target branch with the
  Если первый визит `build` закончился `redesign` до пуша, следующий визит — «later visit», где PR только обновляют, а его ещё нет. Условие готовности требует PR, так что агент, скорее всего, его откроет.
  Fix: «When no pull request is open, open one…». Passes: full pass, confirmed

- **F5.11** [low] `agents/analyst.md:67`
  > - From `brainstorming` and `writing-plans` take the method only. The gate `design_ok` is
  `grilling` заканчивается словами «Do not act on it until the user confirms you have reached a shared understanding» (grilling `SKILL.md:22`), но исключение называет только два скила. Часть аналитиков попросит лишнее подтверждение в чате.
  Fix: назвать `grilling` в том же предложении, не добавляя слов (analyst.md на 800 из 800). Passes: full pass, confirmed

- **F8.3** [low] `BLUEPRINT.md:159`
  > later visit fixes what the ledger, the human's rejection or the merge conflict names.
  Проза блупринта не называет красный CI из `merge`, хотя кит с 0.1.1 возвращает его в `build`.
  Fix: «…the merge conflict or a red CI from `merge` names.» Passes: 1/3, confirmed (`flows/task.yaml:116-119`, `agents/developer.md:67-68`)

## Оставлено планом

План ничего не оставляет («Leave: none»).

## Вопросы к человеку

1. Правило остановки не выполнено: в изменённом тексте 3 medium (F1.4, F2.7, F12.6), high нет. Задача говорит выпускать без нового круга, только если останутся одни low. Сделать ещё круг по этим трём (все правки в 1–2 строки) или выпустить 0.1.2 как есть? Рекомендация: ещё один короткий круг. F2.7 касается пуша рядом с базовой веткой, а F1.4 может держать ревью до `max_visits`.
