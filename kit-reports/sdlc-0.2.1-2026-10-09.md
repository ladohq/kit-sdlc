# Отчёт о ките: sdlc 0.2.1

- Дата: 2026-10-09
- Кит: worktree рана `improve/sdlc-2` (`.lado/worktrees/kit-sdlc/improve-sdlc-2`), путь дан задачей
- Коммит: bf57458 (визит 1 шага `evaluate`)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.2.0-2026-10-09.md` (артефакт `assessment`, коммит 29a4c8f) и плана (`plan`). База — коммит, который оценивал этот отчёт, b6fe2b7. Изменение: `git diff b6fe2b7 bf57458 -- kit.yaml README.md BLUEPRINT.md agents flows skills` — 7 файлов, 31 строка добавлена, 16 убрано (коммиты c3809bf, блупринт, и bf57458, кит).
- Проходы: изменение короткое (6 мест в 5 файлах кита и R16, раздел 4, журнал блупринта), поэтому три прохода по изменённому тексту и местам, которые на него ссылаются (`flows/task.yaml` state `merge`, `agents/developer.md` «Fixing», tracker SKILL.md v1.1.0), критик сделал сам; проверку вырезанных правил — по `git diff --word-diff`. Полный проход по 7 файлам целиком дал суб-агент; его 4 находки критик проверил по файлам и `jira.py` трекера v1.1.0: 3 оставлены в изменённом тексте, 1 перенесена в «Пропущено ранее» (её слова диф не трогал), уровень двух понижен (почему — в находках). Одиночных находок проходов по изменению нет, отброшено 0.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 3 находки в изменённом тексте (0 high, 0 medium, 3 low), потерянных правил нет; 10 из 12 критериев без находок |
| Охват | повторная оценка изменённого текста, с полным проходом по 7 файлам, которые трогает диф |
| Правило остановки | выполнено: 0 high, 0 medium в изменённом тексте |

Счёт относится только к тому, что указано в строке «Охват». С полной оценкой (`assessment`) его сравнивать нельзя.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
expects skills: tracker
```

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.2.1

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 4 | 3 / 5 | yellow |
| Work steps in a flow | flows/task.yaml | 5 | 5 / 8 | green |
| Gates in a flow | flows/task.yaml | 2 | 2 / 3 | green |
| Words in a role prompt | agents/analyst.md | 800 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 606 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 650 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 419 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 753 | 1000 / 1500 | green |
| Own skills | kit | 4 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Флоу

![task](sdlc-0.2.1-2026-10-09/task.svg)

```
same as the plan in BLUEPRINT.md
```
(код выхода 0)

## Исправить первым

Блокирующего нет. Если делать ещё круг — F3.4 (одна фраза), остальное по желанию.

## Находки

### 1. Границы ролей

Изменение прав не трогает. Без находок.

### 2. Передача между шагами

Изменение не трогает ни `reads`, ни `produces`. Без находок.

### 3. Готовность и исходы

- **F3.4** [low] `skills/sdlc-settings/SKILL.md:57`
  > `tracker took it. Check each write by what `tracker` gives back for it: a comment by the id`
  Проверка комментария опирается на id, который сообщает `tracker`, а README не требует его от трекер-кита: «sdlc asks the skill, by meaning, to read a task with its comments, list and change its» (`README.md:19-20`, без id). С трекером, который id не даёт, агент не может подтвердить комментарий (сравнивать текст запрещено) и то считает его неподтверждённым, то постит снова. С tracker-jira-server 1.1.0 из README этого не бывает: «print the new comment's id; `get` shows the same id on each comment» (tracker SKILL.md:60-61). Поэтому low, а не medium, как предложил полный проход.
  Fix: назвать id среди того, что трекер-кит даёт (`README.md:19-21`: «…post a comment and report its id…»), или в правиле: «when `tracker` reports no id, find your comment by `tracker`'s mark, as the newest in your name, and say so in your note». Passes: full pass, confirmed

