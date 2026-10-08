# Отчёт о ките: sdlc 0.1.0

- Дата: 2026-10-08
- Кит: worktree рана `create/comet` (`.lado/worktrees/kit-comet/create-comet`), путь дан задачей
- Коммит: 1b8b2a9
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно прошлой версии этого же отчёта (коммит 04db724, база 451aee4), `git diff 451aee4 -- kit.yaml README.md BLUEPRINT.md agents flows skills`. Шаг `create`: весь кит новый, поэтому три прохода шли по всему тексту, а полный проход из «Re-evaluation» 5 не нужен.
- Проходы: три независимых суб-агента, у каждого рубрика, папка кита, зависимые скилы, прошлый отчёт и diff. Каждый начинал со своего места: 1 — роли, 2 — флоу по шагам, 3 — скилы. Проверку вырезанных правил я сделал сам по diff и таблице автора. 3 находки одного прохода отброшены, 4 оставлены как подтверждённые.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 8 находок (0 high, 5 medium, 3 low); 7 из 12 критериев без находок |
| Охват | весь кит (в `create` изменён весь текст): 19 файлов |
| Правило остановки | не выполнено: 0 high, 5 medium. Это совет для гейта релиза, не блок |

Счёт относится только к тому, что указано в строке «Охват»: он не сравним со счётом первого раунда (15 находок).

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
| Words in a role prompt | agents/analyst.md | 790 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 578 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 578 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 392 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 645 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.1.0-2026-10-08/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

1. В `merge` ограничить «undo the merge … push nothing» случаем локальной проверки. При красном CI мерж-коммит уже запушен, его нельзя сбрасывать (F5.5).
2. Сказать, считаются ли дешёвые minor с fate `fix` (в обычном раунде и после cleanup) «something to fix» для внешнего раунда и для сходимости (F3.6).
3. Проверять файлы настроек и на эпик-ветке, на которую аналитик ставит ветку рана (F2.4).
4. Решить про `max_visits: 5`: поднять лимит или принять, что обычный ран доходит до гейта лимита (F8.1).
5. Дописать супервизору, что делать после `cancel` (F12.2).

## Находки

### 1. Границы ролей

Права названы, новые правки их соблюдают. Ревьюер больше не пушит: «never push yourself» (`skills/review-loop/SKILL.md:85`). Вопросы разработчика и ревьюера идут через супервизора (`agents/developer.md:35`, `agents/reviewer.md:38`).

### 2. Передача между шагами

