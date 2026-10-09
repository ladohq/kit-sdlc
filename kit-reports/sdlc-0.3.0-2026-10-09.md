# Отчёт о ките: sdlc 0.3.0

- Дата: 2026-10-09
- Кит: worktree рана `improve/sdlc-3` (`.lado/worktrees/kit-sdlc/improve-sdlc-3`), путь дан задачей
- Коммит: cc15ba4 (визит 2 шага `evaluate`). Визит 1 оценивал 28c4210; его отчёт — коммит f844c13.
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка.
  - Сравнивается с `kit-reports/sdlc-0.2.1-2026-10-09.md` (артефакт `assessment`, коммит d345e4d), планом (`plan`) и отчётом визита 1.
  - База — коммит из плана, 60d650c. Изменение — `git diff 60d650c -- kit.yaml README.md BLUEPRINT.md agents flows skills`, 9 файлов.
  - Правки после визита 1 — `git diff f844c13 cc15ba4 -- …`, 4 файла: `skills/openspec-changes/SKILL.md` (шаг 3 «Archive»), `skills/sdlc-settings/SKILL.md` (одна строка), `flows/task.yaml` (`archive` do), раздел 5 `BLUEPRINT.md`. Сделаны по комментарию человека на `release_ok`.
- Проходы, визит 2:
  - Дельта — три правки по 1–5 строк, поэтому три прохода по ней и по местам, связанным с ней, критик сделал сам, без суб-агентов. Связанные места: «Archive» и «Merge» интегратора, «After the archive», шаг 4 аналитика.
  - Затем проверка вырезанных правил по `git diff --word-diff f844c13 cc15ba4`.
  - Затем полный проход по 4 файлам дельты целиком.
  - Новых находок нет; одиночных отброшенных нет.
- Визит 1: три прохода критика по изменению, проверка вырезанных правил, полный проход по 9 файлам диффа; одна находка оставлена как подтверждённая (F6.1).

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 1 открытая находка в изменённом тексте (0 high, 1 medium, 0 low) — F5.1, оставлена человеком; 3 находки визита 1 закрыты; 11 из 12 критериев без открытых находок |
| Охват | повторная оценка изменённого текста (визит 2: правки по комментарию на `release_ok`), с полным проходом по 4 файлам дельты |
| Правило остановки | не выполнено: 0 high, 1 medium (F5.1). Её оставил человек — требования к `tracker` вынесены в артефакт сессии `tracker-requirements`. Это совет к гейту релиза, не блок |

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

1. Назначение задачи: если `tracker` не может назвать аккаунт, от которого работает, а у задачи есть исполнитель, сначала спросить человека (F5.1). Человек отложил эту правку до релиза tracker-кита с требованиями из `tracker-requirements`.

## Находки

### 1. Границы ролей

Без находок. Изменение прав не меняет: мерж и архив по-прежнему у интегратора, аналитик только проверяет CLI хостинга.

### 2. Передача между шагами

Без находок. `merge` читает ledger: «    reads: [design, ledger, merge-report]» (`flows/task.yaml:114`). Что держит `merge-report`, сказано в одном месте — в роли (`agents/integrator.md:38-45`).

### 3. Готовность и исходы

