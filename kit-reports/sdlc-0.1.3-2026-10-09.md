# Отчёт о ките: sdlc 0.1.3

- Дата: 2026-10-09
- Кит: worktree рана `improve/sdlc` (`.lado/worktrees/kit-sdlc/improve-sdlc`), его задала задача рана
- Коммит: 4b7d998
- Оценил: критик kit-builder (слои a и b)
- Режим: полная оценка (шаг `assess`). Отчёт `kit-reports/sdlc-0.1.2-2026-10-08.md` был повторной оценкой без полного прохода, а после его коммита (c51a486) изменился README (4b7d998, способ установки). Поэтому он только фон.
- Проходы: три независимых суб-агента. У каждого были рубрика, папка кита, зависимые скилы (superpowers v6.4.1, grilling v1.2.3) и ожидаемый скил `tracker` из `tracker-jira-server` v1.0.0 (взят из кэша LADO, кит не установлен). Старых отчётов проходы не видели. Начинали с разных мест: 1 — kit.yaml и роли, 2 — флоу по шагам, 3 — скилы. Каждого просили отдельно перечислить, где текст привязан к Jira. Отброшено 7 одиночных находок, все low: повтор команды push у интегратора, повтор списка `merge-report` в `do`, повтор шага 2 «Merge» в `do`, список артефактов гейтов у супервайзера, история Comet в `openspec-changes`, путь к `sdlc.example.md` без `${KIT_DIR}`, гейты, которые не называют push и удаление ветки. Ещё одну кандидатуру проход 3 снял сам: проверить `openspec validate` без дельты было нечем. Оставлено как подтверждённые 4: F3.1, F6.1, F7.1, F11.1.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 18 находок (1 high, 10 medium, 7 low); 2 из 12 критериев без находок. Привязка к Jira: 8 находок (F5.1–F5.5, F9.2, F10.1, F10.2) |
| Охват | полная оценка всего кита: 17 файлов (kit.yaml, README, BLUEPRINT, 5 ролей, 1 флоу, 8 файлов скилов) |

