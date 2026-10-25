# Отчёт о ките: sdlc 0.7.0

- Дата: 2026-10-25
- Кит: worktree рана `improve/sdlc-7` (корень репозитория), путь дан задачей
- Коммит: b41b069 (визит 2 шага `evaluate`; визит 1 — 473160f, его отчёт в git, 9c17bff)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка.
  - Сравнивается с `kit-reports/sdlc-0.6.0-2026-10-21.md` (артефакт `assessment`), планом (`plan`) и `build-report`.
  - База — коммит из плана, fa281df. Изменение — `git diff fa281df -- kit.yaml README.md BLUEPRINT.md agents flows skills`: 8 файлов (`BLUEPRINT.md`, `README.md`, `agents/analyst.md`, `agents/integrator.md`, `agents/supervisor.md`, `flows/task.yaml`, `kit.yaml`, `skills/sdlc-settings/SKILL.md`). Сюда входит и блупринт из `triage` (50c5943).
- Проходы:
  - Визит 1. Три прохода критик сделал сам, без суб-агентов, каждый со своей точки входа: 1 — `kit.yaml` и роли (интегратор, аналитик, супервизор); 2 — `flows/task.yaml`, состояние `merge` и гейты; 3 — `sdlc-settings` («Talking to the human»), потом снова роли — все места, где роль спрашивает человека или ждёт его ответа (`grep -n -i human` по `agents/` и `skills/`). Проходы шли в одном контексте, поэтому независимыми их считать нельзя; каждая находка подтверждена в файлах (`grep -nF`). `recommend` проверен по коду LADO 0.42.0: инструмент `recommend` в `lado/mcp_server.py` есть только у супервизора, а `_ask_recommendation` в `lado/runs.py` запрашивает рекомендацию на каждый открытый гейт, в том числе на гейт лимита петли (вид `loop`). Одиночных отброшено: 1 (low: при конфликте в changelog «keeping both sides' entries» не говорит, куда ставить запись PR, если база уже выпустила версию, под которой она стояла; вред — только рассуждение). Оставлено как подтверждённые: 0.
  - Визит 2. Правки по находкам визита 1: `git diff 9c17bff b41b069 -- kit.yaml README.md BLUEPRINT.md agents flows skills` — `agents/analyst.md`, `agents/supervisor.md`, раздел 4 `BLUEPRINT.md`. Три прохода по правкам и их окрестностям («Taking the task» 6 и «The dialogue» аналитика, «While runs go» супервизора, «Talking to the human» `sdlc-settings`): новых находок нет. Затем проверка вырезанных правил визита 2. Затем — блокирующих находок не осталось — полный проход по всем 8 файлам дельты целиком (правило 5 «Re-evaluation»): F5.2 (medium) и одна находка под «Пропущено ранее». Одиночных отброшено: 0.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | визит 2: 1 находка (0 high, 1 medium, 0 low); F5.1, F7.1, F7.2 визита 1 закрыты; 11 из 12 критериев без находок; 1 находка под «Пропущено ранее» (low) |
| Охват | повторная оценка изменённого текста; визит 2 — с полным проходом по 8 файлам дельты |
| Правило остановки | не выполнено: 0 high, 1 medium (F5.2) |

Счёт относится только к тому, что названо в строке «Охват»: счёт повторной оценки и счёт полной оценки сравнивать нельзя.

**Вердикт: `approved`** — ошибок `lado kits check` нет, красных мер нет, жёлтая обоснована, `--compare` различий не печатает, открытых high нет.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
read-only roles: reviewer
expects skills: tracker
expects commands: git, openspec
```

Expects: `expects skills: tracker`, `expects commands: git, openspec`.

Read-only: `read-only roles: reviewer`; `dependencies.lado: ">=0.42"`.

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.7.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 4 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 800 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 693 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 711 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 463 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 767 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.7.0-2026-10-25/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

1. `flows/task.yaml` `design.do`: «ask, in the message where you propose dropping it» — вопрос о статусе при отказе от задачи задавать на той же карточке `ask_human`, где предлагается отказ (F5.2). Не блокирует.

## Находки

### 1. Границы ролей

Проверено: кто разрешает конфликт в changelog — только интегратор (`agents/integrator.md:30-34`); кто спрашивает человека напрямую — аналитик и супервизор, остальные через супервизора («A worker that reaches the human through the supervisor still asks the supervisor», `skills/sdlc-settings/SKILL.md:46-48`). `recommend` есть только у супервизора, и роль его так и использует. Находок нет.

### 2. Передачи между шагами

Проверено: при разрешённом конфликте в changelog итог шага остаётся `merged`, и интегратор пишет в заметке, что подтянул и как разрешил («Say in your note what was brought in and how you resolved it», `agents/integrator.md:33-34`) — это доходит до супервизора на `done`. Находок нет.

### 3. Готовность и исходы

Проверено: `conflicts` в `merge.do` сужен до «bringing the target in conflicts in a file other than the changelog» (`flows/task.yaml:105-106`), согласовано с шагом 2 интегратора; условия `merged` и `conflicts` не пересекаются. Находок нет.

### 4. Независимая проверка

Проверено: мерж без второго `merge_ok` возможен только когда конфликт целиком в changelog; код PR — тот, что человек одобрил («the pull request's code is what the human approved, so it needs no second `merge_ok`», `agents/integrator.md:32-33`), а **Checks** после мержа базы по-прежнему обязательны (`:35`). Конфликт в любом другом файле возвращается через `conflicts` → build → review → `merge_ok`. Критерий задачи S1 выполнен по тексту. Находок нет.

### 5. Противоречия

- **F5.1** [high] `agents/analyst.md:53-54` — визит 1, **RESOLVED** (см. «Прежние находки»).

- **F5.2** [medium] `flows/task.yaml:30-31` (state `design`)
  > committed. When the task is already in the "in progress" or "review"
  > status, ask, in the message where you propose dropping it, whether to move it back
  `do` шага велит аналитику задать вопрос о статусе «в сообщении, где вы предлагаете отказ», а `sdlc-settings:44-46` теперь требует «Ask the human a question only with `ask_human` … never as a text message». Одни агенты положат оба вопроса в карточку, другие предложат отказ сообщением и будут ждать ответа, которого человек не увидит как вопрос (симптом S2). Строку изменение не трогает, но противоречие создало оно: в 0.6.0 вопрос сообщением был разрешён (`git show fa281df:agents/analyst.md`, строка 17: «(`send_message` to the human, `ask_human`)»), и план требовал проверить все такие места — так что это находка изменения, а не «Пропущено ранее».
  Fix: «ask, on the `ask_human` card where you propose dropping it, whether to move it back…». Passes: full pass, confirmed (`grep -n -i "in the message" agents flows skills` → только `flows/task.yaml:31`)

### 6. Дублирование

Проверено: правило «вопрос только карточкой» живёт в одном месте (`sdlc-settings:44-48`), аналитик и супервизор ссылаются на него коротко или называют канал на месте (`agents/analyst.md:16-17`, `:35`, `:53`; `agents/supervisor.md:43`, `:47`). Порядок вопроса о дефекте (S3) — только в `sdlc-settings:49-51`. Скрипт бюджета: «Similar paragraphs: none». Находок нет.

### 7. Когда звать человека

- **F7.1** [medium] `agents/supervisor.md:46-48` — визит 1, **RESOLVED**.
- **F7.2** [medium] `agents/analyst.md:34-35` — визит 1, **RESOLVED**.

Остальное проверено: вопросы разработчика, ревьюера и интегратора идут супервизору через `send_message` (`agents/developer.md:39-40`, `agents/reviewer.md:42-44`, `agents/integrator.md:46-47`), а супервизор задаёт их человеку карточкой (`agents/supervisor.md:43`). «Ask» без канала в супервизоре («say so and ask», «Ask before you close anything»), в `review-loop` («Round cap») и `openspec-changes:46` покрыто общим правилом `sdlc-settings`. Критерий S2 («the roles say so explicitly») выполнен, кроме F5.2 в `do` шага.

### 8. Петли при повторном визите

Проверено: изменение не добавляет ни состояний, ни исходов (`--compare`: «same as the plan in BLUEPRINT.md»). Возврат из `conflicts` по-прежнему проходит через `build`/`review` с `max_visits: 6`. Лимит петли: рекомендация на его карточке через `recommend` (`agents/supervisor.md:52-54`); LADO 0.42 запрашивает её и на гейте вида `loop`. Находок нет.

### 9. Краткость и «почему»

Проверено: новые правила несут причину — «a question in a message does not show in LADO's UI as one, so it waits unseen» (`sdlc-settings:45-46`), «without the cause the human answers with questions first» (`:50-51`), «the pull request's code is what the human approved» (`integrator.md:32`). Аналитик — 800 слов из 800, на границе зелёного: следующая правка роли, скорее всего, потребует что-то вынести. Находок нет.

### 10. Описания скилов

Проверено: новые скилы и зависимости не добавлялись; `dependencies.lado: ">=0.42"` покрывает `recommend` (проверено на LADO 0.42.0) и `read_only` (≥0.34), `expects` (≥0.30). Находок нет.

### 11. Нейтральность к провайдеру

Проверено: `ask_human`, `send_message`, `recommend` — инструменты LADO. README по-прежнему говорит, что кит не работает на Codex CLI (`README.md:17-18`). Находок нет.

### 12. Безопасность и границы

Проверено: мерж в цель по-прежнему идёт только после `merge_ok`. Разрешение конфликта в changelog — единственное, что интегратор делает сам; оно ограничено «When every conflicting file is the changelog», а при любом другом файле — `git merge --abort` (`agents/integrator.md:30-35`). Новых действий, которые трудно отменить, нет. Находок нет.

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка возвращается без учёта причины в окружении | Дельта сохраняет различение: «A red check whose cause is in the work (`engineering-rules`, "A red check") is `conflicts` too… A red check whose cause is outside the work follows "A red check"» (`agents/integrator.md:35-38`), теперь и после разрешённого конфликта в changelog |
| 2. Работа вне флоу, мерж без гейта | Мерж — шаг `merge` после `merge_ok`; разрешение changelog — внутри шага `merge`, мерж без нового гейта касается только PR, код которого одобрен (критерий 4 — без находок) |
| 3. Путь вне worktree рана | Дельта новых путей не вводит |
| 4. Вердикт без порога важности | Дельта не трогает ревьюера и `review-loop` |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Новое правило `sdlc-settings` о вопросах адресовано всем ролям, но для воркеров через супервизора: «A worker that reaches the human through the supervisor still asks the supervisor» (`:46-48`); read-only ревьюер ничего не пишет |
| 6. Команды не совпадают с `expects.commands` | Новых команд нет: `git merge`, `git merge --abort`, `git reset --keep` — `git`; `expects commands: git, openspec` |
| 7. Read-only роли не совпадают с тем, что делают | `read-only roles: reviewer`; дельта ревьюера не трогает; `dependencies.lado: ">=0.42"` ≥ 0.34; README: «so the kit does not run on Codex CLI» |

## Не прослежено

Ничего. Новые правила прослежены к R10 (changelog) и R14 (вопросы карточкой, порядок вопроса о дефекте, `recommend`). Строки раздела 3 для `supervisor`, `analyst`, `task.merge`, `sdlc-settings` обновлены, раздел 4 пересчитан на визите 2 (analyst 800, supervisor 767 — совпадает с выводом скрипта). Жёлтая мера «Worker roles = 4» обоснована (R12, R4). `--compare` различий не печатает.

## Прежние находки

| Находка | Статус | Подтверждение |
|---|---|---|
| Отчёт 0.6.0: открытых находок нет | — | «12 из 12 критериев без находок» |
| F3.1 [low] из 0.5.0: занятое имя change на ветке параллельного рана | STILL OPEN, вне плана (запись в `BACKLOG.md`) | `git diff fa281df -- skills/openspec-changes` пуст |
| F5.1 [high] визита 1: аналитик задаёт раунд «as one message» | RESOLVED | `agents/analyst.md:53-54`: «Ask a round as one `ask_human` card: numbered questions in `details`, each with context and your recommended answer» |
| F7.1 [medium] визита 1: супервизор ждёт «settled» после сообщения | RESOLVED | `agents/supervisor.md:47-48`: «ask the human by card (`ask_human`, with the choice "settled") with the command and its output; once they answer it is settled, tell the worker to go on» |
| F7.2 [medium] визита 1: аналитик «tell the human» при неработающем входе | RESOLVED | `agents/analyst.md:35`: «when it does not, ask the human by card and design once they settle it» |

Список автора «fixed / not fixed» из `build-report` сверен с файлами: все три правки на месте. Фразу «choices only for a round of one question» автор не добавил, сославшись на «with choices when there are some» в `sdlc-settings:44` и на лимит слов. Этого достаточно: при раунде из нескольких вопросов вариантов нет, и карточка остаётся со свободным ответом.

## Вырезанные правила

| Удалённое правило (файл:строка в базе) | Где оно теперь |
|---|---|
| `agents/analyst.md:16-18` «(`send_message` to the human, `ask_human`)» | Заменено намеренно: `agents/analyst.md:16-17` «asking by `ask_human`»; «`send_message` to the human only informs» — `skills/sdlc-settings/SKILL.md:46` |
| `agents/analyst.md:35` (визит 1) «tell the human and design only once they settle it» | `agents/analyst.md:35` «ask the human by card and design once they settle it»: «only» убрано, условие «once they settle it» осталось |
| `agents/analyst.md:53-54` (визит 1) «Ask a round as one message: numbered questions, each with its context and your recommended answer» | `agents/analyst.md:53-54`, тот же раунд, но карточкой; контекст и рекомендация к каждому вопросу сохранены |
| `agents/analyst.md:64` (визит 1) «attached to your message» | `agents/analyst.md:63-64` «attached to your question» |
| `agents/integrator.md:30` «On a conflict, `git merge --abort` and report `conflicts`» | Сужено по плану: `agents/integrator.md:34-35` «When any other file conflicts, `git merge --abort` and report `conflicts`» |
| `agents/integrator.md:31` «When it merges cleanly and brought changes, run the settings' **Checks**» | `agents/integrator.md:35` «When it merged and brought changes, run the settings' **Checks**» (теперь и после разрешения changelog) |
| `agents/supervisor.md:47` «show the human the command and its output, wait until they say it is settled» | `agents/supervisor.md:47-48`, карточкой с вариантом «settled» |
| `agents/supervisor.md:49-50` «A gate opens. Tell the human in one message which run waits, what they approve, which artifacts to read … and your advice with its reason» | Заменено по S4: `agents/supervisor.md:49-51` через `recommend`; «which run waits» показывает сама карточка гейта |
| `agents/supervisor.md:50` «The human answers in LADO.» | Не нужно: гейт отвечается на карточке LADO, это поведение LADO, а не правило агента |
| `agents/supervisor.md:51-53` loop limit: «show the human the ledger's open findings … recommend `continue` … or `cancel`» | `agents/supervisor.md:52-54`, всё через `recommend` |
| `skills/sdlc-settings/SKILL.md:44-45` «(the body of `send_message`, the `details` of `ask_human`, or an attached artifact)» | Сужено намеренно: `:52-53` «(the `details` of `ask_human`, or an artifact attached to it)» |
| `flows/task.yaml:105` «bringing the target in conflicts» | `:105-106` «bringing the target in conflicts in a file other than the changelog» |
| `kit.yaml` `lado: ">=0.34"`, README «LADO 0.34 or newer» | `>=0.42`, README «LADO 0.42 or newer»; причина «the reviewer is read-only…Codex CLI» сохранена |

Потерянных правил нет.

## Пропущено ранее

- **F5.3** [low] `BLUEPRINT.md:134-135` (R14)
  > - **R14** Everything the human reads is in the language the project's settings name, and
  >   whatever they must read is the last message before a question, not hidden before it.
  Блупринт требует, чтобы материал был «последним сообщением перед вопросом», а кит с 0.6.0 и раньше — «into the same message as the question … never into an earlier message» (`skills/sdlc-settings/SKILL.md:52-54`, в базе — `:44`). Теперь это `details` карточки. Строка R14 существует с 6e94663 (`git blame -L 135,135 BLUEPRINT.md`), её не трогает ни это изменение, ни прошлые. Агентов это не меняет: блупринт они не читают. Но человек, сверяющий кит с блупринтом, видит два разных правила.
  Fix: «…and whatever they must read comes with the question (the `details` of its card or an artifact attached to it), not in an earlier message.» Passes: full pass, confirmed

## Оставлено планом

Нет: в плане «Leave: none».

## Вопросы человеку

Нет. Вопрос визита 1 (как аналитику задавать раунд) автор решил по рекомендации: одна карточка, вопросы пронумерованы в `details`.
