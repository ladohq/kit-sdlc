# Отчёт о ките: sdlc 0.2.0

- Дата: 2026-10-09
- Кит: worktree рана `improve/sdlc` (`.lado/worktrees/kit-sdlc/improve-sdlc`), путь дан задачей
- Коммит: 26f7666 (визит 3 шага `evaluate`). Визит 1 оценивал d77b2d6 (отчёт — коммит 61892b4), визит 2 — d8150dc (отчёт — коммит c193b00).
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.1.3-2026-10-09.md` (артефакт `assessment`) и отчёта визита 1. База — коммит из плана, 4b7d998. Правки после визита 2: `git diff d8150dc 26f7666`, один коммит 26f7666 (6 строк в `skills/sdlc-settings/SKILL.md`, раздел «The task key as a name»).
- Проходы, визит 3: дельта — один абзац, поэтому три прохода по нему и по местам, которые на него ссылаются (`sdlc.example.md:19-22`, `openspec-changes:37-38`, `supervisor.md:32-33`), критик сделал сам, без суб-агентов. Затем проверил вырезанные правила по `git diff --word-diff d8150dc 26f7666` и сделал полный проход по всему `skills/sdlc-settings/SKILL.md`, единственному файлу дельты. Новых находок нет, одиночных отброшенных нет.
- Визит 2: пять суб-агентов (три прохода, проверка вырезанных правил, полный проход по 11 файлам); 3 одиночные отброшены, 2 из полного прохода оставлены (F3.3, F6.4).

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 5 находок в изменённом тексте (0 high, 0 medium, 5 low), потерянных правил нет; 8 из 12 критериев без находок |
| Охват | повторная оценка изменённого текста (визит 3: `skills/sdlc-settings/SKILL.md`, с полным проходом по файлу; визит 2: 11 файлов) |
| Правило остановки | выполнено: 0 high, 0 medium в изменённом тексте |

Счёт относится только к тому, что указано в строке «Охват». С полной оценкой его сравнивать нельзя.

**Условие релиза (решение человека, R16):** README требует, чтобы скил `tracker` принимал Markdown («The skill must accept comments in Markdown and format them for its tracker. sdlc reads», `README.md:21`). tracker-jira-server v1.0.0 этого пока не умеет: «## Writing in Jira: wiki markup, not Markdown» (tracker SKILL.md:65). Блупринт записывает порядок: «work, 2026-10-09), and `sdlc` 0.2.0 is published after it.» (`BLUEPRINT.md:95`). Публиковать 0.2.0 только после релиза tracker-jira-server с Markdown. До него у пары sdlc 0.2.0 + tracker-jira-server v1.0.0 комментарий при мерже уйдёт в Jira сырым Markdown.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
expects skills: tracker
```

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.2.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 5 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 798 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 613 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 640 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 419 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 753 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.2.0-2026-10-09/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

Нет блокирующих и medium-находок. Если делать ещё круг — F10.3 и F7.2 (по строке каждая).

## Находки

### 1. Границы ролей

Права не изменились: «and keep the human informed. You change no code and write to the tracker only when the» (`agents/supervisor.md:11`). Скила `tracker` у ревьюера нет.

### 2. Передача между шагами

`merge-report` снова говорит, кто ставит метку: «label, set through `tracker` or asked of the human when it cannot, say which; epic» (`agents/integrator.md:36`). Гейт `merge_ok` его читает.

### 3. Готовность и исходы

- **F3.3** [low] `skills/sdlc-settings/SKILL.md:56`
  > `task, and read the task back after each write: a reported success is not proof that the`
  Не сказано, с чем сверять прочитанное. Комментарий теперь уходит в Markdown, скил трекера его форматирует, а tracker-jira-server к тому же ставит перед каждым комментарием метку «`[LADO: <your agent name>]`; leave that mark to it.» (tracker SKILL.md:23). Прочитанный текст никогда не равен отправленному. Один интегратор примет комментарий, другой решит, что запись не прошла, и опубликует второй. Метка была и в 0.1.3, а форматирование увеличивает разницу.
  Fix: «read the task back: the write is there (the tracker may format a comment differently)». Passes: full pass, confirmed (tracker SKILL.md:21-23)

### 4. Независимая проверка

Гейты и их `reads` не менялись. Записи в трекер при мерже идут только после `merge_ok`.

### 5. Противоречия

Правило имени из ключа теперь задано по шагам, и все три примера из него следуют: «`<key>`. Convert the key yourself, in this order: drop everything up to the last `/`,» (`skills/sdlc-settings/SKILL.md:74`). С ним согласны шаблоны веток (`skills/sdlc-settings/sdlc.example.md:20-21`), `openspec-changes:37-38` и `agents/supervisor.md:33`. F5.11 — RESOLVED, см. «Прежние находки».

### 6. Дублирование

