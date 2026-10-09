# Отчёт о ките: sdlc 0.3.0

- Дата: 2026-10-09
- Кит: worktree рана `improve/sdlc-3` (`.lado/worktrees/kit-sdlc/improve-sdlc-3`), путь дан задачей
- Коммит: 28c4210 (визит 1 шага `evaluate`)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.2.1-2026-10-09.md` (артефакт `assessment`, коммит d345e4d) и плана (`plan`). База — коммит из плана, 60d650c. Изменение: `git diff 60d650c -- kit.yaml README.md BLUEPRINT.md agents flows skills`, 9 файлов (101+, 65−). В них входит коммит блупринта bf2de2f из `triage`.
- Проходы: изменение небольшое, поэтому три прохода по нему и по связанным местам критик сделал сам, без суб-агентов. Связанные места: tracker SKILL.md v1.1.0 и его `jira.py`, «After the archive» скила `openspec-changes`, `design` do. Затем проверка вырезанных правил по `git diff --word-diff 60d650c` и таблице автора «Cut text». Затем полный проход по 9 файлам, которые трогает дифф, целиком. Одиночных находок 1, оставлена как подтверждённая (F6.1), отброшенных нет.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 4 находки в изменённом тексте (0 high, 2 medium, 2 low), из них 1 — потерянное правило; 9 из 12 критериев без находок |
| Охват | повторная оценка изменённого текста, с полным проходом по 9 файлам диффа |
| Правило остановки | не выполнено: 0 high, 2 medium (F3.1, F5.1) — совет к гейту релиза, не блок |

Счёт относится только к тому, что указано в строке «Охват». С полной оценкой (`assessment`) его сравнивать нельзя.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
expects skills: tracker
expects commands: git, openspec
```

Expects: `expects skills: tracker`, `expects commands: git, openspec`. Предупреждения о `dependencies.lado` нет: в kit.yaml `">=0.30"`.

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.3.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 5 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 799 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 639 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 793 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 419 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 753 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.3.0-2026-10-09/task.svg)

```
same as the plan in BLUEPRINT.md
```
(код выхода 0)

## Исправить в первую очередь

1. Откат провалившегося архива: удалять и неотслеживаемые файлы, которые архив создал в `openspec/specs/` для новых capability, а не только папку в `archive/` (F3.1)
2. Назначение: если `tracker` не может сказать, от какого аккаунта работает, а у задачи есть исполнитель, сначала спросить человека (F5.1)

## Находки

### 1. Границы ролей

Изменение прав не меняет. Без находок. Мерж и архив по-прежнему только у интегратора; аналитик проверяет CLI хостинга, но ничего им не делает.

### 2. Передача между шагами

Без находок. `merge` теперь читает ledger: «    reads: [design, ledger, merge-report]» (`flows/task.yaml:115`). Что держит `merge-report`, сказано в одном месте — в роли (`agents/integrator.md:38-45`).

### 3. Готовность и исходы

- **F3.1** [medium] `skills/openspec-changes/SKILL.md:77`
  > `   any commit: `git restore --staged --worktree -- openspec/` and delete the folder it`
  `git restore` возвращает только отслеживаемые файлы. Если дельта добавляет новую capability, `openspec archive` создаёт новый файл `openspec/specs/<capability>/spec.md` («merges the delta into `openspec/specs/`», `:74`). Он не отслеживается, и после отката остаётся на месте: правило велит удалить только папку в `openspec/changes/archive/`. Следующая фраза требует «`git status` then shows no change under `openspec/`» (`:78`), что после такого отката не выполняется. Что делать в этом случае, не сказано: интегратор то удалит файл, то оставит его, то застрянет. В `merge` по правилу «leave no uncommitted file behind» (`agents/integrator.md:52`) исход `conflicts` наступит с грязным worktree.
  Fix: «…and delete every untracked path under `openspec/` that `git status --short -- openspec/` lists (the folder under `archive/`, the spec of a new capability)». Passes: 3/3 (проходы критика)