Счёт относится только к тому, что указано в строке «Охват». С повторной оценкой его сравнивать нельзя.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
expects skills: tracker
```

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.1.3

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 5 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 798 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 614 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 641 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 419 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 739 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.1.3-2026-10-09/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

1. Не требовать `.lado/tracker.yaml`: это файл одного трекер-кита. С другим трекером он блокирует каждый ран на `design` (F5.1).
2. В ролях называть действия трекера, а не синтаксис `jira.py`: `get --comments 50`, `link --title`, `comment -` (F10.1).
3. Разметку комментария при мерже брать из скила `tracker`, а не фиксировать «Jira wiki markup» (F5.2).
4. Хранить слова статусов в одном месте и снять условие «Until `tracker` can…» (F5.3, F5.4).
5. Описание кита, README и описание флоу: «трекер задач», а tracker-jira-server только как пример (F10.2, F9.2).

## Находки

### 1. Границы ролей

Права у каждой роли названы: «mock-ups; code is the developer's.» (`agents/analyst.md:14`), «You are read-only on the code: you read, run checks and comment on the pull request, and» (`agents/reviewer.md:15`). Изменения пишет только та роль, которой принадлежит шаг. Конфликт ревьюера со скилом `engineering-rules` вынесен в F5.6.

### 2. Передача между шагами

- **F2.1** [medium] `flows/task.yaml:113` (state `merge`)
  > `    reads: [design, merge-report]`
  `do` шага `merge` ведёт в «Archive first» («When `merge-report` says nothing was archived and the human accepted the findings at», `:117`). «Archive» проверяет схождение по ledger и пишет закрывающий отчёт из него (`agents/integrator.md:22`, `:24`), но `ledger` шаг не читает. Это часть F3.17 отчёта 0.1.2, которую при релизе 0.1.2 приняли открытой.
  Fix: `reads: [design, ledger, merge-report]`. Passes: 2/3

### 3. Готовность и исходы

- **F3.1** [medium] `flows/task.yaml:98` (states `archive`, `merge`)
  > `      review converged, a task not checked without a reason, a delta that contradicts a`
  «accepts the open findings» на `merge_ok` предлагается для всех трёх причин. Но дельту, которая противоречит живому спеку, согласием не заархивировать: `openspec validate --specs --strict` упадёт снова, а у `merge` нет исхода на этот случай («    outcomes: {merged: done, conflicts: build}», `:128`). Это F3.17 из отчёта 0.1.2, принятая при релизе.
  Fix: принять можно только несошедшееся ревью; если архив в `merge` не удался, слить ничего нельзя, дело идёт к супервайзеру, ран остаётся в `merge`. Passes: 1/3, confirmed (`flows/task.yaml:128`; `skills/openspec-changes/SKILL.md:74` «3. `openspec validate --specs --strict` passes, and the grep for contradictions (above)»)

### 4. Независимая проверка

- **F4.1** [low] `agents/analyst.md:91` (gate `design_ok`)
  > `then each file of the change in full under its path, and the names of the mock-ups.`
  Гейт читает только `design` («reads: [design]», `flows/task.yaml:37`). Внешний вид человек одобряет по именам макетов, а сами макеты гейт не показывает.
  Fix: аналитик прикладывает `mockup-*.html` к записке `ready` через `artifacts` в `flow_advance`. Passes: 2/3

### 5. Противоречия

- **F5.1** [high] `agents/analyst.md:33` (state `design`)
  > `   and `.lado/tracker.yaml` are on it; when one is missing, send the supervisor its name`
  `.lado/tracker.yaml` — файл настроек tracker-jira-server. `expects.skills: [tracker]` пускает любой трекер-кит, а у другого такого файла нет. Тогда каждый ран останавливается на `design` в ожидании файла, который никто не читает. Супервайзер ещё до первого рана объявляет файл пропавшим: «Read `.lado/sdlc.md` (`sdlc-settings`) and the tracker's `.lado/tracker.yaml`. When» (`agents/supervisor.md:19`). То же в `skills/sdlc-settings/SKILL.md:8`. Проходы оценили high, high и medium; взят high.
  Fix: проверять только `.lado/sdlc.md`, а настройки трекера — «файл настроек, который называет скил `tracker`, если он его называет». Так в обеих ролях и в `sdlc-settings`. Passes: 3/3

- **F5.2** [medium] `agents/integrator.md:29` (state `archive`)
  > `   Jira language, in Jira wiki markup. Write it from the pull request's description and`
  Шаблон тоже в wiki-разметке: «- Merge comment (wiki markup):» (`skills/sdlc-settings/sdlc.example.md:30`, строки 33–39). Комментарий публикуется «as it is» (`agents/integrator.md:63`). Трекер с Markdown (GitHub, GitLab, Jira Cloud) покажет команде сырые `*…*`, а отменить публикацию нельзя.
  Fix: «в разметке, которую называет скил `tracker`». Шаблон в примере пометить как пример для Jira Server. Passes: 3/3

- **F5.3** [medium] `skills/sdlc-settings/SKILL.md:28`
  > `| **Jira** | the tracker's status words for "in progress" and "review", the label for "waiting for release", the issue types the routine skips, the merge comment's template | analyst, developer, integrator |`
  Строка 8 того же файла говорит обратное: «  Jira's own settings (project key, statuses) are the tracker's `.lado/tracker.yaml`.». Скил `tracker` сам сопоставляет слово «review» со статусом через `statuses:`. Получаются две карты статусов, которые разойдутся, и не сказано, по какой из них переводить задачу.
  Fix: слова статусов держать в одном месте. Рекомендация: в `.lado/sdlc.md`, раздел **Tracker**; строку 8 сузить до «ключ проекта и доступ — в настройках трекера». Passes: 3/3

