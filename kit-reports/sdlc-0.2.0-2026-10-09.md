# Отчёт о ките: sdlc 0.2.0

- Дата: 2026-10-09
- Кит: worktree рана `improve/sdlc` (`.lado/worktrees/kit-sdlc/improve-sdlc`), путь дан задачей
- Коммит: d77b2d6 (визит 1 шага `evaluate`)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.1.3-2026-10-09.md` (артефакт `assessment`). База — коммит из плана, 4b7d998; изменение — `git diff 4b7d998 -- kit.yaml README.md BLUEPRINT.md agents flows skills` (11 файлов, +190 −156).
- Проходы: работали пять независимых суб-агентов. Каждому дали рубрику, папку кита, diff, зависимые скилы и скил `tracker` из tracker-jira-server v1.0.0.
  - Три прохода по изменённому тексту, каждый со своего места: 1 — роли, 2 — флоу по шагам, 3 — скилы.
  - Четвёртый проверял вырезанные правила по `git diff --word-diff` и таблице автора.
  - Пятый сделал полный проход по всем 11 файлам из diff.
  - Одиночных находок отброшено 1: «два статуса подходят по смыслу» — вред только рассуждением. Оставлено 2 подтверждённых (F3.2, F9.3), ещё 1 — из полного прохода (F5.10).

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4) |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 9 находок в изменённом тексте (1 high, 2 medium, 6 low), включая потерянные правила; 6 из 12 критериев без находок |
| Охват | повторная оценка изменённого текста и полный проход по 11 файлам из diff |
| Правило остановки | не выполнено: 1 high, 2 medium. Это совет для гейта релиза, не блок |

Счёт относится только к тому, что указано в строке «Охват». С полной оценкой его сравнивать нельзя.

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
| Words in a role prompt | agents/analyst.md | 794 | 800 / 1500 | green |
| Words in a role prompt | agents/developer.md | 613 | 800 / 1500 | green |
| Words in a role prompt | agents/integrator.md | 627 | 800 / 1500 | green |
| Words in a role prompt | agents/reviewer.md | 419 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 759 | 1000 / 1500 | green |
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

1. Markdown-комментарий и tracker-jira-server: в «Writing to the tracker» добавить правило на случай, когда скил `tracker` называет свою разметку; в `merge-report` показывать текст ровно таким, каким он будет опубликован. Это решает человек, см. вопрос 1 (F5.7).
2. Путь обновления для существующего `.lado/sdlc.md`: раздел **Jira** → **Tracker**, шаблон — в Markdown (F5.8).
3. Правило имени из ключа держать в одном месте, которое читает и супервайзер (F6.3).

## Находки

### 1. Границы ролей

Права не изменились: «and keep the human informed. You change no code and write to the tracker only when the» (`agents/supervisor.md:11`). Записи в трекер остались у тех же ролей, у ревьюера `tracker` нет.

### 2. Передача между шагами

Флоу, его `reads` и `produces` не менялись. Комментарий трекера по-прежнему идёт через `merge-report` («the drafted comment as it will be posted; the ledger's residual risk and declined», `agents/integrator.md:37`).

### 3. Готовность и исходы

- **F3.2** [low] `agents/analyst.md:32` (state `design`)
  > `   origin/<target>`. Check that `.lado/sdlc.md` is on it; when it is missing, send the`
  Проверку настроек трекера на целевой ветке убрали, как и решил человек. Но скил `tracker` ищет свои настройки «finds it from the current directory up to the repository root» (tracker SKILL.md:94), то есть в worktree рана. Если на ветке эпика их нет, `tracker` упадёт при привязке в `build` или при комментарии уже после мержа, а не в `design`. Потом всё равно дойдёт до супервайзера через таблицу выходов скила, только поздно.
  Fix, без имени трекера: после `reset --keep` перечитать задачу через `tracker` из worktree; при ошибке отправить супервайзеру ошибку и ветку, как для `.lado/sdlc.md`. Passes: 1/3, confirmed (tracker SKILL.md:93-95; `agents/analyst.md:32`)

### 4. Независимая проверка

- **F4.2** [low] `agents/integrator.md:35` (state `archive`, gate `merge_ok`)
  > `will do in git and in the tracker (base branch: the comment and the waiting-for-release`
  `merge-report` теперь обещает метку, но не говорит, кто её поставит. В tracker-jira-server v1.0.0 нет действия «поставить метку существующей задаче», поэтому после мержа интегратор попросит человека. На `merge_ok` человек одобряет не то, что произойдёт; в 0.1.3 здесь стояло «a request to the human to set the waiting-for-release label by hand». Проверка вырезанных правил оценила это как low–medium; взят low.
  Fix: «…the waiting-for-release label (set through `tracker`, or asked of the human when it cannot; say which)». Passes: 3/3 (а также полный проход и проверка вырезанных правил)

