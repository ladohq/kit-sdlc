# Отчёт о ките: sdlc 0.2.0

- Дата: 2026-10-09
- Кит: worktree рана `improve/sdlc-2` (`.lado/worktrees/kit-sdlc/improve-sdlc-2`), путь дан задачей
- Коммит: b6fe2b7 (шаг `assess` рана `improve/sdlc-2`)
- Оценил: критик kit-builder (слои a и b)
- Режим: полная оценка. Прежний отчёт этого файла (визит 3 рана `improve/sdlc`, коммит 5c3774c) был повторной оценкой, без таблицы «Известные дыры» по всему киту. К тому же текст кита после его коммита 26f7666 изменился: `git diff 26f7666 b6fe2b7 -- kit.yaml README.md BLUEPRINT.md agents flows skills` меняет `README.md:24` («1.0.2 or newer»). Поэтому этот отчёт заменяет прежний; прежний остаётся в git (5c3774c).
- Проходы: три независимых суб-агента, каждый с рубрикой, папкой кита, зависимыми скилами (superpowers v6.4.1, mattpocock-skills v1.2.3) и ожидаемым скилом `tracker` из tracker-jira-server **v1.1.0** (склонирован по тегу во временную папку: в кэше LADO есть только v1.0.0). Начало: проход 1 — kit.yaml и роли, проход 2 — флоу по состояниям, проход 3 — скилы. `kit-reports/` и `BACKLOG.md` суб-агенты не читали. Одиночных находок 4, все оставлены как подтверждённые (F3.2, F3.3, F5.2, F6.3); отброшено 0. Цитаты и номера строк сверены критиком с файлами.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 11 находок (0 high, 5 medium, 6 low); 7 из 12 критериев без находок |
| Охват | полная оценка всего кита: 14 файлов (kit.yaml, README.md, BLUEPRINT.md, 5 ролей, 1 флоу, 4 скила с 3 файлами при них) |