- **F5.4** [medium] `skills/sdlc-settings/SKILL.md:57`
  > `Until `tracker` can assign a task or set a label on an existing one, the human does both`
  Условие привязано к возможностям tracker-jira-server v1.0.0, а роли всё равно пишут «by hand» без условия: `agents/analyst.md:28` «is not assigned to the human, ask them in your `ready` note to assign it by hand», `agents/integrator.md:64` «human in your note to set the waiting-for-release label by hand (`sdlc-settings`,». Если трекер умеет назначать задачу или ставить метку, один агент сделает это сам (запись, которую рутина не называет, `:50`), а другой попросит человека.
  Fix: одно правило в `sdlc-settings`: «если у скила `tracker` есть такое действие, рутина выполняет его; иначе человек делает это вручную». Роли ссылаются на него. Passes: 3/3

- **F5.5** [medium] `agents/supervisor.md:33`
  > `preferred scale, an epic). Name the run after the key, lowercase (`abc-123`), and give`
  Ран, изменение («`<name>` is kebab-case and starts with the task key in lowercase: `abc-123-export-csv`.», `skills/openspec-changes/SKILL.md:37`) и ветки (`sdlc.example.md:19-21`) рассчитаны на ключ вида `ABC-123`. Ключи вида `#123` или `owner/repo#123` дают в именах `#` и `/`, и агенты каждый раз выкручиваются по-своему. Проходы оценили low, low и medium; взят medium.
  Fix: «ключ, приведённый к нижнему регистру, буквам, цифрам и `-` (`abc-123`, `gh-123`)». Эпик — «когда у трекера есть эпики». Passes: 3/3

- **F5.6** [medium] `agents/reviewer.md:16`
  > `change no file, branch or commit; no checkout, reset or stash in the run's worktree,`
  Ревьюер запускает проверки, а его скил `engineering-rules` велит «  commit; restore tracked files a build rewrote.» (`skills/engineering-rules/SKILL.md:54`). Либо он нарушит режим только чтения, либо молча оставит разработчику грязный worktree. Известная дыра 5. В отчёте 0.1.2 это F1.5 («Пропущено раньше», low). Проходы оценили low и medium; взят medium.
  Fix: «файлы, которые проверка создала или переписала: перечислить в ledger для разработчика, не трогать». Passes: 2/3

### 6. Дублирование

- **F6.1** [medium] `agents/reviewer.md:31-32`
  > `Choose the passes by the round and the design's review depth: breadth on the first round`
  > `of a full change, precision otherwise. A hotfix or tweak whose diff turns out to touch`
  Правило выбора прохода живёт в трёх местах, и копии уже расходятся. `review-loop` даёт breadth ещё и «design's review depth decide), and on a round after a fix that reshaped the design;» (`skills/review-loop/SKILL.md:65`), а роль говорит «precision otherwise». Таблица аналитика (`agents/analyst.md:45`) — третья редакция. От рана к рану проход будет выбираться по-разному.
  Fix: правило оставить в `review-loop`, в роли и в таблице сослаться на него. Passes: 1/3, confirmed (`skills/review-loop/SKILL.md:65` против `agents/reviewer.md:32`)

- **F6.2** [low] `flows/task.yaml:78-80` (state `review`)
  > `      cleanup and final rounds, and a return after `merge_ok` or `conflicts`; reaching the`
  `do` пересказывает правило cleanup и финального раунда из `review-loop` («  the cleanup leaves nothing to fix, the round that converged is the final one. An», `skills/review-loop/SKILL.md:136`).
  Fix: в `do` оставить ссылку на «Stop criterion». Passes: 2/3

### 7. Когда звать человека

- **F7.1** [low] `skills/review-loop/SKILL.md:146`
  > `fails, ask the human with the open findings and your recommendation, and do as they say.`
  Ревьюер — воркер. Выше тот же скил ведёт к человеку через супервайзера («whether to go on without the external round, and do as they say (a skip counts as run:», `:123`, после «ask the human through the supervisor»), а здесь — напрямую. Ревьюер может задать вопрос через `ask_human` в обход ведущего (`agents/reviewer.md:40-41`).
  Fix: «ask the human through the supervisor». Passes: 1/3, confirmed (`skills/review-loop/SKILL.md:122`)

### 8. Циклы на повторном визите

- **F8.1** [low] `flows/task.yaml:92` (state `archive`)
  > `      Archive the change inside the pull request (`openspec-changes`, "Archive") and`
  После `merge_ok` → `rejected` или `conflicts` ран снова приходит в `archive`, но `do` молчит о повторном визите. На втором `merge_ok` человек видит новый `merge-report` без причины возврата и без того, что изменилось.
  Fix: на повторном визите `merge-report` начинается с причины возврата и коммитов после прошлого `merge_ok`. Passes: 2/3

### 9. Краткость и «зачем»

- **F9.1** [low] `skills/review-loop/SKILL.md:53`
  > `the fixes of its round; when its round sends nothing back, with the next fixes; it gets`
  Правило о судьбе minor `fix` (строки 49–57) — цепочка через точку с запятой с исключением в хвосте. Читают его по-разному.
  Fix: три пункта: когда отправляется, когда получает `decline`, исключение для cleanup. Passes: 2/3

- **F9.2** [low] `skills/sdlc-settings/SKILL.md:48`
  > `## Writing to Jira`
  Нейтральные правила записи и раздел настроек **Jira** названы по одному трекеру. На них ссылаются пять мест (`flows/task.yaml:32-33`, `agents/analyst.md:29`, `agents/integrator.md:65`, `skills/sdlc-settings/SKILL.md:16`, `:55`). Агент с другим трекером может решить, что ограничение его записей не касается.
  Fix: «Writing to the tracker» и раздел **Tracker** — в скиле, в `sdlc.example.md` и во всех ссылках. Passes: 3/3

### 10. Описания скилов

- **F10.1** [medium] `agents/analyst.md:22` (state `design`)
  > `1. Read the task (tracker `get <KEY> --comments 50`) and each task it links to.`
  Роли переписывают синтаксис `jira.py` из tracker-jira-server: «3. Link the pull request to the task with the tracker (`link <KEY> <url> --title» (`agents/developer.md:55`), «   (`comment <KEY> -`, the text on stdin, posted as it is), read it back, and ask the» (`agents/integrator.md:63`), а также `get` в `agents/supervisor.md:30` и `skills/sdlc-settings/SKILL.md:51`. Это текст скила, вставленный в промпт. `expects` обещает только скил по имени `tracker`, а не его командную строку. С другим трекером агенты запустят несуществующие команды или каждый раз будут переводить их сами.
  Fix: назвать действия («прочитать задачу с комментариями», «привязать pull request», «опубликовать комментарий», «перечитать задачу») и оставить синтаксис скилу. Если трекер не умеет привязывать ссылки, URL идёт комментарием. Passes: 3/3

- **F10.2** [medium] `kit.yaml:4`
  > `  Day-to-day development of a project whose tasks live in Jira Server, one task per run:`
  Описание кита, README («A LADO kit for one developer's day-to-day work on a project whose tasks live in Jira», `README.md:3`, и строки 7, 18, 26–27, 41–47, 57, 63) и описание флоу (`flows/task.yaml:3`, `:8`) говорят, что кит работает только с Jira Server. Блупринт же говорит иное: «session does (now `tracker-jira-server`; another tracker kit with a skill of the same» (`BLUEPRINT.md:72`). Пользователь другого трекера решит, что кит ему не подходит, и не узнает, что кит требует от скила `tracker`. Проходы оценили medium и low; взят medium.
  Fix: «задачи в трекере (например, Jira Server через tracker-jira-server)». В README — строка со списком действий, которые кит ждёт от `tracker`: прочитать с комментариями, сменить статус, комментировать, привязать ссылку. Команды установки и запуска с tracker-jira-server оставить как пример. Passes: 2/3

### 11. Нейтральность к провайдеру

- **F11.1** [low] `skills/review-loop/SKILL.md:151`
  > `(`references/lenses.md`) on the whole diff: cleaning earlier only polishes code about to`
  Строки 67 и 74 того же скила пишут `${SKILL_DIR}/references/…`, а здесь путь относительный. Так же в `engineering-rules` («shapes, and the project's own, are in the skill `review-loop` (`references/», `skills/engineering-rules/SKILL.md:35`). В worktree рана относительный путь указывает в проект. Известная дыра 3.
  Fix: `${SKILL_DIR}/references/lenses.md`. В `engineering-rules` — «the false-greens reference of the skill `review-loop`». Passes: 1/3, confirmed (`skills/review-loop/SKILL.md:67` пишет `${SKILL_DIR}/references/lenses.md`)

Команд одного CLI нет. `gh pr merge` идёт с оговоркой «or the settings' tool's equivalent» (`agents/integrator.md:49-50`).

### 12. Безопасность и рамки

Мерж, удаление удалённой ветки и комментарий в трекере идут только после `merge_ok`. Запись вне рутины требует явного «да»: «routine does not name, needs the human's explicit "yes, create" for that very action;» (`skills/sdlc-settings/SKILL.md:53`). Супервайзер коммитит `.lado/sdlc.md` «only with their yes» (`agents/supervisor.md:23`) и спрашивает, прежде чем завершать воркеров с `discard=true`. Force push запрещён (`skills/engineering-rules/SKILL.md:65`).

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: «finding or a fix. A cause outside it (a missing tool, no network, a runner that fails at» (`skills/engineering-rules/SKILL.md:42`). На это правило ссылаются разработчик (`:37`), интегратор (`:44-47`, `:53-56`) и `review-loop:73`. Неудавшийся архив в `merge` без такого разделения — F3.1 |
| 2. Работа вне флоу, мерж без гейта | Закрыта: мерж только после `merge_ok`. Супервайзер правит только `.lado/sdlc.md` и только с «да» человека (`agents/supervisor.md:23`), а `git pull --ff-only` делает только на чистой базовой ветке (`:62-63`) |
| 3. Путь вне worktree рана | Работа: «- Work only inside the run's worktree, with absolute paths; leave it only by path, never» (`skills/engineering-rules/SKILL.md:58`). Относительные пути к файлам скила — F11.1. Файл одного трекера `.lado/tracker.yaml` — F5.1 |
| 4. Вердикт без порога серьёзности | Закрыта: «- the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:131`), уровни на `:44-47` |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | `engineering-rules` у ревьюера, который только читает, — F5.6. Остальное закрыто: от brainstorming, grilling и writing-plans аналитик берёт только метод (`agents/analyst.md:67-69`), «ask your human partner» идёт через супервайзера (`agents/developer.md:35`) |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Единственная жёлтая мера (Worker roles = 4) обоснована в блупринте (R12, R4). Блупринт тоже везде пишет Jira, но R11 (`BLUEPRINT.md:71-73`) сам говорит, что подойдёт любой трекер-кит со скилом `tracker`. Цель рана с ним не расходится.

## Оставлено планом

Плана ещё нет: это шаг `assess`. По задаче три открытые medium отчёта 0.1.2 (F3.17, F2.12, F8.5) были приняты при релизе 0.1.2 и вне рамок этого рана. В этой оценке F3.17 видна как F2.1 и F3.1. F2.12 (проба ревьюера) и F8.5 (пустая дельта после отказа на `merge_ok`) проходы заново не нашли. Тексты, на которых они держались, не менялись, так что считать их закрытыми нельзя. F3.18 из «Пропущено раньше» отчёта 0.1.2 (ожидание CI ревьюером) тоже заново не найдена. F1.5 из того же раздела вернулась как F5.6.

## Вопросы к человеку

1. Разметка комментария при мерже. Рекомендация: «в разметке, которую называет скил `tracker`»; текущий шаблон оставить в `sdlc.example.md` как пример для Jira Server (F5.2).
2. Где хранить слова статусов («в работе», «на ревью»)? Рекомендация: в `.lado/sdlc.md` (раздел **Tracker**), передавать их скилу `tracker` как есть; из `sdlc-settings:8` убрать «statuses» (F5.3).
3. Ключи не вида `ABC-123` и эпики (F5.5) выходят за рамки простой замены слов, но без них кит с GitHub Issues спотыкается на именах. Рекомендация: включить в 0.2.0 одним правилом в `openspec-changes` и одной строкой у супервайзера; эпик — «когда у трекера они есть».
4. F5.6 (ревьюер и переписанные сборкой файлы), F6.1, F7.1, F11.1 и low-находки не про трекер. Рекомендация: в этом ране брать только находки про трекер (F5.1–F5.5, F9.2, F10.1, F10.2), остальное оставить в бэклоге, чтобы не повторились лишние круги 0.1.2.
