# Отчёт о ките: sdlc 0.1.2

- Дата: 2026-10-08
- Кит: worktree рана `improve/sdlc-2` (`.lado/worktrees/kit-comet/improve-sdlc-2`), путь дан задачей. В `kit.yaml` пока стоит `version: 0.1.1`: версию поднимают при релизе, отчёт назван по плановой версии из плана.
- Коммит: d3af76d (визит 3 шага `evaluate`; визиты 1 и 2 оценивали f5f61be и 9802ff5, их версии этого отчёта — коммиты d6e1e23 и a003e81)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.1.1-2026-10-08.md` (артефакт `assessment`) и отчёта визита 2. База — коммит из плана, 1422184: `git diff 1422184 -- kit.yaml README.md BLUEPRINT.md agents flows skills`. Правки этого визита: `git diff 9802ff5 d3af76d` (7 файлов, +49 −45). По решению человека на `release_ok` запасной ревьюер супервизора убран целиком.
- Проходы: три независимых суб-агента, у каждого рубрика, папка кита, зависимые скилы, оба diff и отчёт визита 2. Начинали с разных мест: 1 — роли, 2 — флоу по шагам, 3 — скилы. Вырезанное проверили я и проходы 1 и 3. Полный проход (Re-evaluation 5) по 7 файлам из diff сделал четвёртый суб-агент. Каждую его находку я подтвердил в файлах. Одиночных находок отброшено 0. Одна оставлена как подтверждённая (F12.10). Находки прохода 2 о `merge_ok` после «archive nothing» объединены с находкой прохода 3 в одном месте в F3.15.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4). analyst.md — 798 слов из 800 зелёных |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 6 находок в тексте, изменённом на визите 3 (0 high, 3 medium, 3 low); 7 из 12 критериев без находок. Ещё 4 в «Пропущено раньше» (3 medium, 1 low) |
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
| Words in a role prompt | agents/analyst.md | 798 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 604 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 641 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 392 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 739 | 1000 / 1500 | green |
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

1. Вернуть разработчику правило для локальной красной проверки после вливания цели. Оно потерялось вместе со скобкой в `build` (F2.11).
2. Сказать, что делает человек на `merge_ok`, когда `archive` ничего не заархивировал из-за несошедшегося ревью. Одобрение сейчас сольёт неархивированное изменение (F3.15).
3. Различать в проверке удалённого head «впереди мы» и «впереди удалённый» (F3.16).

## Находки

### 1. Границы ролей

Права ролей не менялись. Запасной ревьюер супервизора убран, поэтому вне рана больше ничего не запускается: в `agents/supervisor.md` нет ни «fresh agent», ни «fallback».

### 2. Передача между шагами

- **F2.11** [medium] `agents/developer.md:68` (state `build`, после `merge` → `conflicts`)
  > the run's branch, resolve it, and run the checks again. For a red CI from `merge`, fix
  Скобка в `build` (её убрали по F3.11) покрывала два случая: «a conflict or red check from `merge` (merge the target branch in». Вместе с ней пропало правило для второго случая: `conflicts` из-за локальной проверки, покрасневшей после вливания цели («target in conflicts or a local check is red for a cause in the work: undo the merge», `flows/task.yaml:114`). Интегратор откатывает свой мерж-коммит, а «Fixing» разработчика называет только конфликт (влить цель) и красный CI (чинить поверх ветки). Разработчик, принявший локальную красную проверку за красный CI, чинит без цели. На его ветке ошибка не воспроизводится, проверки зелёные, и ран идёт через `review` → `archive` → `merge_ok` → `merge` к той же красной проверке. Это лишний круг с двумя гейтами.
  Fix: «For a conflict, or a red check after the target was brought in, merge `origin/<target>` into the run's branch, resolve it, and run the checks again.» Passes: cut-rule check (проход 1 и я; `git diff 9802ff5 d3af76d -- flows/task.yaml`)

### 3. Готовность и исходы

- **F3.15** [medium] `flows/task.yaml:93-95` (state `archive`, затем `merge_ok`)
  > risk. When the change cannot be archived as it is (the ledger does not show the
  > living spec), archive nothing and say so at the top of `merge-report`: the human sends it back at `merge_ok`. Report `ready`: note_summary
  Новая причина ведёт в тупик в обе стороны. `rejected` уводит в `build` («outcomes: {approved: merge, rejected: build}», `flows/task.yaml:104`), где разработчику нечего чинить: ledger — артефакт ревьюера. Он отчитается `ready` без правок или придумает правку. При этой причине человек вполне может принять открытые находки и одобрить. Тогда `approved` ведёт в `merge`, а «Merge» интегратора не архивирует и не проверяет архивный коммит («1. `git fetch origin`, note `git rev-parse HEAD`, and merge `origin/<target>` into the», `agents/integrator.md:42`). Изменение уйдёт в базовую ветку неархивированным, а это трудно отменить. Проходы 2 и 3 оценили medium.
  Fix: в `archive`: «…the human sends it back, or accepts the open findings there»; в `merge`: «When `merge-report` says nothing was archived and the human accepted the findings at `merge_ok`, archive first ("Archive"), then merge»; в `build`: отказ из-за несошедшегося ledger без правок кода — процитировать и отчитаться `ready`. Passes: 2/3

- **F3.16** [medium] `skills/review-loop/SKILL.md:90-91`
  > never push yourself; when it differs, request no external round: report `changes` with
  > "push the head <sha>". Request the round as the
  «When it differs» покрывает оба направления, а лекарство — одно. Если впереди удалённый head (человек нажал «Update branch» на хосте или сам запушил), push без force будет отклонён: «- Never force-push, never rewrite pushed history, never `--no-verify`: reviewers and» (`skills/engineering-rules/SKILL.md:65`). Правила на этот случай у разработчика нет. Случай редкий. Проход 2 оценил low, проход 3 — medium; по правилу взято medium.
  Fix: «…when the remote head is behind yours, report `changes` with "push the head <sha>"; when it is ahead or diverged, report `changes` naming both heads, for the developer to bring the remote in.» Passes: 2/3

### 4. Независимая проверка

Мерж только после `merge_ok`. Путь мимо архива при одобрении — в F3.15. Находки внешнего раунда проверяются как любые другие (`skills/review-loop/SKILL.md:121`).

### 5. Противоречия

Изменения согласованы: случаи визитов `build` совпадают во флоу, в роли («On a visit after `review`, `merge_ok` or `merge`, the note says what to fix.», `agents/developer.md:62`) и в блупринте (`BLUEPRINT.md:159-160`). Интегратор читает сходимость из ledger и больше не ссылается на скил, которого не грузит.

### 6. Дублирование

Скрипт похожих абзацев ничего не нашёл. Повторов в правках нет.

### 7. Когда звать человека

- **F7.5** [low] `skills/review-loop/SKILL.md:119`
  > whether to go on without the external round (their answer: "Stop criterion").
  Описан только ответ «да». После удаления запасного ревьюера сюда приходит любой проект без внешнего ревьюера, где CLI не умеет запускать агентов. При «нет» у ревьюера нет пути: он может отвечать `changes` без находок, и `build` ↔ `review` будут крутиться до `max_visits`. Обычно человек вместе с «нет» говорит, что делать, а цикл ограничен лимитом.
  Fix: «…whether to go on without the external round, and do as they say (a skip counts as run: "Stop criterion").» Passes: 3/3

### 8. Циклы на повторном визите

Без новых находок в изменённом тексте: повторный визит `build` после `redesign` описан (F2.10), `design` цитирует любую причину возврата (F4.3). Неясный раунд `review` после `merge_ok` или `conflicts` — F8.4 в «Пропущено раньше».

### 9. Краткость и «зачем»

- **F9.4** [low] `flows/task.yaml:49` (state `build`)
  > answer there: a comment binds like the design; build the revised change. On a visit
  Фраза покрывает и первый визит («the first, or after `redesign`»), когда «revised» изменения ещё нет. Только формулировка: действие агента не меняется.
  Fix: «…build the approved change (after `redesign`, the revised one).» Passes: 2/3

### 10. Описания скилов

Описания не менялись. Интегратору `review-loop` больше не нужен (F10.2).

### 11. Нейтральность к провайдеру

Новых команд и инструментов нет. Без внешнего ревьюера и без свежего агента путь теперь идёт через человека (`skills/review-loop/SKILL.md:117-119`).

### 12. Безопасность и рамки

- **F12.10** [low] `flows/task.yaml:30` (state `design`)
  > committed. When the task is already in the settings' "in progress" status, ask, in
  При `dropped` после `redesign` задача может стоять в «review»: разработчик переводит её туда на последнем PR («4. When the design says this is the task's last pull request, move the task to the», `agents/developer.md:57`). Правило спрашивает о возврате статуса только для «in progress», так что задача останется в «review» без вопроса.
  Fix: «When the task is already in the settings' "in progress" or "review" status, ask…». Passes: 1/3, confirmed (`agents/developer.md:57-58`, `flows/task.yaml:61` «outcomes: {ready: review, redesign: design}»)

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: интегратор разделяет причины по `engineering-rules`, «A red check» (`agents/integrator.md:44-47`). Куда разработчик несёт локальную красную проверку — F2.11 |
| 2. Работа вне флоу, мерж без гейта | Работы вне рана больше нет: запасной ревьюер супервизора удалён. Мерж только после `merge_ok`, но одобрение после «archive nothing» сливает без архива — F3.15 |
| 3. Путь вне worktree рана | Закрыта: файл настроек на целевую ветку кладёт человек (`agents/supervisor.md:25-26`) |
| 4. Вердикт без порога серьёзности | Закрыта: «the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:126`) |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: «- From `brainstorming`, `grilling` and `writing-plans` take the method only.» (`agents/analyst.md:67`). Пробная ошибка ревьюера из `engineering-rules` — F5.18 в «Пропущено раньше» |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Единственная жёлтая мера (Worker roles = 4) обоснована (R12, R4). Раздел 4 блупринта совпадает с выводом скрипта (798 / 604 / 641 / 392 / 739). Новых ролей, шагов, гейтов и скилов нет. Новая обязанность супервизора убрана, поэтому его строка трассировки снова верна.