- **F3.2** [low] `agents/analyst.md:26` (вырезанное правило)
  > `4. Move it to the "in progress" status and assign it to the account `tracker` works`
  Вырезано «unless it is there already». В таблице автора правило перенесено в «already so» в `sdlc-settings`. Но там «already so» относится только к исполнителю и метке: «- an assignee or label as `tracker` reports it after the write, or its "already so";» (`skills/sdlc-settings/SKILL.md:62`). Для статуса: «- a status as the task now shows it, by meaning;» (`:61`). На повторном визите `design` (после отклонения или `redesign`) аналитик снова попросит перевести задачу в «in progress», где она уже стоит. tracker-jira-server отвечает на это успехом («is already in», `jira.py:680`), а трекер, который откажет, остановит шаг. Для Jira вреда нет.
  Fix: вернуть «, unless it is there already» или дописать в `sdlc-settings` `:61`: «, or that it already was there». Passes: cut-rule check

### 4. Независимая проверка

Без находок. Человек на `merge_ok` теперь видит правило, что можно принять: «was archived, approving accepts its open findings: approve only an unconverged» (`flows/task.yaml:110`). Гейт по-прежнему показывает `ledger` и `merge-report`.

### 5. Противоречия

- **F5.1** [medium] `skills/sdlc-settings/SKILL.md:79`
  > `  `tracker` which account that is and compare it with the task's assignee. Only when the`
  У ожидаемого скила `tracker` (tracker-jira-server 1.1.0) нет действия «какой у меня аккаунт». Своё имя он раскрывает только внутри `assign … me` (`jira.py:770`: `login = {"me": os.environ["JIRA_USER"], ...`). Учётные данные он велит не трогать: «for them, print them or put them in a file. Whatever you write in Jira appears in the» (tracker `SKILL.md:22`, продолжение «never ask for them, print them or put them in a file»). Аналитик не может выполнить правило как написано. Он или прочтёт `JIRA_USER` вопреки скилу `tracker`, или сравнит по догадке (`get` показывает исполнителя отображаемым именем) и переназначит чужую задачу. Именно от этого правило и защищает. Нарушен и общий принцип раздела: для действия, которого у `tracker` может не быть, всегда есть запасной путь («when `tracker` can, do it; otherwise ask the human», `:77-78`). Здесь его нет. Это правка F3.7 (b0573e6), сделанная по плану: план предлагал «ask `tracker`, or by what it reports», не проверив, что `tracker` это умеет.
  Fix: «…compare it with the task's assignee; when `tracker` cannot tell you which account it works as and the task has an assignee, ask the human first.» Passes: 3/3 (проходы критика)

### 6. Дублирование