- **F6.4** [low] `agents/developer.md:58`
  > `   "review" status, once. Read the task back after each write.`
  Повторяет `skills/sdlc-settings/SKILL.md:56`, на которое строка 55 уже ссылается. Строку задел diff (убрано «settings'»), но сама фраза старая.
  Fix: убрать вторую фразу. Passes: full pass, confirmed

### 7. Когда звать человека

- **F7.2** [low] `agents/supervisor.md:24`
  > `there and tells you when it is missing, with the target. Then ask the human to put it on`
  Теперь аналитик шлёт супервайзеру ещё и ошибку `tracker` на целевой ветке («origin/<target>`. Check that `.lado/sdlc.md` is on it and that `tracker` reads the», `agents/analyst.md:32`). Абзац супервайзера разбирает только пропавший файл. Ошибка трекера попадёт под общее «A failure outside the kit», но супервайзер может попросить человека запушить `.lado/sdlc.md`, хотя дело в другом.
  Fix: «…tells you when it is missing or when `tracker` fails there, with the target; then ask the human to put the missing file on that branch, or show them the error». Passes: 2/3

### 8. Циклы на повторном визите

Дельта не трогает текст повторных визитов. Повтор проверки на ветке ограничен: «once it answers, fetch, reset and check again» (`agents/analyst.md:34`).

### 9. Краткость и «зачем»

- **F9.5** [low] `BLUEPRINT.md:190` (row `task.design`)
  > `| `task.design` | work step | R3, R5, R7, R11, R16 | Takes the task ("in progress"; assigns it through `tracker`, or asks the human to), checks it is still relevant (`dropped` otherwise), proposes the scale, writes the openspec change, and for full the design doc and plan; names the target branch (base or epic) and checks `.lado/sdlc.md` is on it. Reads `build-report` on a return. Produces `design`. |`
  Строка блупринта не упоминает новую проверку `tracker` на целевой ветке. В журнале нет строки о правках визитов 2–3: проверка трекера, `#123` → `task-123` с правилом в `sdlc-settings`, требование Markdown в README. Журнал читается так, будто проверки трекера не осталось («no `.lado/tracker.yaml` check», `:262`).
  Fix: дописать в строку «and that `tracker` reads the task there»; добавить строку журнала 0.2.0 с находками отчёта 0.2.0 (F3.2, F4.2, F5.7, F5.8, F6.3, F9.3, F5.10). Passes: 2/3

### 10. Описания скилов

- **F10.3** [low] `skills/sdlc-settings/SKILL.md:7`
  > `  how an agent writes to the tracker through the skill `tracker`. Use at the start of`
  В описании нет нового раздела «The task key as a name», хотя на него ссылаются супервайзер, `openspec-changes` и `sdlc.example.md`.
  Fix: добавить «and how a task key becomes a run, change or branch name». Passes: 3/3 (а также полный проход)

### 11. Нейтральность к провайдеру

`grep -rni jira kit.yaml agents flows skills` ничего не находит. В README Jira есть только в примере tracker-jira-server. Префикса `gh-` больше нет: «only (`ABC-123` → `abc-123`, `#123` → `task-123`, `owner/repo#123` → `repo-123`).» (`skills/sdlc-settings/SKILL.md:75`).

### 12. Безопасность и рамки

Новая задача и любая запись, которую рутина не называет, требуют явного «yes, create» (`skills/sdlc-settings/SKILL.md:58`). Мерж и записи в трекер после него — только после `merge_ok`. Условие публикации — выше, в карточке.

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: ошибка `tracker` на целевой ветке идёт супервайзеру («task from there; when either fails, send the supervisor the missing file or the error», `agents/analyst.md:33`), у супервайзера есть «A failure outside the kit». Разбор этого случая у супервайзера — F7.2 |
| 2. Работа вне флоу, мерж без гейта | Закрыта: мерж только после `merge_ok`; супервайзер пишет в трекер только по просьбе человека |
| 3. Путь вне worktree рана | Закрыта: проверки аналитика идут в worktree рана после `git reset --keep origin/<target>` (`agents/analyst.md:31-33`) |
| 4. Вердикт без порога серьёзности | Изменение не трогает: `skills/review-loop/SKILL.md:131` |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: `tracker` нет у ревьюера; «ask your human partner» разработчика идёт через супервайзера (`agents/developer.md:35`). Расхождение разметки с tracker-jira-server v1.0.0 — решение человека, см. условие релиза |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Жёлтая мера (Worker roles = 4) обоснована. Раздел 4 совпадает с выводом скрипта (analyst 798, integrator 640, supervisor 753). Расхождение строки `task.design` с ролью — F9.5.

## Прежние находки

Отчёт визита 2 (c193b00): 6 находок.
- **F5.11** RESOLVED: «lowercase it, turn every other character that is not a letter or digit into `-`, collapse» (`skills/sdlc-settings/SKILL.md:75`); `ABC-123` → `abc-123`, `#123` → `-123` → `123` → `task-123`, `owner/repo#123` → `repo#123` → `repo-123`. Ветки: «Runs and openspec changes start with the task key as a name, and branches carry it as» (`:73`).
- **F3.3, F6.4, F7.2, F9.5, F10.3** STILL OPEN: по решению человека не трогались; `git diff d8150dc 26f7666` их строк не меняет (F10.3: описание, `:3-8`, то же).

Отчёт визита 1 (61892b4), 9 находок, все RESOLVED. Список автора в `build-report` сверен с файлами.
- **F5.7** RESOLVED решением человека (вариант б): правило Markdown осталось, README требует его от трекер-кита (`README.md:21`), блупринт ставит публикацию после релиза tracker-jira-server (`BLUEPRINT.md:94-95`). См. условие релиза.
- **F5.8** RESOLVED решением человека: пути обновления с 0.1.x нет, «the kit yet (session kit-sdlc, 2026-10-09).» (`BLUEPRINT.md:105`). Раздел README и предложение супервайзера убраны.
- **F6.3** RESOLVED: правило в одном месте, `skills/sdlc-settings/SKILL.md:71-78`. Супервайзер ссылается: «(`sdlc-settings`, "The task key as a name": `ABC-123` → `abc-123`).» (`agents/supervisor.md:33`).
- **F3.2** RESOLVED: «origin/<target>`. Check that `.lado/sdlc.md` is on it and that `tracker` reads the» (`agents/analyst.md:32`).
- **F4.2** RESOLVED: `agents/integrator.md:36` «say which».
- **F5.9** RESOLVED: «URL. Name no tracker's commands, settings files or markup yourself, and use them only as» (`skills/sdlc-settings/SKILL.md:52`); то же в R16.
- **F5.10** RESOLVED: «sdlc reads no settings file of the tracker kit» (`README.md:21-22`).
- **F9.3** RESOLVED: «status and post a comment, and, when it can, link a URL, assign the task and label it.» (`README.md:20`).
- **F9.4** RESOLVED: «(R11). *Source:* round 5 answer 2; session kit-sdlc (R16).» (`BLUEPRINT.md:66`).

Отчёт 0.1.3: 8 находок про трекер RESOLVED (визит 1). 10 оставленных планом — STILL OPEN, их текст не менялся.

## Вырезанные правила

| Убранное правило (файл:строка на d77b2d6) | Где оно сейчас |
|---|---|
| openspec-changes:37-38 правило имени и примеры | `skills/sdlc-settings/SKILL.md:73-77`; `gh-123` → `task-123` намеренно |
| openspec-changes:39-40 «Run and branch names take the key in the same form» | `skills/sdlc-settings/SKILL.md:73,77-78` «Every role converts the same way, so the run, the change and the branch get the same name» |
| supervisor.md:32-33 форма ключа для рана | ссылка `agents/supervisor.md:33` + `skills/sdlc-settings/SKILL.md:73-78` |
| визит 3, sdlc-settings:73-75 на d8150dc: «lowercase letters, digits and `-`», `task-` перед цифрами, три примера | `skills/sdlc-settings/SKILL.md:74-77`, каждое — шагом правила; примеры те же |
| analyst.md:33-34 «put the branch on the target again and check again» | `agents/analyst.md:34` «fetch, reset and check again» |
| analyst.md:33 «send the supervisor the file's name and the target» | `agents/analyst.md:33` «the missing file or the error and the target» |
| README.md:20 «link a URL» среди обязательных | `README.md:20` под «when it can»; обход — `skills/sdlc-settings/SKILL.md:67` |
| README.md:21 «sdlc reads no tracker settings of its own» | `README.md:21-22` «no settings file of the tracker kit» |
| sdlc-settings:52 «Never use one tracker's commands, settings files or markup» | `skills/sdlc-settings/SKILL.md:52-53` |

Раздел README «Upgrading from sdlc 0.1.x» и предложение супервайзера появились в 735eefe и убраны в d8150dc. На d77b2d6 их не было, решение человека записано в R16. Ничего не потеряно.

## Пропущено раньше

Нет: все находки — в строках, которые изменил diff.

## Оставлено планом

Человек: «we do not take them, we handle them later»; записаны в BACKLOG.md.
- F2.1 `merge` не читает `ledger`.
- F3.1 согласие на противоречащую дельту на `merge_ok`.
- F4.1 макеты на `design_ok`.
- F5.6 ревьюер и файлы, переписанные сборкой.
- F6.1 выбор прохода в трёх местах.
- F6.2 правило cleanup в `do` шага `review`.
- F7.1 `review-loop` спрашивает человека напрямую.
- F8.1 `archive` молчит о повторном визите.
- F9.1 правило minor `fix`.
- F11.1 относительный путь к `references/lenses.md`.

## Вопросы к человеку

1. Публиковать 0.2.0 только после релиза tracker-jira-server с Markdown (так записано в R16)? Рекомендация: да; на `release_ok` проверить, что он вышел, или выпустить локальный тег без публикации.
2. Правило остановки выполнено, остались 5 low (F3.3, F6.4, F7.2, F9.5, F10.3). Рекомендация: выпускать и занести их в BACKLOG.md; ещё один круг стоит делать только ради F10.3 и F7.2.