- **F3.5** [low] `skills/sdlc-settings/SKILL.md:58`
  > `it reports, among the task's comments; a status, assignee or label as the task now shows`
  Исполнителя «as the task now shows it» не с чем сравнить: чтение задачи показывает отображаемое имя, а назначение печатает логин (`jira.py`: `print("Assignee: %s   Reporter: %s" % (` с `_name(...)`, `:546-547`; `print("%s: assignee %s -> %s" % (args.key, _user(current), _user(login)))`, `:794`). Агенты будут засчитывать по-разному: любой исполнитель, смена исполнителя или «не проверить».
  Fix: «an assignee or label as `tracker` reports it after the write, or its "already so"». Passes: full pass, confirmed

### 4. Независимая проверка

Изменение не трогает ревью и гейты. Без находок.

### 5. Противоречия

Без находок. «a reported success is not proof that the» (`:56`) и «A write the task does not show (a link) is confirmed by» (`:60`) не противоречат: второе — названное исключение. Комментарий интегратора постится «as it is» (`agents/integrator.md:64`), а черновик теперь пишется «written as the skill `tracker` asks» (`:29`) — сходится. Цель назначения «(`tracker`'s account)» (`agents/analyst.md:28`) совпадает с `me` трекера (tracker SKILL.md:40).

### 6. Дублирование

- **F6.4** [low] `agents/integrator.md:29`
  > `   settings' tracker comment language, in Markdown, written as the skill `tracker` asks`
  Правило «текст для трекера пишется так, как просит `tracker`» (R16, `BLUEPRINT.md:99-100`) есть только у интегратора, а общее правило для комментариев о нём молчит: «skill formats them for its tracker.» (`skills/sdlc-settings/SKILL.md:70`). Другой комментарий (ссылка разработчика, когда трекер не умеет ссылки; комментарий по просьбе человека) правила не получит. Сейчас вред мал: такие комментарии — одна строка.
  Fix: перенести оговорку в пункт **Comments** `sdlc-settings` («written in Markdown as the skill `tracker` asks, and handed to it as they are…»), у интегратора оставить ссылку. Passes: full pass, confirmed

### 7. Когда звать человека

Изменение не трогает. Без находок.

### 8. Циклы на повторном визите

Не применимо к изменению: циклов оно не трогает.

### 9. Краткость и «почему»

Без находок. Новое правило несёт причину: «formats it and adds its mark» (`skills/sdlc-settings/SKILL.md:60`). Сокращение формулировки плана у аналитика удержало промпт в зелёной зоне (800 слов) без потери смысла.

### 10. Описания скилов

Без находок. README называет «1.1.0 or newer» (`README.md:24`) и «(with some trackers: assign, label)» (`:56`); R16 — вышедший трекер (`BLUEPRINT.md:94-96`).

### 11. Нейтральность к провайдеру

Без находок. Новые строки называют действия по смыслу («a comment by the id it reports», «`tracker`'s account»), без команд, флагов и файлов tracker-jira-server; «for Jira, a paragraph per line» есть только в блупринте, не в ролях и скилах.

### 12. Безопасность и рамки

Без находок. Изменение не добавляет записей вне рутины; «yes, create» на месте (`skills/sdlc-settings/SKILL.md:61-62`, «human's explicit "yes, create" for that very action» — `:62`).

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Изменение не трогает; закрыта, как в `assessment` (`skills/engineering-rules/SKILL.md:40`) |
| 2. Работа вне флоу, мерж без гейта | Изменение не трогает; мерж после `merge_ok` |
| 3. Путь вне worktree рана | Изменение не добавляет путей |
| 4. Вердикт без порога серьёзности | Изменение не трогает (`skills/review-loop/SKILL.md:131`) |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Изменение не меняет `skills:` ролей; `tracker` по-прежнему нет у ревьюера |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Жёлтая мера (Worker roles = 4) обоснована. Раздел 4 блупринта совпадает с выводом скрипта (analyst 800, developer 606, integrator 650). Журнал (раздел 5) имеет три строки 0.2.1 с находками F10.1, F3.1, F6.2, F5.1, F5.2.

## Прежние находки

Отчёт `assessment` (29a4c8f), 11 находок. Список автора в `build-report` сверен с файлами.

- **F10.1** RESOLVED: «For example, with [tracker-jira-server](https://github.com/ladohq/kit-tracker-jira-server) 1.1.0 or newer:» (`README.md:24`); «back to the developer. What the skill `tracker` cannot do (with some trackers: assign, label) the agents» (`:56`); R16: «must therefore accept Markdown: `tracker-jira-server` does from 1.0.1; the README names» (`BLUEPRINT.md:94`, верно: Markdown пришёл в tracker-jira-server 1.0.1 по его журналу).
- **F3.1** RESOLVED: «tracker took it. Check each write by what `tracker` gives back for it: a comment by the id» (`skills/sdlc-settings/SKILL.md:57`), далее «Never compare the text you sent with the text the tracker keeps» и подтверждение ссылки ответом `tracker`. Остатки — F3.4, F3.5.
- **F6.2** RESOLVED: `agents/developer.md:58` теперь «"review" status, once.»
- **F5.1** RESOLVED: «settings' tracker comment language, in Markdown, written as the skill `tracker` asks» (`agents/integrator.md:29`). `sdlc.example.md` уже по абзацу в строку — автор не менял, верно.
- **F5.2** RESOLVED: «the human (`tracker`'s account) when it is not (`sdlc-settings`, "Writing to the» (`agents/analyst.md:28`).
- **F2.1, F3.2, F3.3, F10.2** STILL OPEN, оставлены планом (отдельный ран по пути архивации): диф не трогает `flows/` и `skills/openspec-changes/`.
- **F6.1, F6.3** STILL OPEN, оставлены планом (в `BACKLOG.md`): `flows/task.yaml` не менялся.

## Вырезанные правила

| Убранное правило (файл:строка в b6fe2b7) | Где оно теперь |
|---|---|
| «Read the task back after each write.» (`agents/developer.md:58`) | `skills/sdlc-settings/SKILL.md:56` «read the task back after each write»; разработчик идёт туда по ссылке шага 3 (`agents/developer.md:55-56`) и по описанию скила («before … any tracker write») |
| «(assign, label)» (`README.md:56`) | `README.md:56`, с оговоркой «with some trackers» |
| «`tracker-jira-server` gets this in its next release (in work, 2026-10-09), and `sdlc` 0.2.0 is published after it» (`BLUEPRINT.md:94-95`) | порядок публикации исполнен (трекер 1.1.0 вышел); `BLUEPRINT.md:94-96` называет вышедшие версии |
| «in Markdown.» и перенос строк (`agents/integrator.md:29-31`) | `agents/integrator.md:29-32`, тот же текст с добавкой; по `--word-diff` больше ничего не убрано |
| значения раздела 4 (`BLUEPRINT.md`, analyst 798, developer 613, integrator 640) | заменены выводом скрипта на 0.2.1 |

Ни одно правило не потеряно.

## Пропущено ранее

- **F3.6** [low] `agents/analyst.md:27`
  > `4. Move it to the "in progress" status, unless it is there already, and assign it to`
  «…assign it to the human … when it is not» читается двояко: «когда у задачи нет исполнителя» или «когда она не на аккаунте `tracker`». Во втором прочтении аналитик молча забирает задачу у коллеги, в первом — оставляет её коллеге. Слова «when it is not» были и в 0.2.0; диф добавил только «(`tracker`'s account)».
  Fix: «assign it to the account `tracker` works as; when someone else has it, ask the human first». Passes: full pass, confirmed

## Оставлено планом

- F2.1, F3.2, F3.3, F10.2 — человек: «a separate run right after this one, for the archive path as a whole» (план, «Answers»); записано в `BACKLOG.md` на main (6923c79).
- F6.1, F6.3 — low, в `BACKLOG.md` (план, «Leave (low, to BACKLOG.md)»).

## Вопросы человеку

1. Делать ли ещё круг ради трёх low (F3.4, F3.5, F6.4) и F3.6? Рекомендация: нет, выпускать 0.2.1 и записать их в `BACKLOG.md`. С tracker-jira-server 1.1.0 F3.4 не возникает, а F3.5 и F3.6 по фразе каждая; их можно взять в следующий ран (путь архивации), он тоже правит роли.
