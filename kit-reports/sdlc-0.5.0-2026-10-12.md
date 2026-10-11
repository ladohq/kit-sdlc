# Отчёт о ките: sdlc 0.5.0

- Дата: 2026-10-12
- Кит: worktree рана `improve/sdlc-5` (`.lado/worktrees/kit-sdlc/improve-sdlc-5`), путь дан задачей
- Коммит: 2d818eb (визит 1 шага `evaluate`)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка.
  - Сравнивается с `kit-reports/sdlc-0.4.0-2026-10-11.md` (артефакт `assessment`), планом (`plan`) и `build-report`.
  - База — коммит из плана, 82da29b (текст кита как в 5e0eccc). Изменение — `git diff 82da29b -- kit.yaml README.md BLUEPRINT.md agents flows skills`: 4 файла (`BLUEPRINT.md`, `kit.yaml`, `skills/openspec-changes/SKILL.md`, `skills/sdlc-settings/SKILL.md`), +30 −11 строк. Сюда входит и блупринт из `triage` (94b461f).
- Проходы:
  - Дельта небольшая, поэтому три прохода критик сделал сам, без суб-агентов, каждый со своей точки входа: 1 — `kit.yaml` и роли, которые находят change (аналитик, разработчик, ревьюер, интегратор, супервизор); 2 — `flows/task.yaml` по состояниям `design` → `build` → `review` → `merge`; 3 — `openspec-changes` и `sdlc-settings`, потом снова роли.
  - Проходы шли в одном контексте, поэтому независимыми их считать нельзя. Единственную находку критик подтвердил в файлах и прогоном `openspec` 1.14.0 во временной папке.
  - Затем проверка вырезанных правил по `git diff --word-diff 82da29b 2d818eb` и таблице «Cut text» из `build-report`.
  - Затем полный проход по 4 файлам дельты целиком (правило 5 «Re-evaluation»).
  - Строку `Task:` проверил на практике: change, у которого `proposal.md` начинается с `Task: LAD-5`, проходит `openspec validate git-check --strict` («Change 'git-check' is valid»).
  - Одиночных находок отброшено: 0; оставлено как подтверждённые: 1 (F3.1).

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 1 находка в изменённом тексте (0 high, 0 medium, 1 low: F3.1); F6.1 закрыта; F8.3 и F5.1 оставлены планом; 11 из 12 критериев без находок |
| Охват | повторная оценка изменённого текста, с полным проходом по 4 файлам дельты |
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
# Complexity budget: sdlc 0.5.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 4 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 799 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 693 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 563 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 463 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 751 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.5.0-2026-10-12/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

Схема побайтно совпадает со схемой 0.4.0: флоу не менялся.

## Исправить в первую очередь

1. Проверку занятого имени change распространить на открытые ветки других ранов или отдать на усмотрение человека (F3.1, low). Это не блокирует.

## Находки

### 1. Границы ролей

Без находок. Имя change выбирает тот, кто создаёт change, — аналитик: «Write the change as `openspec-changes` describes» (`agents/analyst.md:73`). Остальные роли имя не выбирают, а берут: «Later steps find the change by the name the artifact `design` gives, not by a prefix.» (`skills/openspec-changes/SKILL.md:40`).

### 2. Передача между шагами

Без находок. Имя change передаётся через артефакт, а не через префикс: аналитик пишет в `design` «the change's name, the scope and out of scope» (`agents/analyst.md:87`), а `build`, `review` и `merge` читают `design` (`reads: [design, ledger]`, `flows/task.yaml:44`; `reads: [design, build-report]`, `:70`; `reads: [design, ledger]`, `:98`). Задача говорила про «ноту шага design»; план выбрал артефакт `design`, и это надёжнее: нота после гейта может не дойти до `merge`, а артефакт читается явно. `design` содержит и строку `Task:`, потому что держит «each file of the change in full» (`agents/analyst.md:91`).

### 3. Готовность и исходы

- **F3.1** [low] `skills/openspec-changes/SKILL.md:43`
  > A name taken by an open change (`openspec/changes/<name>/`) or an archived one
  - Аналитик ищет занятые имена в дереве своей ветки, а её он ставит на цель: «the run's branch on the target: `git fetch origin` and `git reset --keep» (`agents/analyst.md:30`). Открытые change других ранов, которые идут параллельно, лежат на их ветках и в этом дереве не видны.
  - Пока имя начиналось с ключа задачи, два рана не могли получить одно имя. С именами по смыслу два рана могут выбрать одно и то же (`git-check`). Если оба смержатся в один день, архив второго падает: проверено на `openspec` 1.14.0 — «Error: Archive '2026-10-12-git-check' already exists.», код 1. Тот же путь получает и итоговый отчёт: `docs/superpowers/reports/<YYYY-MM-DD>-<name>-review.md` (`:35`).
  - Интегратор тогда сообщает `conflicts` («Commit nothing; the integrator reports `conflicts`.», `:89`), и ран возвращается в `build`. Правила, что делать с таким конфликтом имён, у разработчика нет: имя дал `design`. Случай редкий (то же имя и тот же день), а сам возврат ограничен `max_visits`.
  - Тот же разрыв касается и случая с тем же `Task:`: change, оставшийся от прежнего рана той же задачи на его ветке, аналитик не увидит.

  Fix: в абзаце о конфликте имён считать занятыми и имена на ветках удалённого репозитория: «… or one on another branch of the remote (`git ls-tree -d --name-only origin/<branch> openspec/changes/`)». Или, проще, одной фразой в «Archive» 2: архив, которого `openspec` не создаёт, потому что архив с таким именем уже есть, — это `conflicts`, и в `build` разработчик переименовывает change в другое имя того же смысла. Passes: 1/3, confirmed (цитаты найдены `grep -nF`; ошибку архива воспроизвёл `openspec archive git-check --yes` во временной папке)

