# Отчёт о ките: sdlc 0.6.0

- Дата: 2026-10-21
- Кит: worktree рана `improve/sdlc-6` (`.lado/worktrees/lad-118/improve-sdlc-6`), путь дан задачей
- Коммит: fa281df (визит 3 шага `evaluate`; визит 2 — d00b86e, отчёт 48c57bc; визит 1 — 3df706f, его отчёт в git, e6ff550)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка.
  - Сравнивается с `kit-reports/sdlc-0.5.0-2026-10-12.md` (артефакт `assessment`), планом (`plan`) и `build-report`.
  - База — коммит из плана, 2d818eb. Изменение — `git diff 2d818eb -- kit.yaml README.md BLUEPRINT.md agents flows skills`: 6 файлов (`BLUEPRINT.md`, `README.md`, `agents/integrator.md`, `kit.yaml`, `skills/sdlc-settings/SKILL.md`, `skills/sdlc-settings/sdlc.example.md`), +89 −37 строк. Сюда входит и блупринт из `triage` (668dc80).
- Проходы:
  - Дельта небольшая, поэтому три прохода критик сделал сам, без суб-агентов, каждый со своей точки входа: 1 — `kit.yaml` и роли (интегратор, затем роли, которые читают **Tracker**: аналитик, разработчик, супервизор); 2 — `flows/task.yaml`, состояния `merge_ok` и `merge`; 3 — `sdlc-settings` и `sdlc.example.md`, потом снова интегратор.
  - Проходы шли в одном контексте, поэтому независимыми их считать нельзя. Обе находки изменённого текста подтверждены в файлах.
  - Затем проверка вырезанных правил по `git diff --word-diff 2d818eb 3df706f` и таблице «Cut text» из `build-report`.
  - Затем полный проход по 6 файлам дельты целиком (правило 5 «Re-evaluation»): одна находка, F10.1 (low).
- Визит 2: правки по находкам визита 1, `git diff e6ff550 d00b86e -- kit.yaml README.md BLUEPRINT.md agents flows skills` (4 файла; abf87c6 — F7.1, F5.1, F10.1; d00b86e — запасной вариант раздела для ненайденного значения, F7.1). Три прохода по правке и её окрестностям (шаг 6 интегратора, «Writing to the tracker», раздел «Install and start» README, места, которые на них ссылаются: R10, R16 блупринта), затем полный проход по этим 4 файлам. Находки визита 1 — в «Прежних находках». Новая: F10.2 (low), одиночная, подтверждена `grep`. Одиночных отброшено: 0.
- Визит 3: правка F10.2 по ответу человека на `release_ok`, `git diff 48c57bc fa281df -- kit.yaml README.md BLUEPRINT.md agents flows skills` — одно предложение R16 в `BLUEPRINT.md`, файлы кита не менялись. Три прохода по R16 и местам, на которые оно ссылается (R10, README `:26-30`), и полный проход по `BLUEPRINT.md`: новых находок нет. Одиночных отброшено: 0.
  - Визит 1. Одиночных находок отброшено: 1 (low: в записке интегратор называет только старую строку «Waiting for release label», а не старый шаблон «Merge comment»; README в «Upgrading from 0.5» называет обе, так что человек узнаёт обо всём). Оставлено как подтверждённые: 0.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | визит 3: 0 находок; F7.1, F5.1, F10.1 визита 1 и F10.2 визита 2 закрыты; 12 из 12 критериев без находок; F3.1 из 0.5.0 оставлена планом |
| Охват | повторная оценка изменённого текста; визит 1 — с полным проходом по 6 файлам дельты, визит 2 — по 4 файлам правок, визит 3 — по `BLUEPRINT.md` |
| Правило остановки | выполнено: 0 high, 0 medium |

