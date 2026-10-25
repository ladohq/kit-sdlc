# Отчёт о ките: sdlc 0.7.0

- Дата: 2026-10-25
- Кит: worktree рана `improve/sdlc-7` (корень репозитория), путь дан задачей
- Коммит: 473160f (визит 1 шага `evaluate`)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка.
  - Сравнивается с `kit-reports/sdlc-0.6.0-2026-10-21.md` (артефакт `assessment`), планом (`plan`) и `build-report`.
  - База — коммит из плана, fa281df. Изменение — `git diff fa281df -- kit.yaml README.md BLUEPRINT.md agents flows skills`: 8 файлов (`BLUEPRINT.md`, `README.md`, `agents/analyst.md`, `agents/integrator.md`, `agents/supervisor.md`, `flows/task.yaml`, `kit.yaml`, `skills/sdlc-settings/SKILL.md`), +68 −30 строк. Сюда входит и блупринт из `triage` (50c5943).
- Проходы:
  - Три прохода критик сделал сам, без суб-агентов, каждый со своей точки входа: 1 — `kit.yaml` и роли (интегратор, аналитик, супервизор); 2 — `flows/task.yaml`, состояние `merge` и гейты; 3 — `sdlc-settings` («Talking to the human»), потом снова роли — все места, где роль спрашивает человека или ждёт его ответа (`grep -n -i human` по `agents/` и `skills/`).
  - Проходы шли в одном контексте, поэтому независимыми их считать нельзя. Каждая оставленная находка подтверждена в файлах: цитата найдена `grep -nF`.
  - `recommend` проверен по коду LADO 0.42.0 (`lado/mcp_server.py`, инструмент `recommend` только у супервизора; `lado/runs.py`, `_ask_recommendation` — запрос рекомендации на каждый открытый гейт, в том числе на гейт лимита петли, вид `loop`).
  - Затем проверка вырезанных правил по `git diff --word-diff fa281df` и таблице «Cut text» из `build-report`.
  - Полный проход по файлам дельты (правило 5 «Re-evaluation») не делался: открыта блокирующая находка F5.1. Роли супервизора и интегратора («Merge») и раздел «Talking to the human» прочитаны целиком.
  - Одиночных находок отброшено: 1 (low: при конфликте в changelog «keeping both sides' entries» не говорит, куда ставить запись PR, если база уже выпустила версию, под которой она стояла; вред — только рассуждение, файлы его не показывают). Оставлено как подтверждённые: 0.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 3 находки (1 high, 2 medium, 0 low); 10 из 12 критериев без находок |
| Охват | повторная оценка изменённого текста и его окрестностей (разделы, где роли спрашивают человека); без полного прохода — открыта high |
| Правило остановки | не выполнено: 1 high, 2 medium |

Счёт относится только к тому, что названо в строке «Охват»: счёт повторной оценки и счёт полной оценки сравнивать нельзя.

**Вердикт: `changes`** — открыта F5.1 (high).

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
| Words in a role prompt | agents/analyst.md | 797 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 693 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 711 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 463 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 761 | 1000 / 1500 | green |
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

1. Аналитик: «Ask a round as one message» — задавать раунд вопросов одной карточкой `ask_human` (вопросы с контекстом и рекомендациями в `details`), а не сообщением (F5.1).
2. Супервизор, «A failure outside the kit»: ждать ответа человека по карточке `ask_human`, а не после сообщения (F7.1).
3. Аналитик, «Taking the task» 6: то же для неработающего входа в инструмент код-хоста (F7.2).

## Находки

### 1. Границы ролей

Проверено: кто разрешает конфликт в changelog — только интегратор (`agents/integrator.md:30-34`); кто спрашивает человека напрямую — аналитик и супервизор, остальные через супервизора («A worker that reaches the human through the supervisor still asks the supervisor», `skills/sdlc-settings/SKILL.md:46-48`). `recommend` есть только у супервизора, и роль его так и использует. Находок нет.

### 2. Передачи между шагами

Проверено: при разрешённом конфликте в changelog итог шага остаётся `merged`, и интегратор пишет в заметке, что подтянул и как разрешил («Say in your note what was brought in and how you resolved it», `agents/integrator.md:33-34`) — это доходит до супервизора на `done`. Находок нет.

### 3. Готовность и исходы

Проверено: `conflicts` в `merge.do` сужен до «bringing the target in conflicts in a file other than the changelog» (`flows/task.yaml:105-106`), согласовано с шагом 2 интегратора; условия `merged` и `conflicts` не пересекаются. Находок нет (одиночная low про версию в changelog отброшена, см. «Проходы»).

### 4. Независимая проверка

Проверено: мерж без второго `merge_ok` возможен только когда конфликт целиком в changelog; код PR — тот, что человек одобрил («the pull request's code is what the human approved, so it needs no second `merge_ok`», `agents/integrator.md:32-33`), а **Checks** после мержа базы по-прежнему обязательны (`:35`). Конфликт в любом другом файле возвращается через `conflicts` → build → review → `merge_ok`. Критерий задачи S1 выполнен по тексту. Находок нет.

### 5. Противоречия

- **F5.1** [high] `agents/analyst.md:53-54`
  > why (scope, non-goals, acceptance), then on how. Ask a round as one message: numbered
  > questions, each with its context and your recommended answer; such rounds replace
  Роль велит аналитику задавать раунд вопросов «одним сообщением», а новое правило `sdlc-settings` — «Ask the human a question only with `ask_human` … never as a text message» (`skills/sdlc-settings/SKILL.md:44-45`). Это ровно тот случай LAD-117 (четыре вопроса аналитика текстом, ран ждал незаметно, замечание #521), который S2 должен закрыть. Карточка `ask_human` — одна строка вопроса и кнопки, так что пронумерованный раунд в неё «не влезает», и более конкретное правило роли толкает агента обратно к `send_message`. Ран встаёт, а человек вопроса не видит. Строку изменение не трогает, но противоречие создало именно оно: в 0.6.0 роль разрешала `send_message` для разговора с человеком (`git show fa281df:agents/analyst.md`, строка 17: «(`send_message` to the human, `ask_human`)»), так что это находка изменения, а не «Пропущено ранее». `build-report` утверждает «No text tells a role to ask the human by message» — эта строка пропущена.
  Fix: «Ask a round as one `ask_human` card: the numbered questions, each with its context and your recommended answer, in its `details`; choices only for a round of one question.» Passes: 3/3

### 6. Дублирование

Проверено: правило «вопрос только карточкой» живёт в одном месте (`sdlc-settings`), аналитик и супервизор ссылаются на него коротко (`agents/analyst.md:16-17`, `agents/supervisor.md:43`). Порядок вопроса о дефекте (S3) — только в `sdlc-settings:49-51`. Скрипт бюджета: «Similar paragraphs: none». Находок нет.

### 7. Когда звать человека

- **F7.1** [medium] `agents/supervisor.md:46-48`
  > - **A failure outside the kit** (the tracker unreachable, no `openspec`, a missing setting):
  > show the human the command and its output, wait until they say it is settled, then
  > tell the worker to go on.
  Супервизор ждёт ответа человека («wait until they say it is settled»), но канал не назван, а «show» читается как сообщение. По новому правилу сообщение только информирует, так что супервизор будет стоять на вопросе, которого человек не видит как вопрос — симптом S2. План просил проверить все такие места («Check that no other text of the kit tells a role to ask the human by message»).
  Fix: «…show the human the command and its output by card (`ask_human`, choice "settled"), then tell the worker to go on.» Passes: 2/3

- **F7.2** [medium] `agents/analyst.md:34-35`
  > 6. Check that the code host's tool of **Git** runs logged in (e.g. `gh auth status`);
  > when it does not, tell the human and design only once they settle it.
  То же у аналитика: «tell the human» и ждать, пока человек всё уладит. Ответ ожидается, а канал — сообщение.
  Fix: «…ask the human by card to log it in, and design only once they answer.» Passes: 2/3

Остальное проверено: вопросы разработчика, ревьюера и интегратора идут супервизору через `send_message` (`agents/developer.md:39-40`, `agents/reviewer.md:42-44`, `agents/integrator.md:46-47`), а супервизор задаёт их человеку карточкой (`agents/supervisor.md:43`). «Ask the human» в `review-loop` («Round cap», `:141`) и `openspec-changes:46` канал не называют, так что их покрывает общее правило. S2 выполнен везде, кроме F5.1, F7.1 и F7.2.

### 8. Петли при повторном визите

Проверено: изменение не добавляет ни состояний, ни исходов (`--compare`: «same as the plan in BLUEPRINT.md»). Возврат из `conflicts` по-прежнему проходит через `build`/`review` с `max_visits`. Лимит петли: рекомендация на его карточке через `recommend` (`agents/supervisor.md:52-54`); LADO 0.42 запрашивает её и на гейте вида `loop`. Находок нет.

### 9. Краткость и «почему»

Проверено: новые правила несут причину — «a question in a message does not show in LADO's UI as one, so it waits unseen» (`sdlc-settings:45-46`), «without the cause the human answers with questions first» (`:50-51`), «the pull request's code is what the human approved» (`integrator.md:32`). Аналитик укладывается в 797 слов из 800. Находок нет.

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
| 2. Работа вне флоу, мерж без гейта | Мерж — шаг `merge` после `merge_ok`; разрешение changelog — внутри шага `merge`, мерж без нового гейта касается только PR, код которого одобрен (F4 — без находок) |
| 3. Путь вне worktree рана | Дельта новых путей не вводит |
| 4. Вердикт без порога важности | Дельта не трогает ревьюера и `review-loop` |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Новое правило `sdlc-settings` о вопросах адресовано всем ролям, но для воркеров через супервизора: «A worker that reaches the human through the supervisor still asks the supervisor» (`:46-48`); read-only ревьюер ничего не пишет |
| 6. Команды не совпадают с `expects.commands` | Новых команд нет: `git merge`, `git merge --abort`, `git reset --keep` — `git`; `expects commands: git, openspec` |
| 7. Read-only роли не совпадают с тем, что делают | `read-only roles: reviewer`; дельта ревьюера не трогает; `dependencies.lado: ">=0.42"` ≥ 0.34; README: «so the kit does not run on Codex CLI» |

## Не прослежено

Ничего. Новые правила прослежены к R10 (changelog) и R14 (вопросы карточкой, порядок вопроса о дефекте, `recommend`). Строки раздела 3 для `supervisor`, `analyst`, `task.merge`, `sdlc-settings` обновлены. Жёлтая мера «Worker roles = 4» обоснована (R12, R4). `--compare` различий не печатает.

## Прежние находки

| Находка | Статус | Подтверждение |
|---|---|---|
| Отчёт 0.6.0: открытых находок нет | — | «12 из 12 критериев без находок» |
| F3.1 [low] из 0.5.0: занятое имя change на ветке параллельного рана | STILL OPEN, вне плана (запись в `BACKLOG.md`) | `git diff fa281df -- skills/openspec-changes` пуст; правка `agents/analyst.md` — только строки 16-17 |

## Вырезанные правила

| Удалённое правило (файл:строка в базе) | Где оно теперь |
|---|---|
| `agents/analyst.md:16-18` «(`send_message` to the human, `ask_human`)» | Заменено намеренно: `agents/analyst.md:16-17` «asking by `ask_human`»; «send_message to the human only informs» — `skills/sdlc-settings/SKILL.md:46` |
| `agents/integrator.md:30` «On a conflict, `git merge --abort` and report `conflicts`» | Сужено по плану: `agents/integrator.md:34-35` «When any other file conflicts, `git merge --abort` and report `conflicts`» |
| `agents/integrator.md:31` «When it merges cleanly and brought changes, run the settings' **Checks**» | `agents/integrator.md:35` «When it merged and brought changes, run the settings' **Checks**» (теперь и после разрешения changelog) |
| `agents/supervisor.md:49-50` «A gate opens. Tell the human in one message which run waits, what they approve, which artifacts to read … and your advice with its reason» | Заменено по S4: `agents/supervisor.md:49-51` через `recommend`; «which run waits» показывает сама карточка гейта |
| `agents/supervisor.md:50` «The human answers in LADO.» | Не нужно: гейт отвечается на карточке LADO, это поведение LADO, а не правило агента |
| `agents/supervisor.md:51-53` loop limit: «show the human the ledger's open findings … recommend `continue` … or `cancel`» | `agents/supervisor.md:52-54`, всё через `recommend` |
| `skills/sdlc-settings/SKILL.md:44-45` «(the body of `send_message`, the `details` of `ask_human`, or an attached artifact)» | Сужено намеренно: `:52-53` «(the `details` of `ask_human`, or an artifact attached to it)» |
| `flows/task.yaml:105` «bringing the target in conflicts» | `:105-106` «bringing the target in conflicts in a file other than the changelog» |
| `kit.yaml` `lado: ">=0.34"`, README «LADO 0.34 or newer» | `>=0.42`, README «LADO 0.42 or newer»; причина «the reviewer is read-only…Codex CLI» сохранена |

Потерянных правил нет.

## Пропущено ранее

Нет.

## Оставлено планом

Нет: в плане «Leave: none».

## Вопросы человеку

1. F5.1: как аналитику задавать раунд вопросов при полном масштабе? Рекомендация: одной карточкой `ask_human`, с пронумерованными вопросами (контекст и рекомендуемый ответ к каждому) в `details` и свободным ответом. Кнопки — только когда в раунде один вопрос. Альтернатива — по карточке на вопрос с кнопками: человеку проще отвечать, но вопросы раунда теряют общий контекст, и карточек становится вчетверо больше.