### 4. Независимая проверка

Без находок. Флоу не менялся. Архив и мерж по-прежнему только после `merge_ok` (`flows/task.yaml:88-98`).

### 5. Противоречия

Без находок. правило читать один-два недавних change из `openspec/changes/archive/` и следовать их форме (`skills/openspec-changes/SKILL.md:15-16`) не спорит со строкой `Task:`: явное правило про первую строку сильнее «формы» старых change, а `openspec validate --strict` строку принимает (см. «Проходы»). Коммит архива `archive(<KEY>): <name>` (`:93`) по-прежнему несёт ключ, но это сообщение коммита, а не имя change; задача этого не запрещает.

### 6. Дублирование

Без находок. `sdlc-settings` не повторяет правило именования change, а ссылается на него: «An openspec change is named by its meaning instead (`openspec-changes`).» (`skills/sdlc-settings/SKILL.md:84-85`). Текст R16 в блупринте — запись требования, а не промпт.

### 7. Когда звать человека

Без находок. Человека спрашивают в одном случае: «the same `Task:` line (left from an earlier run of the same task), ask the human whether» / «to continue it or start anew.» (`skills/openspec-changes/SKILL.md:46-47`). Спрашивает аналитик, а он говорит с человеком напрямую (`agents/analyst.md:16-18`).

### 8. Циклы на повторном визите

Без новых находок. На повторном визите `design` аналитик правит уже закоммиченный change: «Revise the committed change, not a version from memory» (`flows/task.yaml:22`). Имя он заново не выбирает. Оговорка «left from an earlier run» не даёт принять собственный change рана за чужой. F8.3 — в «Оставлено планом».

### 9. Краткость и причины

Без находок. У нового правила есть причина: «task key: the archive outlives the tracker.» (`skills/openspec-changes/SKILL.md:38`). Запрет переименовывать старое тоже нужен, а не пуст: «Changes named otherwise before stay as they are; never rename one or the archive.» (`:41`). Без него роль могла бы привести старые change к новому правилу.

### 10. Описания скилов

Без находок. В описание `openspec-changes` добавлено «named» («How an openspec change is named, laid out, …», `skills/openspec-changes/SKILL.md:4`). Набор скилов у ролей не менялся.

### 11. Нейтральность к провайдеру

Без находок. Новый текст не называет ни CLI агента, ни модель, ни файлы настроек.

### 12. Безопасность и границы