Счёт относится только к тому, что указано в строке «Охват». С полной оценкой его сравнивать нельзя.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
read-only roles: reviewer
expects skills: tracker
expects commands: git, openspec
```

Expects: `expects skills: tracker`, `expects commands: git, openspec`.

Read-only: `read-only roles: reviewer`; `dependencies.lado: ">=0.34"` (`kit.yaml:16`).

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.6.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 4 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 799 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 693 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 656 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 463 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 751 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.6.0-2026-10-21/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

Флоу не менялся.

## Исправить в первую очередь

Нечего: открытых находок в изменённом тексте нет.

## Находки

### 1. Границы ролей

Без находок. Действия после мержа принадлежат только интегратору: «the actions of **After the merge** are part of the integrator's routine» (`skills/sdlc-settings/SKILL.md:59`); в таблице разделов **After the merge** нужен только ему (`:31`). Аналитик и разработчик читают **Tracker** только ради пропускаемых типов задач (`:30`).

### 2. Передача между шагами

Без находок. Раздел интегратор читает из `.lado/sdlc.md` («Read `.lado/sdlc.md` first», `agents/integrator.md:16`), цель мержа — из `design` (`reads: [design, ledger]`, `flows/task.yaml:98`). Что сделано и что нет, уходит в note_body: «note_body what was» / «done in git and in the tracker and what is left for the human» (`flows/task.yaml:103-104`).

### 3. Готовность и исходы

Без находок. Исход при неполном выполнении раздела назван: «the outcome stays `merged` and the» / «human decides.» (`agents/integrator.md:62-63`); это совместимо с «Report `merged` once the pull request's state is merged and the tracker routine is» (`flows/task.yaml:102`).

### 4. Независимая проверка

Без находок. Мерж и записи в трекер — по-прежнему после `merge_ok` (`flows/task.yaml:88-98`).

### 5. Противоречия

Без находок. F5.1 визита 1 закрыта: исключение стоит отдельной строкой после списка и покрывает статусы: «An action of **After the merge** that `tracker` lacks or refuses, or a status it names that» / «no status of the task fits, follows the integrator's rule instead of the ones above.» (`skills/sdlc-settings/SKILL.md:86-87`). «the ones above» — правила о недостающем действии (статус: «ask the human», `:77`; метка и исполнитель: `:81-82`), а не проверка записи (`:60-67`): фраза говорит только о случаях «lacks or refuses» и «no status … fits».

### 6. Дублирование

Без находок. Правило заполнения шаблона комментария — только у интегратора (`agents/integrator.md:55-58`); `sdlc-settings` о нём не говорит, пример держит только шаблон (`sdlc.example.md:32-42`). Текст R10 в блупринте — запись требования, а не промпт.

### 7. Когда звать человека

Без находок. F7.1 визита 1 закрыта: «section says. A value not found there: do what the section says for that case;» / «when it says nothing, the action is not done: never guess the value. An action» (`agents/integrator.md:59-60`). Сначала — запасной вариант самого раздела (это всё ещё «exactly what the settings' **After the merge** says», `:50`), без него действие не делается и попадает под уже написанное правило: «name in your note what was not done and why; the outcome stays `merged` and the» (`:62`). Угадывание запрещено в обоих случаях.

### 8. Циклы на повторном визите

Без новых находок. При возврате из `conflicts` шаг 6 до записи в трекер не доходит: пункты 1–3 сообщают `conflicts` раньше. Двойной комментарий после повторного мержа невозможен: мерж делается один раз (`agents/integrator.md:45-47`).

### 9. Краткость и причины

Без находок. Новые фразы меняют поведение. «the outcome stays `merged`» не пустая: без неё отказ трекера можно было бы принять за повод для `conflicts`. Ссылка «(the README says how to move it)» говорит интегратору, куда направить человека.

### 10. Описания скилов

F10.1 визита 1 закрыта: «For example, with [tracker-jira-server](https://github.com/ladohq/kit-tracker-jira-server) 1.6.0 or newer:» (`README.md:30`).

F10.2 визита 2 закрыта: «and the README names 1.6.0 or newer, the first that can also set a field (R10).» (`BLUEPRINT.md:135`), что совпадает с `README.md:27`, `:30`.

Описание `sdlc-settings` обновлено: «tracker's skipped issue types, what happens to the task after the merge» (`skills/sdlc-settings/SKILL.md:5`). Набор скилов у ролей не менялся.

### 11. Нейтральность к провайдеру

Без находок. Новый текст называет действия трекера по смыслу («add or remove a label, set a field», `skills/sdlc-settings/SKILL.md:55`), без команд какого-либо трекер-кита и без CLI агента. В примере нет слов проекта LADO: статус «Done», метки `merged` / `in-review`, поле `release` (`sdlc.example.md:44-46`).

### 12. Безопасность и границы

Без находок. Граница записи узкая: «and nothing else on the task» (`agents/integrator.md:52`); без раздела «touch the task not at all» (`:53`); обхода нет: «`tracker` lacks or the tracker refuses: look for no workaround, do the rest, and» (`:61`). Создать задачу по-прежнему можно только с явного «yes, create» (`skills/sdlc-settings/SKILL.md:70-71`).

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка без учёта причины в окружении | Дельта не трогает. Отказ трекера после мержа — не `conflicts`: «the outcome stays `merged` and the» / «human decides.» (`agents/integrator.md:62-63`) |
| 2. Работа вне флоу, мерж без гейта | Дельта не трогает: записи в трекер — шаг `merge` после `merge_ok`, ask гейта: «merges with a merge commit and updates the tracker» (`flows/task.yaml:94`) |
| 3. Путь вне worktree рана | Закрыта. Новый путь — `CHANGELOG.md` в примере (`sdlc.example.md:46`), то есть файл репозитория; README ссылается на `skills/sdlc-settings/sdlc.example.md` как на файл кита для человека, а не для агента |
| 4. Вердикт без порога серьёзности | Дельта не трогает: «- the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:129`) |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта. Новое правило записи для **After the merge** в `sdlc-settings` адресовано интегратору (`:59`, `:82-83`); у read-only ревьюера оно не вызывается, ревьюер в трекер не пишет («change no file, branch or commit», `agents/reviewer.md:17`) |
| 6. Команды не совпадают с `expects.commands` | Закрыта: `expects commands: git, openspec`; новых команд нет. Версия трекер-кита для `field` не проверяется `expects.skills` и названа в README (`:26-28`), см. F10.1 |
| 7. Read-only роли не совпадают с тем, что делают | Закрыта: (a) `read-only roles: reviewer`; (b) ревьюер не пишет и не коммитит; (c) `dependencies.lado: ">=0.34"` (`kit.yaml:16`); (d) README говорит про Codex CLI («the kit does not run on Codex», `README.md:17`) |