- **F2.4** [medium] `agents/supervisor.md:25`
  > origin/<base>:<path>`): the analyst puts each run's branch on the remote target, where an
  Проверка смотрит только `origin/<base>`, а аналитик ставит ветку рана на цель, которой может быть эпик-ветка: «or the epic's branch when the» (`agents/analyst.md:29`). Если эпик-ветка отрезана до того, как `.lado/sdlc.md` или `.lado/tracker.yaml` запушили, файлов в ней нет. Тогда каждый следующий шаг останавливается на «When the file is missing» (`skills/sdlc-settings/SKILL.md:18`). Это остаток F2.3.
  Fix: после reset аналитик проверяет оба файла и, если их нет, пишет супервизору. Либо супервизор проверяет и `origin/<target>` рана. Passes: 2/3 (третий проход дал low; взято medium)

### 3. Готовность и исходы

- **F3.6** [medium] `skills/review-loop/SKILL.md:125`
  > Minor findings do not start another round: a minor finding of the final round gets
  Дешёвая minor-находка получает `fix`: «cheap (the file is already open, no new test needed), otherwise `decline` with the» (`:50`). Внешний раунд идёт «only when the local pass leaves nothing to fix» (`:68`), сходимость после cleanup — «When the cleanup leaves nothing to fix» (`:122`). Правило выше снимает вопрос только для финального раунда. В обычном раунде, и после cleanup, где почти все находки minor, один ревьюер вернёт `changes` ради minor-фикса, другой пойдёт дальше и оставит строки `fix` невыполненными. При лимите в 5 визитов от этого выбора зависит, хватит ли визитов.
  Fix: одна фраза: «a minor `fix` rides with the next round's fixes and does not count as something to fix for the external round or the stop criterion», либо явно «cleanup `fix` findings go back to the developer and start the final round». Passes: 2/3

- **F3.7** [low] `agents/integrator.md:44`
  > `git reset --keep <the noted commit>`, so the unpushed merge commit goes. Otherwise
  По букве «Otherwise push» относится и к красной проверке с причиной вне работы, хотя `engineering-rules` («A red check») велит отправить её супервизору, ждать и перезапустить.
  Fix: «With every check green, push.» Passes: 1/3, confirmed (`agents/integrator.md:44-45` и `skills/engineering-rules/SKILL.md:38-45`)

### 4. Независимая проверка

Без изменений: ревью делает отдельная роль, `design_ok` читает `design`, `merge_ok` читает `ledger` и `merge-report`. Новый `reads: [design, merge-report]` у `merge` (`flows/task.yaml:104`) даёт интегратору то, что одобрил человек.

### 5. Противоречия

- **F5.5** [medium] `flows/task.yaml:112` (state `merge`)
  > work: undo the merge as your role says, push nothing, and name the files or the
  `do` велит откатить мерж и ничего не пушить и при красном CI. Но к моменту CI роль уже запушила мерж-коммит, а единственный откат в роли написан для незапушенного случая: «`git reset --keep <the noted commit>`, so the unpushed merge commit goes. Otherwise» (`agents/integrator.md:44`). Если сделать этот reset после пуша, это противоречит «Never force-push, never rewrite pushed history» (`skills/engineering-rules/SKILL.md:65`), а локальная ветка отстанет от удалённой, и следующий пуш разработчика без force будет отклонён. Если сделать revert и пуш, нарушится «push nothing». Интеграторы поступят по-разному.
  Fix: в `do` ограничить «undo the merge … push nothing» конфликтом и красной локальной проверкой. В роли (шаг 2): «a red CI: the merge commit is already pushed; leave it and report `conflicts`». Passes: 3/3

- **F5.6** [low] `agents/analyst.md:74`
  > scale write the design doc and, with `writing-plans`, the plan under `docs/superpowers/`;
  Шаблон writing-plans кладёт в файл плана строку «**For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans» (`writing-plans/SKILL.md:61`). План читает разработчик. Этих скилов в ките нет, и строка расходится с «Do the work yourself» (`agents/developer.md:27`). Новая строка аналитика «take the method only» убирает handoff из диалога, но не из файла.
  Fix: «leave the plan's execution header out». Passes: 1/3, confirmed (обе цитаты выше)

### 6. Дублирование

Повторы из F6.1 заменены указателями: «and resolve it), as your role describes ("Fixing").» (`flows/task.yaml:51`). Правило о ручном назначении теперь только в `skills/sdlc-settings/SKILL.md:57`. Предел раундов берётся из настроек (`skills/review-loop/SKILL.md:128`). Скрипт похожих абзацев не нашёл.

### 7. Когда звать человека

Канал к человеку теперь назван у всех рабочих ролей: «A question for the human, also where a skill says "ask your human partner", goes to the» (`agents/developer.md:35`), у ревьюера — «send it to the supervisor» (`agents/reviewer.md:38`), причина вне работы — `engineering-rules` «A red check». На лимите цикла супервизор советует `continue` или `cancel` (`agents/supervisor.md:53-54`).

### 8. Циклы на повторном визите

- **F8.1** [medium] `flows/task.yaml:62` (state `review`) — STILL OPEN (см. «Прежние находки»)
  > max_visits: 5
  Новый текст: «The visits hold the first local round, up to the round cap of external rounds and the» (`:71`). Это 1 + 3 + 1 = 5, но только при пределе 3 (предел — настройка проекта), когда ни один раунд, кроме первого, не уходит в локальные исправления без внешнего раунда, и когда нет повторного входа после отказа на `merge_ok` или `conflicts`: эти визиты считаются в те же 5. Что делать на лимите, теперь сказано, так что ран не зависает, но обычный ран будет доходить до гейта лимита.
  Fix: поднять `max_visits` (например, до 8) в флоу и в скелете блупринта, либо убрать фразу, которая привязывает визиты к пределу, и оставить лимит как редкий стоп. Passes: 2/3

### 9. Краткость и «зачем»

Новые правила несут причину, например «fixing code for it only breaks code» (`skills/engineering-rules/SKILL.md`, «A red check») и «where an unpushed file is missing» (`agents/supervisor.md:25-26`). Промпты в зелёной зоне, analyst.md — 790 слов из 800.

### 10. Описания скилов

Новый раздел «A red check» лежит в `engineering-rules`, и его description («Use whenever you change code, run checks, report them or commit») покрывает этот случай. Остальное без изменений: у каждого скила есть «Use when», зависимости объявлены своими папками, `tracker` — в `expects.skills`.

### 11. Нейтральность к провайдеру

Новые команды — только git (`git cat-file -e`, `git ls-remote`, `git rev-parse`); имён инструментов CLI и моделей нет.

### 12. Безопасность и рамки

- **F12.2** [medium] `agents/supervisor.md:54`
  > review visit) when the open findings are being fixed, or `cancel`.
  Супервизор теперь советует `cancel`, но в «When a run ends» описаны только `done` и `dropped` («At `dropped`», `:68`). После отмены задача в Jira остаётся «In Progress», PR открыт (возможно, с коммитом архива), воркеры и worktree на месте, и никто не говорит человеку, что осталось.
  Fix: «**At cancel**: tell the human what is left (the pull request, the Jira status, the worktree) and ask before closing anything or ending workers with `discard=true`.» Passes: 1/3, confirmed (`supervisor.md:54` и `:58-69`, где нет случая `cancel`)

- **F12.3** [low] `flows/task.yaml:28` (state `design`)
  > engineering decisions you took for the record. Report `dropped` only when the human
  Перевод в In Progress теперь идёт после проверки, что задача нужна (`agents/analyst.md:26`), но человек может согласиться бросить задачу и позже, во время диалога. Тогда задача остаётся «In Progress», и вернуть статус никто не просит. «with nothing written to Jira» (`agents/analyst.md:25`) относится только к шагу 3.
  Fix: в `dropped` (task.yaml или роль): «when you already moved the task, ask the human in the note whether to move it back». Passes: 1/3, confirmed (`agents/analyst.md:25-26` и `flows/task.yaml:28`)

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: «Find its cause before anyone acts on it: read the failure, and run the failing check alone» (`skills/engineering-rules/SKILL.md:40`), ссылки из integrator и review-loop. Остатки — F5.5, F3.7 |
| 2. Работа вне флоу, мерж без гейта | Закрыта: мерж после `merge_ok`; настройки коммитятся «only with their yes», пуш делает человек (`agents/supervisor.md:23-26`) |
| 3. Путь вне worktree рана | Закрыта: «Work only inside the run's worktree, with absolute paths» (`skills/engineering-rules/SKILL.md`, «Git»). Связанный остаток — F2.4 |
| 4. Вердикт без порога серьёзности | Закрыта: «the last round found no blocker and no confirmed important finding» (`skills/review-loop/SKILL.md:118`); пересечение для minor — F3.6 |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: `agents/analyst.md:64-66` ограничивает brainstorming и writing-plans, `agents/developer.md:35` перенаправляет «ask your human partner», `review-loop:85` «never push yourself». Остаток — F5.6 (строка в файле плана) |

## Не прослежено

Нет. Роли, шаг, гейты и скилы названы в разделе 3 блупринта, единственная жёлтая мера обоснована (R12, R4), `--compare`: «same as the plan in BLUEPRINT.md». Замечание автора верно: в строках трассировки `task.design` и `task.merge` нет новых `reads` (`build-report`; `design`, `merge-report`). Это расхождение описания, не структуры, и на сравнение со скелетом оно не влияет; лучше дописать при следующей правке блупринта.

## Прежние находки

- **F2.1** RESOLVED: `flows/task.yaml:104` «reads: [design, merge-report]».
- **F2.2** RESOLVED: `flows/task.yaml:13` «reads: [build-report]».
- **F2.3** RESOLVED для базовой ветки: `agents/supervisor.md:23` «…it is their file. Both». Остаток для эпик-ветки — новая F2.4.
- **F3.1** RESOLVED: `skills/engineering-rules/SKILL.md:38` «## A red check»; integrator и review-loop ссылаются на него.
- **F3.2** RESOLVED: `agents/integrator.md:46` «2. Wait for the pull request's CI, at most the CI time ceiling of **Checks**, then merge»; есть маршрут к супервизору для таймаута и отказа хоста.
- **F3.3** RESOLVED: `flows/task.yaml:76` «of `review-loop` holds, or the human accepted the open findings, recorded in».
- **F3.4** RESOLVED для финального раунда: `skills/review-loop/SKILL.md:125` «a minor finding of the final round gets»; то же пересечение в других раундах — новая F3.6.
- **F5.1** RESOLVED: `agents/analyst.md:64` «From `brainstorming` and `writing-plans` take the method only. The gate `design_ok` is».
- **F5.2** RESOLVED: `agents/analyst.md:51-52` «such rounds replace / brainstorming's one question at a time».
- **F5.3** RESOLVED: `skills/review-loop/SKILL.md:85` «never push yourself».
- **F5.4** RESOLVED: `agents/analyst.md:41` «precision passes from the first round, unless the diff proves risky; otherwise as full»; таблица больше не обещает облегчённый цикл.
- **F6.1** RESOLVED: `flows/task.yaml:51` «as your role describes ("Fixing")».
- **F6.2** RESOLVED: `skills/review-loop/SKILL.md:128` «After the round cap of **External reviewer** (`sdlc-settings`)».
- **F7.1** RESOLVED: `agents/developer.md:35`, `agents/reviewer.md:38`.
- **F8.1** STILL OPEN (2 из 3 проходов): `max_visits: 5` не изменён; что делать на лимите, теперь сказано. См. раздел 8.
- **F12.1** RESOLVED: `agents/analyst.md:25` «their agreement report `dropped`, with nothing written to Jira.»; статус меняется на шаге 4. Остаток — новая F12.3 (low).

## Вырезанные правила

| Вырезанное правило (файл:строка в базе) | Где оно теперь |
|---|---|
| `flows/task.yaml:50-51` «When the change is already archived, revert the archive commit first» | `agents/developer.md:65` «When the change is already archived, revert the archive first» → `openspec-changes` «After the archive» |
| `agents/analyst.md:23` «`tracker` cannot assign a task yet: when it is not assigned …» | `skills/sdlc-settings/SKILL.md:57` «Until `tracker` can assign a task or set a label on an existing one, the human does both»; указатель в `agents/analyst.md:27-28` |
| `agents/analyst.md:42` hotfix «precision pass and one external round» | Заменено намеренно (F5.4): «precision passes from the first round, unless the diff proves risky; otherwise as full» |
| `agents/integrator.md:42` «a red check is `conflicts` too» | `agents/integrator.md:42-44`, теперь с условием «whose cause is in the work» |
| `flows/task.yaml:108` «abort the merge, push nothing» | `flows/task.yaml:112` «undo the merge as your role says, push nothing» — расширено на CI, см. F5.5 |
| `flows/task.yaml:71` «Visits hold up to 3 external rounds, the cleanup pass and the final round.» | `flows/task.yaml:71-73` (новая формулировка, F8.1) |
| `skills/review-loop/SKILL.md:83` «Push before you ask: the external reviewer reads what is pushed.» | `skills/review-loop/SKILL.md:83-85` (пушит разработчик: `agents/developer.md:48` «Push the run's branch»; ревьюер сверяет удалённую голову) |
| `skills/review-loop/SKILL.md:123-124` «After 3 external rounds … ask the human … and do as they say» | `skills/review-loop/SKILL.md:128-129`, предел из настроек |
| `skills/review-loop/SKILL.md:120` «after the cleanup found nothing to fix» | `skills/review-loop/SKILL.md:121-123` «found no blocker and no important finding» |
| `agents/reviewer.md:38` «ask them during your step» | `agents/reviewer.md:38-40` «send it to the supervisor … Wait for the human's answer and record it in the ledger» |

Потерянных правил нет.

## Пропущено раньше

Нет. Весь текст в `create` считается изменённым, поэтому новые находки этого раунда стоят в своих критериях.

## Вопросы к человеку

Нет вопросов.