Без открытых находок. F3.1 и F3.2 визита 1 закрыты (RESOLVED, см. «Прежние находки»). Откат неудачного архива теперь полный: «   untracked path under `openspec/` that `git status --short -- openspec/` lists (the» (`skills/openspec-changes/SKILL.md:78`).

### 4. Независимая проверка

Без находок. На `merge_ok` человек видит правило, что можно принять: «      was archived, approving accepts its open findings: approve only an unconverged» (`flows/task.yaml:109`). Гейт показывает `ledger` и `merge-report`.

### 5. Противоречия

- **F5.1** [medium] `skills/sdlc-settings/SKILL.md:79` — STILL OPEN, оставлена человеком
  > `  `tracker` which account that is and compare it with the task's assignee. Only when the`
  - У tracker-jira-server 1.1.0 нет действия «какой у меня аккаунт». Своё имя скил раскрывает только внутри `assign … me` (`jira.py:770`).
  - Учётные данные скил велит не трогать: «for them, print them or put them in a file. Whatever you write in Jira appears in the» (tracker `SKILL.md:22`).
  - Поэтому аналитик не может выполнить правило как написано: он или прочтёт `JIRA_USER`, или сравнит исполнителя по догадке.
  - Человек на `release_ok` решил не править это в sdlc сейчас: требования к `tracker` вынесены в артефакт сессии `tracker-requirements`, правка sdlc — после релиза tracker-кита (BACKLOG.md).

  Fix: «…compare it with the task's assignee; when `tracker` cannot tell you which account it works as and the task has an assignee, ask the human first.» Passes: 3/3 (визит 1)

### 6. Дублирование

Без открытых находок. F6.1 визита 1 закрыта: `archive` do больше не перечисляет причины, а отсылает к роли: «      can be archived, `merge-report` says why at its top (your role, "Archive"). Only an» (`flows/task.yaml:97`).

### 7. Когда звать человека

Без находок. Аналитик, если CLI хостинга не работает, говорит человеку сам (`agents/analyst.md:36`). Если архив в `merge` не удался, решение уходит разработчику через `conflicts`.

### 8. Циклы на повторном визите

Без находок. Путь `merge` → `conflicts` → `build` покрыт: «conflict, a red check or a refused archive from `merge`, as your role describes» (`flows/task.yaml:56`) и «Fixing» разработчика (`agents/developer.md:69-71`).

### 9. Краткость и причины

Без находок. В `review` do — ссылка на критерий вместо пересказа: «limit is rare. When to run the cleanup pass and the final round is `review-loop`,» (`flows/task.yaml:80`).

### 10. Описания скилов

Без находок. Команды объявлены, `gh` не объявлен, README их перечисляет (известная дыра 6). Состав итогового отчёта интегратор берёт из своей роли (`agents/integrator.md:27-30`).

### 11. Нейтральность к провайдеру

Без находок. Новая строка про статус — по смыслу, без синтаксиса трекера: «- a status as the task now shows it, by meaning, or that it already was there;» (`skills/sdlc-settings/SKILL.md:61`). R16 соблюдён: `grep -rniE "jira|jql|wiki|tracker\.yaml"` по ролям, флоу и скилам ничего не находит.

### 12. Безопасность и границы

Без находок. Удаление неотслеживаемых путей ограничено `openspec/`. Оно происходит только при красном `openspec validate` до коммита, и только то, что показывает `git status --short -- openspec/` (`skills/openspec-changes/SKILL.md:76-79`). В worktree рана под `openspec/` до архива нет неотслеживаемых файлов: работа закоммичена (`flows/task.yaml` `build`: «the work is committed and pushed»).

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка без учёта причины в окружении | Закрыта. Провал после архива откатывается целиком и уходит человеку через `merge-report`. Остальное — `engineering-rules`, «A red check» (`skills/engineering-rules/SKILL.md:40`) |
| 2. Работа вне флоу, мерж без гейта | Закрыта: мерж только после `merge_ok` |
| 3. Путь вне worktree рана | Закрыта; откат ограничен путём `openspec/` |
| 4. Вердикт без порога серьёзности | Закрыта: «the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:131`) |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта; скилы ролей не менялись |
| 6. Команды не совпадают с `expects.commands` | Закрыта: (a) `expects commands: git, openspec`, README `:17`; (b) `gh` не объявлен, его проверяет аналитик (`agents/analyst.md:35`); (c) `dependencies.lado: ">=0.30"` |

## Не прослежено

Ничего. `--compare` различий не печатает. Новая строка раздела 5 блупринта ссылается на этот отчёт и комментарий человека на `release_ok`. Жёлтая мера (Worker roles = 4) обоснована (R12, R4).

## Прежние находки

Находки отчёта assess (`kit-reports/sdlc-0.2.1-2026-10-09.md`, d345e4d) и плана:

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F2.1 `merge` не читает `ledger` | RESOLVED | «    reads: [design, ledger, merge-report]» (`flows/task.yaml:114`) |
| F2.2 Нет итогового отчёта без сходимости | RESOLVED | «closing report is the one in the ledger. When the ledger holds none, write it» (`agents/integrator.md:27`) |
| F3.1 Провал проверки после `openspec archive` | RESOLVED | grep до архива (`skills/openspec-changes/SKILL.md:71-72`), полный откат до коммита (`:76-80`) |
| F3.2 Одно правило на `merge_ok` | RESOLVED | «      unconverged review can be accepted at `merge_ok`; for any other cause the human» (`flows/task.yaml:98`); ask гейта (`:108-110`); «Merge» шаг 1 интегратора (`agents/integrator.md:49-53`) |
| F6.1 `merge` do пересказывает роль | RESOLVED | «is red for a cause in the work: what to undo and what to leave is your role's» (`flows/task.yaml:122`) |
| F6.2 `review` do пересказывает критерий остановки | RESOLVED | `flows/task.yaml:80-81` |
| F6.3 Состав `merge-report` дважды | RESOLVED | «file is left, and `merge-report` holds what your role lists ("Archive"). When nothing» (`flows/task.yaml:96`) |
| F10.1 Нет `expects.commands` | RESOLVED | `expects commands: git, openspec` |
| F10.2 CLI хостинга не проверяется | RESOLVED | `agents/analyst.md:35-36` |
| F3.7 (b0573e6) Чей аккаунт у исполнителя | RESOLVED по плану; выполнимость — F5.1 | `skills/sdlc-settings/SKILL.md:78-80` |
| F5.3 (b0573e6) Вводная фраза о перечитывании | RESOLVED | `skills/sdlc-settings/SKILL.md:56` |

Находки визита 1 этого отчёта:

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F3.1 [medium] Откат оставляет спеку новой capability | RESOLVED | «then delete every» … «untracked path under `openspec/` that `git status --short -- openspec/` lists (the» … «folder under `archive/`, the spec of a new capability)» (`skills/openspec-changes/SKILL.md:77-79`) |
| F3.2 [low] Вырезано «unless it is there already» | RESOLVED | «- a status as the task now shows it, by meaning, or that it already was there;» (`skills/sdlc-settings/SKILL.md:61`) |
| F5.1 [medium] `tracker` не говорит свой аккаунт | STILL OPEN, оставлена человеком | текст `skills/sdlc-settings/SKILL.md:79` не изменился (`git diff f844c13 cc15ba4 -- skills/sdlc-settings/SKILL.md` меняет только `:61`) |
| F6.1 [low] Причины «ничего не заархивировано» трижды | RESOLVED | `flows/task.yaml:96-97`: только ссылка «(your role, "Archive")» |

Список автора «fixed / not fixed» в `build-report` визита 2 совпадает с файлами.

## Вырезанные правила

Визит 2 (`git diff --word-diff f844c13 cc15ba4`):

| Вырезанное правило (файл:строка в f844c13) | Где оно сейчас |
|---|---|
| причины, по которым ничего не заархивировано (`flows/task.yaml:97-99`) | `agents/integrator.md:39-40` (состав `merge-report`); do отсылает туда (`flows/task.yaml:97`) |
| «say so at the top of `merge-report`» (`flows/task.yaml:98-99`) | «`merge-report` says why at its top (your role, "Archive")» (`flows/task.yaml:97`) |
| «delete the folder it created under `openspec/changes/archive/`» (`skills/openspec-changes/SKILL.md:77-78`) | шире: «delete every untracked path under `openspec/` … (the folder under `archive/`, …)» (`:77-79`) |

Визит 1: все вырезанные правила найдены на новых местах, кроме «unless it is there already» (F3.2). Теперь и это правило на месте (`skills/sdlc-settings/SKILL.md:61`). Таблица — в отчёте визита 1, коммит f844c13.

## Пропущено ранее

Нет. Полный проход по 4 файлам дельты нового вне изменённого текста не нашёл.

## Оставлено планом

План ничего не оставлял («Leave: none»). На `release_ok` человек оставил F5.1: требования к `tracker` (действие «какой у меня аккаунт») — в артефакте сессии `tracker-requirements`, правка sdlc — после релиза tracker-кита (BACKLOG.md).

## Вопросы человеку

Нет вопросов.