## Не прослежено

Ничего. `--compare` различий не печатает. Жёлтая мера (Worker roles = 4) обоснована (R12, R4); раздел 4 блупринта совпадает с выводом скрипта (integrator 630). **After the merge** названо в R2 (`BLUEPRINT.md:25`), R10 (`:81-103`), R11, в строках `task.merge` и `sdlc-settings` раздела 3 и в обратной проверке R10. Раздел 5 — две строки 0.6.0 со ссылкой на LAD-118 и `triage`.

## Прежние находки

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F3.1 [low] из 0.5.0: занятое имя change на ветке параллельного рана | STILL OPEN, оставлена планом | `git diff 2d818eb -- skills/openspec-changes agents/analyst.md` пуст; запись в `BACKLOG.md` |
| F7.1 [medium] визита 1: значение, которого нет там, где велит раздел | RESOLVED | «when it says nothing, the action is not done: never guess the value. An action» (`agents/integrator.md:60`; в d00b86e — сначала запасной вариант раздела, `:59`) |
| F5.1 [low] визита 1: исключение After the merge не покрывает статусы | RESOLVED | «no status of the task fits, follows the integrator's rule instead of the ones above.» (`skills/sdlc-settings/SKILL.md:87`) |
| F10.2 [low] визита 2: R16 блупринта называет 1.1.0 | RESOLVED | «and the README names 1.6.0 or newer, the first that can also set a field (R10).» (`BLUEPRINT.md:135`) |
| F10.1 [low] визита 1: пример установки с 1.1.0 | RESOLVED | «… kit-tracker-jira-server) 1.6.0 or newer:» (`README.md:30`) |
| F8.3 [low] из 0.4.0 | STILL OPEN, оставлена раньше (BACKLOG) | `git diff 2d818eb -- agents/developer.md` пуст |
| F5.1 [medium] из 0.3.0: `tracker` не говорит свой аккаунт | STILL OPEN, оставлена раньше (BACKLOG) | «Assign to the account `tracker` works as: ask» (`skills/sdlc-settings/SKILL.md:83`) — без изменений по смыслу |

Изменения плана:

| Изменение | Статус | Чем подтверждено |
|---|---|---|
| `sdlc-settings`: необязательный раздел **After the merge**, его отсутствие — не пробел | RESOLVED | «**After the merge** is optional: without it, or without its part for a merge, the» (`skills/sdlc-settings/SKILL.md:20`); строка таблицы `:31` |
| **Tracker** без метки и шаблона; описание | RESOLVED | «\| **Tracker** \| the issue types the routine skips \| analyst, developer, integrator \|» (`:30`); описание `:5` |
| «Writing to the tracker»: действия раздела — часть рутины; метка ±, поле; проверка записи | RESOLVED | `:55`, `:59`, `:65-66` |
| Отсутствующее или отказанное действие раздела — по правилу интегратора | RESOLVED, с оговоркой F5.1 | `:82-83` |
| `sdlc.example.md`: нейтральный пример base / epic | RESOLVED | `:29-49` |
| `integrator.md` шаг 6 | RESOLVED, с оговоркой F7.1 | `agents/integrator.md:50-63`; 656 слов, зелёный |
| Вариант `merge_ok` остаётся | RESOLVED | `:65-66` |
| README: настройки, строка `merge`, `field` и версии, «Upgrading from 0.5» | RESOLVED, с оговоркой F10.1 | `README.md:21-22`, `:26-28`, `:38-40`, `:59`, `:63-64`, `:66-72` |
| `kit.yaml` 0.6.0 | RESOLVED | `version: 0.6.0` (`kit.yaml:2`); описание («merges it with the tracker routine», `:8`) метку не называет |
| `flows/task.yaml` проверен | RESOLVED (без изменений) | `:7`, `:94`, `:102-104` не называют метку или комментарий как правило кита |
| Grep плана ничего не находит | Расходится с планом, намеренно | `grep -rniE 'waiting.for.release\|waiting_for_release\|comment only\|stays in review' …` находит `agents/integrator.md:54` и `README.md:68`. Обе строки требуют ответы плана («the integrator … names the old line»; «the README says how to move it»): план сам себе противоречил, автор выбрал ответы. Согласен |

Список автора «fixed / not fixed» в `build-report` совпадает с файлами.

## Вырезанные правила

`git diff --word-diff 2d818eb 3df706f`, сверено с таблицей «Cut text» автора:

| Вырезанное правило (файл:строка в 2d818eb) | Где оно сейчас |
|---|---|
| «Draft the comment from the settings' merge comment template» (`agents/integrator.md:50`) | шаблон — в разделе проекта («Post a comment by this template», `sdlc.example.md:32`); правило заполнения — `agents/integrator.md:55-58` |
| «post it, read it back» (`agents/integrator.md:54`) | проверка записи комментария по id — «a comment by the id `tracker` reports, among the task's comments» (`skills/sdlc-settings/SKILL.md:61`), на него ссылается шаг 6 («as `sdlc-settings`» / «says ("Writing to the tracker")», `agents/integrator.md:51-52`) |
| «set the» / «waiting-for-release label, or ask the human in your note to set it» (`agents/integrator.md:54-55`) | снято намеренно (LAD-118); метка — в разделе проекта (`sdlc.example.md:45`), недоступное действие — `agents/integrator.md:60-63` |
| «For a merge into» / «an epic branch: the comment only; the task stays in review» (`agents/integrator.md:55-56`) | снято намеренно; часть epic раздела проекта: «Post the comment above; change nothing else.» (`sdlc.example.md:49`) |
| «Change nothing else on» / «the task» (`agents/integrator.md:56-57`) | «and nothing else on the task» (`agents/integrator.md:52`) |
| **Tracker**: «the label for "waiting for release"», «the merge comment's template» (`skills/sdlc-settings/SKILL.md:28`) | строка **After the merge** (`:31`) |
| «Waiting for release label», «Merge comment» (`sdlc.example.md:27`, `:29`) | раздел **After the merge** примера (`:29-49`) |
| README: «comments in the tracker» (`:53`), «the tracker's label» (`:34`) | `README.md:59`, `:38-40` |
| BLUEPRINT R10: метка, двухчастный комментарий, epic без метки, ручная метка | новый текст R10 (`BLUEPRINT.md:83-103`) |

Потерянных правил нет.

Визит 2 (`git diff --word-diff e6ff550 d00b86e`): вырезано только «An action of **After the merge** that `tracker`» / «lacks or refuses follows the integrator's rule instead.» (`skills/sdlc-settings/SKILL.md:82-83` в e6ff550). Теперь правило — `:86-87`, шире прежнего (и статусы). В d00b86e фраза «a value not found there is an action not done: never guess it» переписана в «A value not found there: do what the section says for that case;» / «when it says nothing, the action is not done: never guess the value.» (`agents/integrator.md:59-60`): правило сохранено и дополнено. Потерянных правил нет.

## Пропущено ранее

Нет. Визит 2: полный проход по 4 файлам правки нового вне изменённого текста не нашёл; F10.2 вызвана правкой README. Визит 1: полный проход по 6 файлам дельты нашёл F10.1, но это строка рядом с изменённым текстом, которую изменение сделало неточной, поэтому она стоит среди находок.

## Оставлено планом

- F3.1 (0.5.0, low): заморозка правил R15, нужен факт из реального рана; человек одобрил на `release_ok` `improve/sdlc-5`; в BACKLOG.

## Вопросы человеку

Нет.

## Найдено по пути

Ничего.