## Прежние находки

Отчёт 0.1.1 (12) и отчёт визита 1 (6): все RESOLVED, ничего не откатилось.

Отчёт визита 2 (a003e81), все 19 RESOLVED. Сверено всеми тремя проходами:
- **F12.7** RESOLVED: пункт «The reviewer asks for a fresh reviewer» удалён из `agents/supervisor.md`, как решил человек.
- **F3.13** RESOLVED: «their answer, recorded in / the ledger, holds for the rest of the loop unless they say otherwise.» (`skills/review-loop/SKILL.md:132-133`).
- **F7.4** RESOLVED: пункта супервизора нет; ревьюер спрашивает человека один раз (`skills/review-loop/SKILL.md:117-119`).
- **F12.8** RESOLVED: «new work unless a step committed on it (the analyst's change after a rejection at / `design_ok`, the developer's work after `redesign`)» (`agents/supervisor.md:69-70`).
- **F5.13** RESOLVED: обязанности больше нет; строка журнала со ссылкой на отчёт 0.1.2 есть (`BLUEPRINT.md:231`).
- **F2.10** RESOLVED: «On a visit after `design_ok` (the first, or after `redesign`) the note is the human's» (`flows/task.yaml:48`). Хвост формулировки — F9.4.
- **F3.11** RESOLVED: скобка убрана (`flows/task.yaml:53`). Потерянное при этом правило — F2.11.
- **F2.8** RESOLVED: «nothing new is» (`flows/task.yaml:29`).
- **F3.12** RESOLVED: «(the ledger does not show the / review converged, …» (`flows/task.yaml:93-94`). Что дальше на `merge_ok` — F3.15.
- **F10.2** RESOLVED: «the ledger says its last round met the stop criterion, / or that the human accepted the open findings.» (`agents/integrator.md:22-23`).
- **F2.9** RESOLVED: «2. When no pull request is open, open one against the design's target branch with the» (`agents/developer.md:50`).
- **F5.11** RESOLVED: `agents/analyst.md:67` (цитата в «Известных дырах»).
- **F4.3** RESOLVED: «quote either reason at / the top of `design`.» (`flows/task.yaml:21-22`).
- **F12.9** RESOLVED: «ask the human to put it on that target / branch (the base, or the epic's branch the analyst named) and push it.» (`agents/supervisor.md:25-26`).
- **F5.14** RESOLVED: «Breadth on the first round of a full change (the role and the» (`skills/review-loop/SKILL.md:64`).
- **F3.14** RESOLVED: правило есть (`skills/review-loop/SKILL.md:90-91`). Обратное направление — F3.16.
- **F8.3**, **F5.15**, **F5.16** RESOLVED: `BLUEPRINT.md:159-160`, `:172`, `:180`, `:182`.

Список автора в `build-report` сверен с файлами: все пункты подтверждены.

## Вырезанные правила

Визиты 1 и 2 — в версиях отчёта на d6e1e23 и a003e81. Визит 3:

| Вырезанное правило (файл:строка в 9802ff5) | Где оно теперь |
|---|---|
| `flows/task.yaml:52-53` «(merge the target branch in and resolve it)» для конфликта и локальной красной проверки из `merge` | Конфликт — `agents/developer.md:67-68`. Локальная красная проверка — **потеряно**, F2.11 |
| `agents/supervisor.md:54-58` пункт о запасном ревьюере | Убран по решению человека; путь через человека — `skills/review-loop/SKILL.md:117-119` |
| `skills/review-loop/SKILL.md:117-118` просьба к супервизору о свежем агенте | Убрана вместе с пунктом супервизора (то же решение) |
| `agents/integrator.md:22-23` «stop criterion of `review-loop`… recorded in the ledger» | `agents/integrator.md:22-23` «the ledger says…»: ledger пишет, где цикл стоит относительно критерия (`agents/reviewer.md:45-46`) |
| `agents/developer.md:50-53` «On the first visit… On later visits» | `agents/developer.md:50-53` «When no pull request is open… When one is open» — шире |
| `agents/developer.md:62` «On a later visit» | `agents/developer.md:62` «On a visit after `review`, `merge_ok` or `merge`» |
| `agents/analyst.md:66` «before you write it down» | `agents/analyst.md:66` «before writing it down» — то же правило |

## Пропущено раньше

Находки «Пропущено раньше» визитов 1 и 2 все RESOLVED (см. выше). Новые с полного прохода визита 3:

- **F5.17** [medium] `flows/task.yaml:54` (state `build`)
  > Done when every task of the change's `tasks.md` is checked, the project's checks
  Скил и роль разработчика допускают неотмеченную задачу с причиной: «way stays unchecked with a line saying why, and the build report names it.» (`skills/openspec-changes/SKILL.md:62`), «the tasks checked and any left unchecked with the reason;» (`agents/developer.md:77`). Условие готовности `build` этого не допускает. F3.10 исправила то же расхождение только в `archive`. Один разработчик отметит задачу без выполнения, другой отчитается `redesign` из-за мелочи.
  Fix: «…every task of `tasks.md` is checked or says why not…»; то же в `skills/review-loop/SKILL.md:68`. Passes: full pass, confirmed

- **F5.18** [medium] `skills/review-loop/SKILL.md:72`
  > (`engineering-rules`, "A red check"). A check must be able to fail; the traps are in `${SKILL_DIR}/references/false-greens.md`.
  Ревьюер только читает: «change no file, branch or commit; no checkout, reset or stash in the run's worktree,» (`agents/reviewer.md:16`). Но то, на что ссылается эта строка, велит править: «same for all: see the check fail on a deliberate error before you trust its green.» (`references/false-greens.md:5`). Один ревьюер внесёт пробную ошибку в общий worktree, другой пропустит пробу и засчитает ложный зелёный.
  Fix: в `agents/reviewer.md`: «A check you cannot see fail without editing a file goes into the ledger as unverifiable, with the probe for the developer to run; make none yourself.» Passes: full pass, confirmed

- **F8.4** [medium] `flows/task.yaml:74` (state `review`)
  > cleanup and final rounds, and a return after `merge_ok` or `conflicts`; reaching the
  Не сказано, какой раунд идёт после возврата из `merge_ok` или `conflicts`. В ledger уже есть cleanup и финальный раунд, и при буквальном чтении критерий остановки выполняется после одного локального прохода. Один ревьюер сойдётся без внешнего раунда по правке после гейта, другой прогонит внешний раунд, cleanup и финальный раунд заново.
  Fix: в «Stop criterion» `review-loop`: «After a return from `merge_ok` or `conflicts`, the delta gets a precision pass and an external round within the cap; cleanup runs again only when that round finds something to fix.» Passes: full pass, confirmed

- **F5.19** [low] `BLUEPRINT.md:178`
  > | `task.merge` | work step | R10, R11 | Brings the base in, merges with a merge commit, checks by state, cleans up, Jira label and comment; `conflicts` back to build when the base changes the code or the local check is red; a red CI after the push leaves the pushed merge: `conflicts` for a cause in the work, the supervisor otherwise. Pushes to the pull request's branch as the developer does. Reads `design`, `merge-report`. |
  Флоу даёт `conflicts`, когда вливание цели конфликтует или локальная проверка красная по причине в работе (`flows/task.yaml:113-114`), а не когда «the base changes the code».
  Fix: «`conflicts` back to build when bringing the target in conflicts or a local check is red for a cause in the work». Passes: full pass, confirmed

## Оставлено планом

План ничего не оставляет («Leave: none»).

## Вопросы к человеку

1. Правило остановки не выполнено третий визит подряд: в правках визита 3 — 3 medium, high нет. Две из них (F2.11, F3.15) — следствия правок этого визита и правятся одной-двумя фразами. F3.16 редкая. Полный проход каждый раз находит новое в нетронутом тексте (сейчас 3 medium). Варианты: (а) ещё один короткий круг только по F2.11 и F3.15, потом релиз без нового круга оценки по остальному; (б) выпустить 0.1.2 сейчас и всё открытое отправить в бэклог 0.1.3. Рекомендация: (а). F3.15 может слить неархивированное изменение в базовую ветку, а F2.11 — потерянное правило, которое этот ран сам и убрал.