Счёт относится только к тому, что указано в строке «Охват». С повторной оценкой его сравнивать нельзя.

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
same as the plan in BLUEPRINT.md
```
(код выхода 0)

## Исправить первым

1. Правило чтения после записи в трекер: проверять запись по тому, что `tracker` возвращает (комментарий по его id, статус по смыслу, метку в списке), никогда не сравнивать текст; запись, которую чтение задачи не показывает (ссылка), подтверждается ответом `tracker` (F3.1).
2. README: «tracker-jira-server 1.1.0 or newer» и убрать «(assign, label)» как пример того, чего `tracker` не умеет (F10.1).
3. `merge` читает и `ledger` (F2.1).
4. Что делать, когда проверки после `openspec archive` красные (F3.2), и когда в `merge` архив по-прежнему нельзя сделать (F3.3).

## Находки

### 1. Границы ролей

Без находок. Права каждой роли названы: «You are read-only on the code: you read, run checks and comment on the pull request, and» (`agents/reviewer.md:15`); аналитик «You write documents and mock-ups; code is the developer's» (`agents/analyst.md:13-14`); супервайзер пишет в трекер только по просьбе человека (`agents/supervisor.md:11`).

### 2. Передача между шагами

- **F2.1** [medium] `flows/task.yaml:113` (state `merge`)
  > `    reads: [design, merge-report]`
  На пути, где `archive` ничего не заархивировал, а человек принял находки на `merge_ok`, `merge` архивирует сам («archive first ("Archive")», `:118`), а архив пишется «with the closing report from the ledger» (`agents/integrator.md:24`). `ledger` в `reads` шага нет, и к заметке гейта его не прикладывают: интегратор пишет итоговый отчёт без источника или берёт артефакт, которого шаг не называет.
  Fix: `reads: [design, ledger, merge-report]`. Passes: 2/3 (уровни low и medium; взят medium)

### 3. Готовность и исходы

- **F3.1** [medium] `skills/sdlc-settings/SKILL.md:56`
  > `task, and read the task back after each write: a reported success is not proof that the`
  Правило не говорит, что проверять. С tracker-jira-server 1.1.0 чтение задачи показывает комментарий в wiki-разметке с меткой `[LADO: …]` спереди («`get` shows the text as Jira keeps it, in wiki markup», tracker SKILL.md:86-87), статус под именем доски («Code Review»), а ссылок не показывает вовсе. Агент, сравнивающий текст, решит, что комментарий о мерже не дошёл, и запостит его снова (команда увидит дубль); ссылку он подтвердить не может и то шлёт её ещё и комментарием (`:67`), то идёт к супервайзеру. Это F3.3 отчёта визита 2 (в `BACKLOG.md`), пункт (2) задачи.
  Fix (без названий команд трекера): «Check each write by what `tracker` gives back for it: a comment by the id it reports, among the task's comments; a status, assignee or label as the task now shows it, by meaning. Never compare the text you sent with the text the tracker keeps: it formats it and adds its mark. A write the task does not show (a link) is confirmed by `tracker`'s own result; say so in your note.» Passes: 3/3 (критерии 5, 5 и 3; взят 3 — правило о том, когда запись считается сделанной)

- **F3.2** [medium] `skills/openspec-changes/SKILL.md:75`
  > `3. `openspec validate --specs --strict` passes, and the grep for contradictions (above)`
  Проверка идёт после шага 2, когда `openspec archive` уже перенёс файлы в worktree без коммита. Ни скил, ни `do` шага `archive` («archive nothing and say so at the top of `merge-report`», `flows/task.yaml:99`) не говорят, что делать, если она красная: интегратор оставит наполовину заархивированный грязный worktree, а разработчик после `merge_ok` → `build` не найдёт коммита архива для `git revert` («After the archive», `:85-87`).
  Fix: в шаге 3: «When either fails, restore the files the archive changed (they are not committed yet), commit nothing, and say so at the top of `merge-report`.» Passes: 1/3, confirmed (шаги 2–5 `:72-81` без ветки на провал; «After the archive» исходит из коммита архива)

- **F3.3** [medium] `flows/task.yaml:117` (state `merge`)
  > `      When `merge-report` says nothing was archived and the human accepted the findings at`
  `archive` не архивирует по трём причинам: «the ledger does not show the review converged, a task not checked without a reason, a delta that contradicts a living spec» (`:97-99`), и про все три говорит «the human sends it back, or accepts the open findings there». `merge` знает только путь «accepted the findings» и тогда архивирует сам; при противоречащей дельте шаг 3 архива снова красный, а у `merge` исходы только `merged` и `conflicts`. Интегратор архивирует поверх противоречия или застревает.
  Fix: в `archive`: «only an unconverged review can be accepted at `merge_ok`; for the other two the human rejects it». Или в `merge`: «when the archive still fails, send it to the supervisor and stay in `merge`». Passes: 1/3, confirmed (`outcomes: {merged: done, conflicts: build}`, `:128`; шаг 3 архива, `skills/openspec-changes/SKILL.md:75-76`)

### 4. Независимая проверка

Без находок. Работу проверяет отдельный ревьюер, оба гейта показывают свидетельства: `merge_ok` — `reads: [ledger, merge-report]` (`flows/task.yaml:106`), `design_ok` — `reads: [design]` (`:37`).

### 5. Противоречия

- **F5.1** [low] `agents/integrator.md:63`
  > `   `merge-report` as it is, read it back, and set the waiting-for-release label, or ask`
  Комментарий постится «как есть», а черновик пишется без требования к переносам строк («in the settings' tracker comment language, in Markdown», `:29`). tracker-jira-server 1.1.0 требует «Write each paragraph on one line: Jira shows every line break.» (tracker SKILL.md:85). Черновик с переносами (стиль файлов самого кита) приходит в QA-комментарий с рваными строками, либо интегратор правит текст вопреки «as it is».
  Fix (нейтрально): в `agents/integrator.md:28-29`: «in Markdown, written as the skill `tracker` asks text to be written». Passes: 2/3

- **F5.2** [low] `agents/analyst.md:28`
  > `   the human when it is not (`sdlc-settings`, "Writing to the tracker").`
  «Assign it to the human» не говорит, какой это аккаунт, а tracker-jira-server 1.1.0 требует «use only a login you were given; on exit 11 report it and do not try another login, since a guess may assign the task to someone else» (tracker SKILL.md:63-64). Осторожный аналитик спросит у человека логин; правильная цель — аккаунт, от имени которого работает `tracker` (`me`).
  Fix: «assign it to the human, the account `tracker` works as». Passes: 1/3, confirmed (tracker SKILL.md:22-24: «Whatever you write in Jira appears in the user's name»; проходы 1 и 2 отметили то же в разделе о паре с трекером, но не как находку)

### 6. Дублирование

- **F6.1** [low] `flows/task.yaml:124` (state `merge`)
  > `      note_body. A CI red for a cause in the work is `conflicts` too, but the merge is`
  Строки `:121-127` пересказывают шаги 1–2 раздела «Merge» роли (`agents/integrator.md:42-56`): откат мержа до пуша, оставить запушенный, внешние причины — супервайзеру. Две копии разойдутся.
  Fix: в `do` оставить только условия исходов со ссылкой «(your role, "Merge")». Passes: 2/3

- **F6.2** [low] `agents/developer.md:58`
  > `   "review" status, once. Read the task back after each write.`
  Повторяет `skills/sdlc-settings/SKILL.md:56`, на которое шаг 3 уже ссылается. При правке F3.1 эта копия останется старой. Это F6.4 отчёта визита 2 (в `BACKLOG.md`).
  Fix: убрать вторую фразу. Passes: 2/3

- **F6.3** [low] `flows/task.yaml:79` (state `review`)
  > `      limit is rare. In the visit where correctness converges, run the cleanup pass too;`
  Строки `:79-80` пересказывают критерий остановки `review-loop` («When the cleanup leaves nothing to fix, the round that converged is the final one», `skills/review-loop/SKILL.md:136`).
  Fix: заменить ссылкой «(`review-loop`, "Stop criterion")». Passes: 1/3, confirmed (обе цитаты на месте)

### 7. Когда звать человека

Без находок. Работники идут к человеку через супервайзера: «A question for the human, also where a skill says "ask your human partner", goes to the» (`agents/developer.md:35`); аналитик говорит с человеком сам (`agents/analyst.md:16-18`); несходящийся цикл — «fails, ask the human with the open findings and your recommendation, and do as they say.» (`skills/review-loop/SKILL.md`), плюс `max_visits: 8`.

### 8. Циклы на повторном визите

Без находок. «On a later visit your previous ledger is your `ledger` artifact: mark each of its» (`flows/task.yaml:75`); «Write the artifact `build-report` whole on every visit» (`agents/developer.md:73`); `design` пишется целиком заново (`flows/task.yaml:22-23`); `max_visits: 8` (`:68`).

### 9. Краткость и «почему»

Без находок сверх F6. Правила несут причину: «Follow the routine as written; each step of it exists because it was once» (`agents/integrator.md:13`), «a reported success is not proof that the» (`skills/sdlc-settings/SKILL.md:56`).

### 10. Описания скилов

- **F10.1** [medium] `README.md:24`
  > `For example, with [tracker-jira-server](https://github.com/ladohq/kit-tracker-jira-server) 1.0.2 or newer:`
  README называет 1.0.2, в которой нет `assign`, `label` и id комментариев в чтении задачи (всё это пришло в 1.1.0). Человек, поставивший 1.0.2, получит просьбы назначить задачу и поставить метку руками на каждой задаче, а чтение после записи — без id (F3.1). Там же `README.md:56`: «back to the developer. What the skill `tracker` cannot do (assign, label) the agents» — читается так, будто трекер этого не умеет, хотя пример из README (1.1.0) умеет. Блупринт тоже устарел: «must therefore accept Markdown: `tracker-jira-server` gets this in its next release (in» (`BLUEPRINT.md:94`).
  Fix: «1.1.0 or newer» в `:24`; в `:56` «What the skill `tracker` cannot do (with some trackers: assign, label)…»; в R16 назвать вышедшую 1.1.0. Passes: 3/3 (уровни low, low, medium; взят medium)

- **F10.2** [low] `skills/openspec-changes/SKILL.md:77`
  > `4. Write the review's closing report (`review-loop`, "Closing report") to`
  Архивирует интегратор, но `review-loop` нет в его `skills:`. Пока итоговый отчёт есть в `ledger`, вреда нет; ревьюер пишет его только при сходимости («converged, the closing report», `agents/reviewer.md:49`). На пути, где человек принял несошедшееся ревью и интегратор архивирует в `merge`, отчёта нет, и что в нём писать, интегратор не знает.
  Fix: в роли интегратора сказать, что отчёт берётся из `ledger`, а без него — строки ledger, остаточный риск и согласие человека; или добавить интегратору `review-loop`. Passes: 3/3

### 11. Нейтральность к провайдеру

Без находок. Названы только инструменты LADO; кодовый хостинг — через настройки; трекер — по смыслу: «URL. Name no tracker's commands, settings files or markup yourself, and use them only as» (`skills/sdlc-settings/SKILL.md:52`); шаблон — через `${SKILL_DIR}` (`:20`). Ни `jira.py`, ни `.lado/tracker.yaml`, ни wiki-разметка в ролях и скилах не названы (R16 держится).

### 12. Безопасность и рамки

Без находок. Мерж, удаление удалённой ветки и записи в трекер при мерже идут после гейта `merge_ok`; «no force, ever» (`agents/developer.md:49`); запись в трекер вне рутины — только по «yes, create» (`skills/sdlc-settings/SKILL.md:57-58`); перевод задачи назад при `dropped` — только по «да» человека (`flows/task.yaml:30-33`).

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: «Find its cause before anyone acts on it: read the failure, and run the failing check alone» (`skills/engineering-rules/SKILL.md:40`); интегратор разделяет причину в работе и вне её (`agents/integrator.md:44-47`) |
| 2. Работа вне флоу, мерж без гейта | Закрыта: мерж только после `merge_ok` (`flows/task.yaml:105`); «The human picks the tasks; never start a run for a task they did not name.» (`agents/supervisor.md:38`); свои коммиты супервайзер делает только с «да» человека |
| 3. Путь вне worktree рана | Закрыта: «Work only inside the run's worktree, with absolute paths; leave it only by path, never» (`skills/engineering-rules/SKILL.md:58`); шаблон через `${SKILL_DIR}` |
| 4. Вердикт без порога серьёзности | Закрыта: «the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:131`), уровни определены выше |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: «ask your human partner» TDD и systematic-debugging идёт через супервайзера (`agents/developer.md:35`); у brainstorming, grilling и writing-plans аналитик берёт только метод (`agents/analyst.md:66`); у ревьюера и интегратора зависимых скилов нет, `tracker` у ревьюера нет |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Жёлтая мера (Worker roles = 4) обоснована в блупринте (R12, R4). Устаревшая строка R16 о выпуске tracker-jira-server — в F10.1.

## Пара с tracker-jira-server 1.1.0

Сверено с SKILL.md тега v1.1.0 (по задаче рана):
- **Статусы**: «in progress», «review» (`skills/sdlc-settings/SKILL.md:62`) совпадают со словами `statuses` в примере настроек трекера; «спросить, куда задача может перейти» соответствует переходу без цели. Чтение показывает имя доски — сверять по смыслу (F3.1).
- **Назначение**: есть в 1.1.0; кому — F5.2. Повтор — успех без изменений.
- **Метки**: добавление идемпотентно и печатает список меток; `waiting_for_release` из `sdlc.example.md` — одно слово, как требует трекер.
- **id комментариев**: трекер печатает id нового комментария и показывает id при чтении; sdlc об этом не говорит — F3.1.
- **Ссылки**: чтение задачи их не показывает — F3.1.
- **Markdown**: совпадает с конвертером трекера; правило «абзац в одну строку» — F5.1.

## Связь с BACKLOG.md

- F3.1 = F3.3 визита 2, F6.2 = F6.4 визита 2 (оба в `BACKLOG.md`). F3.1 — в рамках задачи (пункт 2).
- F7.2, F9.5, F10.3 визита 2 (в `BACKLOG.md`) проходы не нашли заново; их строки с коммита 26f7666 не менялись (`git diff 26f7666 b6fe2b7` трогает только `README.md:24`). Остаются в бэклоге.

## Вопросы человеку

1. Входят ли в этот ран находки вне пунктов (1)–(2) задачи: F2.1, F3.2, F3.3 (medium, путь архивации и мержа) и low F5.1, F5.2, F6.1, F6.3, F10.2? Рекомендация: взять F2.1, F3.2, F3.3 (по строке-двум каждая, все про путь, где человек принял несошедшееся ревью или архив не прошёл) и F5.1, F5.2 (пара с трекером, как и задача); F6.1, F6.3, F10.2 — в `BACKLOG.md`.
2. Править ли R16 в `BLUEPRINT.md:94` (tracker-jira-server «gets this in its next release») заодно с README (F10.1)? Рекомендация: да, одна строка.

## Найдено по пути

- `lado kits show tracker-jira-server` показывает установленную v1.0.0 (`~/.lado/cache/kit-tracker-jira-server-c0cc1184/v1.0.0`), хотя вышла v1.1.0; для сверки критик склонировал v1.1.0 во временную папку. Это не проблема LADO и не находка: перед проверкой пары в живой сессии обновить кит трекера.