Без находок. Новый текст ничего не переименовывает и не удаляет в репозитории: «never rename one or the archive» (`skills/openspec-changes/SKILL.md:41`).

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка без учёта причины в окружении | Дельта не трогает. Как в 0.4.0: «A red check whose» / «cause is in the work (`engineering-rules`, "A red check") is `conflicts` too» (`agents/integrator.md:30-31`). Отказ архива из-за занятого имени — F3.1 |
| 2. Работа вне флоу, мерж без гейта | Дельта не трогает. Архив и мерж — шаг `merge` после `merge_ok` |
| 3. Путь вне worktree рана | Закрыта. Новые пути — внутри `openspec/` репозитория: `openspec/changes/<name>/`, `openspec/changes/archive/*-<name>/` (`skills/openspec-changes/SKILL.md:43-44`) |
| 4. Вердикт без порога серьёзности | Дельта не трогает: «- the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:129`) |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта. В `openspec-changes` появилось «ask the human» (`:46-47`), а этот скил есть и у read-only ревьюера. Но правило касается выбора имени при создании change, а создаёт его только аналитик (`agents/analyst.md:73`), который говорит с человеком. Ревьюер не пишет: «change no file, branch or commit» (`agents/reviewer.md:17`) |
| 6. Команды не совпадают с `expects.commands` | Закрыта: `expects commands: git, openspec`; новый текст новых команд не вводит |
| 7. Read-only роли не совпадают с тем, что делают | Закрыта: (a) `read-only roles: reviewer`; (b) ревьюер не пишет и не коммитит; (c) `dependencies.lado: ">=0.34"` (`kit.yaml:16`); (d) README говорит про Codex CLI (как в 0.4.0) |

## Не прослежено

Ничего. `--compare` различий не печатает. Жёлтая мера (Worker roles = 4) обоснована (R12, R4); раздел 4 блупринта совпадает с выводом скрипта. Новое правило названо в R16 (`BLUEPRINT.md:126-135`) и в строке `openspec-changes` раздела 3 (`:236`). Раздел 5 ссылается на ран `task/lad-5`, задачу LAD-11 и `triage`.

## Прежние находки

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F6.1 [low] Строка `openspec-changes` в разделе 3 блупринта: «only for a red CI» | RESOLVED | «undoing a pushed archive when a fix after it changes the spec» (`BLUEPRINT.md:236`) |
| F8.3 [low] Ссылка разработчика на «After the archive» только при красном CI | STILL OPEN, оставлена планом | «it, and run the checks again. For a red CI from `merge`, fix the failing job the note» (`agents/developer.md:71`); `git diff 82da29b -- agents/developer.md` пуст |
| F5.1 [medium] `tracker` не говорит свой аккаунт | STILL OPEN, оставлена планом (с 0.3.0) | правило в `skills/sdlc-settings/SKILL.md` («Assigning the task or labelling it») не менялось |

Изменения плана:

| Изменение | Статус | Чем подтверждено |
|---|---|---|
| `sdlc-settings`, «The task key as a name»: ключом называются ран и ветка, change — нет | RESOLVED | «Runs are named by the task key as a name, and branches carry it as `<key>`. An openspec» (`skills/sdlc-settings/SKILL.md:84`); «converts the same way, so the run and the branch get the same name.» (`:89`) |
| `openspec-changes`: имя по смыслу, строка `Task: <KEY>` | RESOLVED | «`<name>` is kebab-case and names the change by its meaning (`export-csv`), never by the» (`skills/openspec-changes/SKILL.md:37`); «task instead: `Task: <KEY>`, the key as the tracker shows it (`Task: ABC-123`), no URL.» (`:39`) |
| Правило конфликта имён | RESOLVED, с оговоркой F3.1 | `skills/openspec-changes/SKILL.md:43-47` |
| `sdlc.example.md:20` — `<key>` только для веток | RESOLVED (без изменений) | `:19-22` — только шаблоны веток |
| Аналитик: имя по `openspec-changes`, `design` называет change | RESOLVED (без изменений) | `agents/analyst.md:73`, `:87`; промпт 799 слов, зелёный |
| Разработчик, ревьюер, интегратор находят change через `design` | RESOLVED (без изменений) | `grep -rnE 'abc-123-\|<key>-\|prefix' agents flows skills README.md` находит только шаблоны веток и новую строку `:40` |
| `supervisor.md:33` — имя рана | RESOLVED (без изменений) | «Name the run after the key» (`agents/supervisor.md:32`) |
| Без миграции старых change | RESOLVED | «Changes named otherwise before stay as they are; never rename one or the archive.» (`skills/openspec-changes/SKILL.md:41`) |
| `kit.yaml` 0.5.0; README | RESOLVED | `version: 0.5.0` (`kit.yaml:2`); README именование change не описывает |
| `BACKLOG.md`: запись о двух change, правящих одно требование; F6.1 исправлена | RESOLVED | `BACKLOG.md` (дифф выше в `build-report`) |

Список автора «fixed / not fixed» в `build-report` совпадает с файлами.

## Вырезанные правила

`git diff --word-diff 82da29b 2d818eb`, сверено с таблицей «Cut text» автора:

| Вырезанное правило (файл:строка в 82da29b) | Где оно сейчас |
|---|---|
| «Runs and openspec changes start with the task key as a name» (`skills/sdlc-settings/SKILL.md:84`) | для ранов и веток — там же (`:84`); для change снято намеренно (задача LAD-11), имя по смыслу (`skills/openspec-changes/SKILL.md:37`) |
| «so the run, the change and the branch get the same name» (`skills/sdlc-settings/SKILL.md:89`) | «so the run and the branch get the same name» (`:89`); снято намеренно |
| «`<name>` is kebab-case and starts with the task key as a name» (`skills/openspec-changes/SKILL.md:37`) | заменено намеренно (`:37-41`). Неявное следствие старого правила — имя change уникально между ранами — восстановлено правилом конфликта имён лишь для дерева своей ветки: F3.1 |
| строка `openspec-changes` раздела 3 блупринта «only for a red CI» | заменена (F6.1) |
| «Run, change and branch names are made from the task key» (`BLUEPRINT.md:126`) | «Run and branch names …» (`:126`) и новый текст R16 (`:129-135`) |
| analyst.md: «its name and `Task:` line included» (добавка автора, откачена) | `skills/openspec-changes/SKILL.md:37-41`, по таблице автора |

## Пропущено ранее

Нет. Полный проход по 4 файлам дельты нового вне изменённого текста не нашёл.

## Оставлено планом

- F8.3 (ссылка разработчика на «After the archive» только при красном CI): заморозка правил R15, нет факта из реального рана; остаётся в BACKLOG.
- F5.1: оставил человек на `release_ok` в `improve/sdlc-3`; записано в BACKLOG.

## Вопросы человеку

1. Чинить ли F3.1 сейчас? Совет: нет, записать в BACKLOG. Нужно два параллельных рана с одинаковым смысловым именем, смерженных в один день. Ран при этом не теряется, а возвращается в `build`. По правилу заморозки R15 лучше дождаться такого случая в реальном ране.