### 5. Противоречия

- **F5.7** [high] `skills/sdlc-settings/SKILL.md:65-66`
  > `- **Comments** are written in Markdown and handed to `tracker` as they are; the tracker`
  > `  skill formats them for its tracker.`
  Единственный трекер-кит, который есть сейчас, этого не делает. tracker-jira-server v1.0.0 говорит обратное: «## Writing in Jira: wiki markup, not Markdown» (tracker SKILL.md:65) и «Jira Server shows descriptions and comments as wiki markup and the script sends your» (`:67`). Интегратор читает оба скила и получает два указания, которые нельзя выполнить одновременно:
  - опубликовать «`merge-report` as it is, read it back, and set the waiting-for-release label, or ask» (`agents/integrator.md:63`) — тогда команда увидит в Jira сырые `**…**` и `- `;
  - перевести в wiki-разметку — тогда опубликован не тот текст, который человек одобрил на `merge_ok` («the drafted comment as it will be posted», `:37`).
  Что выберет агент, зависит от рана, а комментарий уходит с машины под именем пользователя. Обойти через **Extra instructions** проект не может: «Writing to the tracker» стоит выше них (`skills/sdlc-settings/SKILL.md:16`). Это же утверждение — в `BLUEPRINT.md:93` («Markdown; the tracker skill formats it for its tracker.») и в README:20. Проходы оценили high, medium и high, полный проход — high; взят high.
  Fix (выбирает человек, вопрос 1). (а) В этом ките: «Comments are drafted in Markdown; when the skill `tracker` names its own markup, write them in it without changing the content, and show that text in `merge-report`». (б) Вне кита: сначала научить tracker-jira-server принимать Markdown, а в README назвать нужную версию. Passes: 3/3 (а также полный проход)

- **F5.8** [medium] `skills/sdlc-settings/SKILL.md:28`
  > `| **Tracker** | the label for "waiting for release", the issue types the routine skips, the merge comment's template | analyst, developer, integrator |`
  Раздел переименован, шаблон переведён в Markdown, но проект, настроенный под 0.1.x (Clens), хранит в `.lado/sdlc.md` раздел `## Jira` со словами статусов и шаблоном в wiki-разметке. Роли наткнутся на «When the file is missing, or lacks a section your step needs, stop and send the» (`:18`). Супервайзер проверяет только, что файла нет, а в README нет ни строки об обновлении. Если человек просто переименует раздел, старый wiki-шаблон столкнётся с «in Markdown» (`agents/integrator.md:29`).
  Fix: строка в README об обновлении с 0.1.x (переименовать **Jira** в **Tracker**, убрать слова статусов, переписать шаблон в Markdown). Супервайзер в «Before the first run» предлагает это сделать, когда видит **Jira**. Passes: 2/3