- **F6.1** [low] `flows/task.yaml:98` (state `archive`)
  > `      reason, a delta that contradicts a living spec, a failed validation), say so at the`
  Список причин «ничего не заархивировано» и правило «say so at the top of `merge-report`» теперь стоят в трёх местах: в `do` (`:97-99`), в роли («when nothing was archived, that and why (an unconverged review, a task not checked», `agents/integrator.md:39`) и в шаге 3 скила (`skills/openspec-changes/SKILL.md:79`). Копии пока сходятся, но F6.3 этого же рана как раз и убирала двойной перечень `merge-report`.
  Fix: в `do` оставить «When nothing can be archived, `merge-report` says why at its top (your role, "Archive")» и правило про `merge_ok`. Passes: 1/3, confirmed (три цитаты на месте: `grep -n "failed validation" flows/task.yaml agents/integrator.md agents/developer.md` даёт `flows/task.yaml:98` и `agents/integrator.md:40`; у разработчика тот же список в `agents/developer.md:70-71`)

### 7. Когда звать человека

Без находок. Аналитик, если CLI хостинга не работает, говорит человеку сам: «when it does not, tell the human and design only once they settle it.» (`agents/analyst.md:36`). Для аналитика, который говорит с человеком напрямую, это допустимо. Если архив в `merge` не удался, решение уходит разработчику через `conflicts` — так ответил человек на `triage`.

### 8. Циклы на повторном визите

Без находок. Новый путь `merge` → `conflicts` → `build` покрыт: «conflict, a red check or a refused archive from `merge`, as your role describes» (`flows/task.yaml:56`) и «Fixing» разработчика (`agents/developer.md:69-71`). Если коммита архива нет, «revert the archive first» условно («When the change is already archived», `agents/developer.md:65-66`).

### 9. Краткость и причины

Без находок. `review` do заменил пересказ ссылкой: «limit is rare. When to run the cleanup pass and the final round is `review-loop`,» (`flows/task.yaml:80`).

### 10. Описания скилов

Без находок. Команды объявлены, `gh` не объявлен, README их перечисляет (известная дыра 6). У интегратора по-прежнему нет `review-loop`. Состав итогового отчёта роль теперь даёт сама (`agents/integrator.md:27-30`), поэтому ссылка «(`review-loop`, "Closing report")» в шаге 4 скила (`skills/openspec-changes/SKILL.md:80`) для интегратора — только название, а не зависимость.

### 11. Нейтральность к провайдеру

Без находок. `gh auth status` дан как пример CLI хостинга из настроек («(e.g. `gh auth status`)», `agents/analyst.md:35`), а не как команда агентского CLI. R16 соблюдён: `grep -rniE "jira|jql|wiki|tracker\.yaml"` по ролям, флоу и скилам ничего не находит.

### 12. Безопасность и границы

Без находок. Откат архива не трогает закоммиченное: «undo the archive before» … «any commit» (`skills/openspec-changes/SKILL.md:76-77`). Неудавшийся архив в `merge` ничего не мержит: «uncommitted file behind (restore it as `openspec-changes` says), merge nothing and» (`agents/integrator.md:52`).

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка без учёта причины в окружении | Закрыта: провал после архива откатывается и уходит человеку через `merge-report`, а не разработчику как баг. Остальное — `engineering-rules`, «A red check» (`skills/engineering-rules/SKILL.md:40`) |
| 2. Работа вне флоу, мерж без гейта | Закрыта: мерж только после `merge_ok`; архив в `merge` — только при принятом несошедшемся ревью |
| 3. Путь вне worktree рана | Закрыта; откат ограничен путём `openspec/` |
| 4. Вердикт без порога серьёзности | Закрыта: «the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:131`) |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта; изменение скилов ролей не меняло |
| 6. Команды не совпадают с `expects.commands` | Закрыта: (a) `expects commands: git, openspec`, README: «You need LADO 0.30 or newer, `git` and the `openspec` CLI on the agents' PATH, the code» (`README.md:17`). (b) `gh` не объявлен, его проверяет аналитик (`agents/analyst.md:35`). (c) `dependencies.lado: ">=0.30"`, предупреждения нет |

## Не прослежено

Ничего. `--compare` различий не печатает. Новые строки раздела 3 — `openspec` и `git` (command, expects) — названы в R3 и R9. Шаг 6 аналитика попал в строку `task.design` и в R3. Жёлтая мера (Worker roles = 4) обоснована (R12, R4).

## Прежние находки

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F2.1 `merge` не читает `ledger` | RESOLVED | «    reads: [design, ledger, merge-report]» (`flows/task.yaml:115`) |
| F2.2 Нет итогового отчёта без сходимости | RESOLVED | «closing report is the one in the ledger. When the ledger holds none, write it» (`agents/integrator.md:27`) |
| F3.1 Провал проверки после `openspec archive` | RESOLVED, с оговоркой | grep до архива (`skills/openspec-changes/SKILL.md:71-72`), откат до коммита (`:76-79`); неполный откат — новая находка F3.1 |
| F3.2 Одно правило на `merge_ok` | RESOLVED | «top of `merge-report`. Only an unconverged review can be accepted at `merge_ok`;» (`flows/task.yaml:99`); ask гейта (`:109-111`); «Merge» шаг 1 интегратора (`agents/integrator.md:49-53`) |
| F6.1 `merge` do пересказывает роль | RESOLVED | «is red for a cause in the work: what to undo and what to leave is your role's» (`flows/task.yaml:123`) |
| F6.2 `review` do пересказывает критерий остановки | RESOLVED | ссылка на `review-loop`, «Stop criterion» (`flows/task.yaml:80-81`) |
| F6.3 Состав `merge-report` дважды | RESOLVED | «file is left, and `merge-report` holds what your role lists ("Archive"). When the» (`flows/task.yaml:96`) |
| F10.1 Нет `expects.commands` | RESOLVED | `expects commands: git, openspec` в выводе `lado kits check` |
| F10.2 CLI хостинга не проверяется | RESOLVED | «6. Check that the code host's tool of **Git** runs logged in (e.g. `gh auth status`);» (`agents/analyst.md:35`) |
| F3.7 (b0573e6) Чей аккаунт у исполнителя | RESOLVED по плану, но правка невыполнима с tracker-jira-server — новая находка F5.1 | `skills/sdlc-settings/SKILL.md:78-80` |
| F5.3 (b0573e6) Вводная фраза о перечитывании | RESOLVED | «task. A reported success is not proof that the tracker took it, so check each write by» (`skills/sdlc-settings/SKILL.md:56`) |

Список автора «fixed / not fixed» в `build-report` совпадает с файлами: все 11 пунктов плана сделаны.

## Вырезанные правила

| Вырезанное правило (файл:строка в базе) | Где оно сейчас |
|---|---|
| «the other workers reach the human through the supervisor» (`agents/analyst.md:18`) | у каждой из ролей: `agents/developer.md:35`, `agents/reviewer.md:40-42`, `agents/integrator.md` («goes to the supervisor»), `agents/supervisor.md:42-44` |
| «with their agreement report `dropped`» (`agents/analyst.md:25-26`) | «Report `dropped` only when the human agreed the task is no longer wanted» (`flows/task.yaml:28-29`) |
| «unless it is there already» (`agents/analyst.md:27`) | потеряно: F3.2 |
| столбец «Review» таблицы масштабов (`agents/analyst.md:43-44`) | `agents/reviewer.md:31-34` (breadth/precision и расширение для рискованного диффа) |
| условие готовности `archive`: «`openspec validate --specs --strict` passes» (`flows/task.yaml:94`) | шаг 3 скила, `skills/openspec-changes/SKILL.md:76` |
| состав `merge-report` в `archive` do (`flows/task.yaml:95-97`) | `agents/integrator.md:38-45` |
| «the human sends it back, or accepts the open findings there» (`flows/task.yaml:99-100`) | одно правило: `flows/task.yaml:99-100`, ask гейта `:109-111` |
| пересказ «Merge» в `merge` do: откат, «push nothing», CI после пуша, внешние причины (`flows/task.yaml:121-127`) | шаги 2–3 «Merge» роли (`agents/integrator.md:54-67`); внешние причины остались в do (`:125-127`) |
| «archive first ("Archive"), then merge» (`flows/task.yaml:117-118`) | шаг 1 «Merge» роли (`agents/integrator.md:49-50`) |
| правило cleanup / финального раунда в `review` do (`flows/task.yaml:79-80`) | `skills/review-loop/SKILL.md:134-136` |
| «read the task back after each write» (`skills/sdlc-settings/SKILL.md:56`) | пункты проверки записи `:58-63`; комментарий — «read it back» (`agents/integrator.md`, «Merge» шаг 6) |
| «when someone else has the task, ask the human first» (`skills/sdlc-settings/SKILL.md:79`) | `:80`: «task is assigned to another account, ask the human first» (о выполнимости — F5.1) |
| шаг 3 «grep for contradictions» после архива (`skills/openspec-changes/SKILL.md:75-76`) | шаг 1, до архива (`:71-72`) |
| «with the closing report from the ledger» (`agents/integrator.md:24-25`) | `agents/integrator.md:27` |

## Пропущено ранее

Нет. Полный проход по 9 файлам диффа нового вне изменённого текста не нашёл.

## Оставлено планом

Нет: план ничего не оставляет («Leave: none»).

## Вопросы человеку

Нет вопросов.