- **F5.9** [low] `skills/sdlc-settings/SKILL.md:52`
  > `URL. Never use one tracker's commands, settings files or markup: the project may use any`
  Если читать буквально, это запрещает то, что велит скил `tracker` тому же агенту: запускать свой скрипт (tracker SKILL.md:16) и читать свои настройки («Read it when you need the project's words; the user changes it, not you.», `:112`). Имелось в виду «кит не называет их сам». То же в R16 (`BLUEPRINT.md:90-91`).
  Fix: «Name no tracker's commands, settings files or markup yourself; use them only as the skill `tracker` says.» Passes: 3/3 (а также полный проход)

- **F5.10** [low] `README.md:21`
  > `task and label it. sdlc reads no tracker settings of its own.`
  Через десять строк README сам перечисляет настройки трекера в `.lado/sdlc.md`: «tracker's label and skipped issue types, check commands, the external reviewer, review traps and free-form extra» (`:32`).
  Fix: «sdlc reads no settings file of the tracker kit.» Passes: full pass, confirmed

### 6. Дублирование

- **F6.3** [medium] `agents/supervisor.md:33`
  > `form the change's name starts with: lowercase letters, digits and `-` (`ABC-123` →`
  Правило имени из ключа записано дважды, и копии уже расходятся. Полное правило в `skills/openspec-changes/SKILL.md:38` («to lowercase letters, digits and `-` (`ABC-123` → `abc-123`, `#123` → `gh-123`,»), но у супервайзера этого скила нет (только `sdlc-settings` и `tracker`). Для ключа `#123` он назовёт ран `123`, а изменение и ветка станут `gh-123`, хотя правило требует «key in the same form.» (`:40`). Префикс `gh-` — соглашение одного трекера: GitLab-ключ `#123` тоже станет `gh-123`. Автор отметил это отступление в `build-report`. Проходы оценили low, low и low, полный проход — medium; взят medium.
  Fix: правило с нейтральным случаем «ключ из одних цифр» держать в `sdlc-settings` (его читают все роли) и сослаться на него из `openspec-changes` и супервайзера. Passes: 3/3 (а также полный проход)

### 7. Когда звать человека

Маршруты к человеку сохранены. «Ask the human» в `sdlc-settings` (статус, которому ничего не подходит; назначение или метка, которые `tracker` не умеет) идёт у воркеров через записку или супервайзера: «A question for the human, also where a skill says "ask your human partner", goes to the» (`agents/developer.md:35`). Напрямую с человеком говорит только аналитик.

### 8. Циклы на повторном визите

Повторы безопасны: «Move it to the "in progress" status, unless it is there already» (`agents/analyst.md:27`); `max_visits` и петли не менялись.

### 9. Краткость и «зачем»

- **F9.3** [low] `README.md:20`
  > `status, post a comment (written in Markdown), link a URL, and, when it can, assign the`
  «link a URL» стоит среди обязательных действий, а у правила есть обход: «**A link** to a pull request: when `tracker` cannot link a URL, post it as a comment.» (`skills/sdlc-settings/SKILL.md:67`). Автор трекер-кита решит, что ссылка обязательна.
  Fix: перенести «link a URL» в «when it can». Passes: 1/3, confirmed (`skills/sdlc-settings/SKILL.md:67`)

- **F9.4** [low] `BLUEPRINT.md:64-66`
  > `  task, the integrator asks the human, in its note after the merge, to set the label by hand`
  У фразы R10 о ручной метке пропал источник «(round 5 answer 2: a patch of `tracker-jira-server`)». Требование блупринта осталось без источника.
  Fix: дописать «*Source:* round 5 answer 2; session kit-sdlc (R16).» Passes: cut-rule check

### 10. Описания скилов

Описание `sdlc-settings` обновлено и не пересекается с `tracker`: «how an agent writes to the tracker through the skill `tracker`». В README названы tracker-jira-server и команда `lado start`. `expects.skills: [tracker]` при `lado: ">=0.29"`.

### 11. Нейтральность к провайдеру

`grep -rni jira kit.yaml agents flows skills` ничего не находит. Новых инструментов одного CLI нет. Префикс `gh-` разобран в F6.3.

### 12. Безопасность и рамки

Новая задача и любая запись, которую рутина не называет, по-прежнему требуют явного «да»: «human's explicit "yes, create" for that very action; having shown them a draft or asked» (`skills/sdlc-settings/SKILL.md:58`). Мерж и записи в трекер после него стоят за `merge_ok`. Ни одна роль не стала делать больше, чем решил человек.

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: «- **A failure outside the kit** (the tracker unreachable, no `openspec`, a missing setting):» (`agents/supervisor.md:46`); у скила `tracker` своя таблица выходов, и воркер отправляет супервайзеру. Поздняя ошибка настроек трекера — F3.2 |
| 2. Работа вне флоу, мерж без гейта | Закрыта: супервайзер пишет в трекер только по просьбе человека, мерж только после `merge_ok` (`flows/task.yaml:110`) |
| 3. Путь вне worktree рана | Закрыта: `.lado/sdlc.md` проверяется на целевой ветке (`agents/analyst.md:32`). Относительный путь к `references/` оставлен планом (F11.1) |
| 4. Вердикт без порога серьёзности | Изменение не трогает: «- the last round found no blocker and no confirmed important finding;» (`skills/review-loop/SKILL.md:131`) |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: `tracker` нет у ревьюера. Свою ошибку скил отдаёт воркеру по правилу «as a worker, send the supervisor». Противоречие разметки между скилами — F5.7 |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». R16 связан с элементами: `sdlc-settings`, `tracker`, `supervisor`, `analyst`, `integrator`, `openspec-changes` (`BLUEPRINT.md`, обратная проверка). Единственная жёлтая мера (Worker roles = 4) обоснована. Раздел 4 совпадает с выводом скрипта. Утверждение R16 о Markdown — F5.7.

## Прежние находки

Отчёт 0.1.3 (`assessment`), 18 находок. Список автора в `build-report` сверен с файлами, всё совпадает.

- **F5.1** RESOLVED: `grep -rn "tracker.yaml" kit.yaml agents flows skills` ничего не находит. Проверяется только `.lado/sdlc.md` (`agents/analyst.md:32`).
- **F10.1** RESOLVED: «1. Read the task with its comments (skill `tracker`) and each task it links to.» (`agents/analyst.md:22`). Синтаксиса `get`/`link`/`comment` в ролях нет.
- **F5.2** RESOLVED как текст: wiki-разметки нет, шаблон в Markdown. Новое противоречие с tracker-jira-server — F5.7.
- **F5.3** RESOLVED: слова статусов из настроек убраны, правило «**Statuses** go by meaning» одно (`skills/sdlc-settings/SKILL.md:62`).
- **F5.4** RESOLVED: одно правило «**Assigning the task or labelling it**: when `tracker` can, do it; otherwise ask the» (`skills/sdlc-settings/SKILL.md:68`).
- **F5.5** RESOLVED: правило имени есть (`skills/openspec-changes/SKILL.md:37-40`), эпик — «when the tracker has epics». Копия у супервайзера — F6.3.
- **F9.2** RESOLVED: «## Writing to the tracker» (`skills/sdlc-settings/SKILL.md:48`), раздел **Tracker**.
- **F10.2** RESOLVED: «Day-to-day development of a project whose tasks live in a task tracker, one task per» (`kit.yaml:4`). README называет tracker-jira-server только как пример.
- **F2.1, F3.1, F4.1, F5.6, F6.1, F6.2, F7.1, F8.1, F9.1, F11.1** STILL OPEN: их оставил план, см. ниже. Их текст не менялся, например «    reads: [design, merge-report]» (`flows/task.yaml:113`).

## Вырезанные правила

| Убранное правило (файл:строка в базе) | Где оно сейчас |
|---|---|
| analyst.md:27-28 «ask them in your `ready` note to assign it by hand» | `agents/analyst.md:27-28` + `skills/sdlc-settings/SKILL.md:68-69` «ask the human in your note». Заметки аналитика бывают только на `ready`/`dropped`, так что смысл тот же |
| analyst.md:33 проверка `.lado/tracker.yaml` на целевой ветке | убрана по решению человека (R16); последствие — F3.2 |
| integrator.md:35 «a request to the human to set the waiting-for-release label by hand» (в `merge-report`) | частично потеряно — F4.2 |
| integrator.md:63 «posted as it is», перечитать | `agents/integrator.md:63` «as it is, read it back» |
| sdlc-settings:52 «an exit code is not proof that Jira took it» | `skills/sdlc-settings/SKILL.md:56` «a reported success is not proof that the» |
| sdlc-settings:53-55 «yes, create»; пропуск по типу задачи | `skills/sdlc-settings/SKILL.md:58-60` |
| sdlc-settings:57-58 «Until `tracker` can…» | `skills/sdlc-settings/SKILL.md:68-69` |
| supervisor.md:33 имя рана в нижнем регистре | `agents/supervisor.md:33` (копия — F6.3) |
| supervisor.md:61 «the label the human must set by hand» | `agents/supervisor.md:61` «what the human must do there by hand» |
| BLUEPRINT.md:64 источник «round 5 answer 2» | потерян — F9.4 |
| BLUEPRINT R11 «asks the human, in its note, to assign it to themselves» | `BLUEPRINT.md` R11 «asked in the agent's note»; «to the human» — в `agents/analyst.md:28` |

Остальные удаления (синтаксис `jira.py`, wiki-разметка, слова статусов, `.lado/tracker.yaml`) предусмотрены планом. Сверено по `git diff --word-diff 4b7d998`.

## Пропущено раньше

Нет: все находки полного прохода — в строках, которые изменил diff.

## Оставлено планом

Человек: «we do not take them, we handle them later»; записаны в BACKLOG.md.
- F2.1 `merge` не читает `ledger`.
- F3.1 согласие на противоречащую дельту на `merge_ok` (F3.17 отчёта 0.1.2).
- F4.1 макеты не показаны на `design_ok`.
- F5.6 ревьюер и файлы, переписанные сборкой.
- F6.1 выбор прохода в трёх местах.
- F6.2 правило cleanup пересказано в `do` шага `review`.
- F7.1 `review-loop` спрашивает человека напрямую.
- F8.1 `archive` молчит о повторном визите.
- F9.1 правило minor `fix` одной цепочкой.
- F11.1 относительный путь к `references/lenses.md`.

## Вопросы к человеку

1. Markdown и tracker-jira-server (F5.7). Человек решил: «the tracker skill formats it». Но tracker-jira-server v1.0.0 этого не делает: он велит писать в wiki-разметке и отправляет текст как есть.
   - (а) Правило в sdlc: черновик в Markdown, а если скил `tracker` называет свою разметку, агент пишет в ней с тем же содержанием и показывает в `merge-report` готовый к публикации текст.
   - (б) Сначала патч tracker-jira-server (Markdown → wiki), потом 0.2.0 с требованием этой версии в README.
   Рекомендация: (а). Это одна фраза в `sdlc-settings` и одна в R16, и решение человека остаётся в силе: разметку для своего трекера по-прежнему определяет скил `tracker`. (б) можно сделать позже и независимо.
2. Обновление Clens с 0.1.x (F5.8). Рекомендация: строка в README и предложение супервайзера переделать раздел **Jira** в **Tracker**.
