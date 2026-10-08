# Отчёт о ките: sdlc 0.1.2

- Дата: 2026-10-08
- Кит: worktree рана `improve/sdlc-2` (`.lado/worktrees/kit-comet/improve-sdlc-2`), путь дан задачей. В `kit.yaml` пока стоит `version: 0.1.1`: версию поднимают при релизе, отчёт назван по плановой версии из плана.
- Коммит: c51a486 (визит 4 шага `evaluate`; визиты 1–3 оценивали f5f61be, 9802ff5 и d3af76d, их версии этого отчёта — коммиты d6e1e23, a003e81 и 310267b)
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/sdlc-0.1.1-2026-10-08.md` (артефакт `assessment`) и отчёта визита 3. База — коммит из плана, 1422184. Правки этого визита: `git diff d3af76d c51a486` (5 файлов, +53 −31).
- Проходы: три независимых суб-агента, у каждого рубрика, папка кита, зависимые скилы, diff визита и отчёт визита 3. Начинали с разных мест: 1 — роли, 2 — флоу по шагам, 3 — скилы. Полный проход (Re-evaluation 5) по 5 файлам из diff сделал четвёртый суб-агент. Каждую его находку я подтвердил в файлах. Одиночных находок отброшено 0, оставлена как подтверждённая 1 (F8.5). Вырезанных правил нет: автор менял фразы на месте, все три прохода это подтвердили.

Находки — это кандидаты, которые взвешивает человек, а не оценка «прошёл / не прошёл».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый; Worker roles = 4 (обосновано в блупринте: R12, R4). analyst.md — 798 слов из 800 зелёных |
| a. Флоу | 1 нарисован; совпадает со скелетом блупринта |
| b. Рубрика | 3 находки в тексте, изменённом на визите 4 (0 high, 3 medium, 0 low); 9 из 12 критериев без находок. Ещё 2 в «Пропущено раньше» (1 medium, 1 low) |
| Охват | повторная оценка изменённого текста и полный проход по 5 файлам, которые затронул diff |
| Правило остановки | не выполнено: 0 high, 3 medium. Это совет для гейта релиза, не блок |

Счёт относится только к тому, что указано в строке «Охват»: с полной оценкой он не сравним.

### `lado kits check .`

```
sdlc: OK (4 agents, 10 skills, 2 packs and 1 flows)
expects skills: tracker
```

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: sdlc 0.1.1

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

![task](sdlc-0.1.2-2026-10-08/task.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

1. «Archive first» в `merge`: сказать, что одобрение на `merge_ok` заменяет шаг 1 «Archive»; добавить `ledger` в `reads`; дать выход, если архив не получается; оставить «accepts the open findings» только для несошедшегося ревью (F3.17).
2. Дать пробе ревьюера путь к разработчику: fate `fix` для `unverifiable` с пробой (F2.12).
3. Сказать, что делает `review` после возврата с пустой дельтой (F8.5).

## Находки

### 1. Границы ролей

Ревьюер больше не правит файлы ради пробы: «make none yourself» (`agents/reviewer.md:19`). Куда идёт проба — F2.12.

### 2. Передача между шагами

- **F2.12** [medium] `agents/reviewer.md:19`
  > with the probe for the developer to run; make none yourself.
  До разработчика проба не доходит. Он чинит только строки с fate «fix»: «ledger finding whose fate is "fix", as `review-loop` says ("Fixing a finding"), one» (`agents/developer.md:63`). У `unverifiable` нет ни уровня, ни fate: «stack or data not at hand; make it verifiable (a test, the stack) or carry it as residual» (`skills/review-loop/SKILL.md:41`). Один ревьюер поставит строке fate `fix`, другой оставит её остаточным риском, и возможный ложный зелёный дойдёт до гейта непроверенным. Проходы оценили средне, средне и низко; взято medium.
  Fix: в «Fate» `review-loop`: «An unverifiable finding with a probe the developer can run: `fix` (run the probe and report what it showed in `build-report`); the next round sets its verdict.» Passes: 3/3 (и полный проход)

### 3. Готовность и исходы

- **F3.17** [medium] `flows/task.yaml:117-118` (state `merge`)
  > When `merge-report` says nothing was archived and the human accepted the findings at
  > `merge_ok`, archive first ("Archive"), then merge.
  «Archive» начинается с проверки, которую на этом пути пройти нельзя: «1. Check the review converged: the ledger says its last round met the stop criterion,» (`agents/integrator.md:22`). Согласие человека записано в записке гейта, а не в ledger. `merge` не читает `ledger` («reads: [design, merge-report]», `flows/task.yaml:113`), а закрывающий отчёт архива пишется из него. «accepts the open findings» в `archive` предложено для всех трёх причин не архивировать: «review converged, a task not checked without a reason, a delta that contradicts a» (`flows/task.yaml:98`). Но противоречие живому спеку согласием не снять: шаг архива «3. `openspec validate --specs --strict` passes, and the grep for contradictions (above)» (`skills/openspec-changes/SKILL.md:74`) провалится снова. Для этого у `merge` нет исхода («outcomes: {merged: done, conflicts: build}», `:128`). Один интегратор остановится и спросит, другой сольёт без архива, третий заархивирует противоречащую дельту.
  Fix: в `archive` оставить «or accepts the open findings there» только для несошедшегося ревью. В `merge`: «…archive first ("Archive": the human's approval stands for its step 1; the closing report lists the accepted findings as residual risk), then merge; when the archive cannot be made, merge nothing and send it to the supervisor, staying in `merge`.» Добавить `ledger` в `reads` `merge`. Passes: 3/3 (и полный проход)

### 4. Независимая проверка

Мерж по-прежнему только после `merge_ok`. Архив внутри `merge` делается уже после гейта, но человек одобрял, зная, что архива нет. Что архив может не получиться — F3.17.

### 5. Противоречия

Новые правки согласованы: «checked or says why not» в `build` (`flows/task.yaml:57`) и в проверке ревью (`skills/review-loop/SKILL.md:68-69`) совпадает с `openspec-changes`. «Fixing» разработчика совпадает с исходами `merge`. Конфликт и красный CI после вливания пересекаются: повторное вливание уже влитой цели безвредно.

### 6. Дублирование

Скрипт похожих абзацев ничего не нашёл. «Checked or says why not» в двух местах — одно правило в двух проверках, разных по шагам.

### 7. Когда звать человека

Ответ человека о пропуске внешнего раунда теперь выполняется в обе стороны: «and do as they say (a skip counts as run:» (`skills/review-loop/SKILL.md:123`).

### 8. Циклы на повторном визите

- **F8.5** [medium] `flows/task.yaml:54-55` (state `build`, затем `review`)
  > `build-report` and fix it like a finding; when it is only that the review did not
  На этом пути разработчик ничего не меняет, и `review` получает пустую дельту. Единственное правило для возврата из `merge_ok`: «from `merge_ok` or `conflicts`, the delta gets a precision pass and an external round» (`skills/review-loop/SKILL.md:139`), то есть внешний раунд по пустой дельте. Ни `do` шага `review`, ни роль ревьюера не велят разобрать причину отказа, которая процитирована только в начале `build-report`. Ревьюер скорее всего снова отчитается `converged` с тем же ledger, и ран пойдёт `archive` → `merge_ok` ещё раз: лишний круг через гейт, ограниченный только человеком.
  Fix: в `do` шага `review`: «After a return from `merge_ok` with no new commits, request no external round; close in `ledger` what the rejection quoted at the top of `build-report` names (run the missing rounds, or record the human's decision).» Passes: 1/3, confirmed (`skills/review-loop/SKILL.md:139`; `flows/task.yaml:78` «cleanup and final rounds, and a return after `merge_ok` or `conflicts`; reaching the»)

### 9. Краткость и «зачем»

Без находок: правки короткие, analyst.md не тронут.

### 10. Описания скилов

Описания и списки `skills:` не менялись.

### 11. Нейтральность к провайдеру

Новых команд и инструментов нет.

### 12. Безопасность и рамки

Без новых находок в изменённом тексте. Возврат статуса спрашивается и для «review» (`flows/task.yaml:30-31`). Путь мимо проверки архива при одобрении — F3.17.

## Известные дыры

| Известная дыра | Находка или чем кит её закрывает |
|---|---|
| 1. Красная проверка отправлена назад без поиска причины в окружении | Закрыта: интегратор разделяет причины (`agents/integrator.md:44-47`), разработчик вливает цель при локальной красной проверке (`agents/developer.md:66-68`). Неудавшийся архив в `merge` такого разделения не имеет — F3.17 |
| 2. Работа вне флоу, мерж без гейта | Вне рана ничего нет. Мерж только после `merge_ok`; архив после гейта без выхода при неудаче — F3.17 |
| 3. Путь вне worktree рана | Закрыта: новых путей нет |
| 4. Вердикт без порога серьёзности | Закрыта: `skills/review-loop/SKILL.md:131` |
| 5. Зависимый скил пишет или спрашивает там, где роли нельзя | Закрыта: пробная правка из `engineering-rules` для ревьюера отменена (`agents/reviewer.md:17-19`). Восстановление переписанных сборкой файлов — F1.5 в «Пропущено раньше» |

## Не прослежено

Нет. `--compare`: «same as the plan in BLUEPRINT.md». Единственная жёлтая мера (Worker roles = 4) обоснована (R12, R4). Раздел 4 блупринта совпадает с выводом скрипта (developer 614, reviewer 419). Строки `task.archive` и `task.merge` (`BLUEPRINT.md:176`, `:178`) не упоминают архив внутри `merge`, но журнал это записывает (`:238`).

## Прежние находки

Отчёт 0.1.1 (12), визит 1 (6), визит 2 (19): все RESOLVED, ничего не откатилось.

Отчёт визита 3 (310267b), все 10 RESOLVED. Сверено всеми тремя проходами:
- **F2.11** RESOLVED: «conflict, or a red check after the target was brought in, merge `origin/<target>` into» (`agents/developer.md:67`).
- **F3.15** RESOLVED как текст: `archive` (`flows/task.yaml:99-100`), `merge` (`:117-118`), `build` (`:54-55`). Новые пробелы этого текста — F3.17 и F8.5.
- **F3.16** RESOLVED: «behind yours, report `changes` with "push the head <sha>"; when it is ahead or diverged,» (`skills/review-loop/SKILL.md:93`).
- **F7.5** RESOLVED: `skills/review-loop/SKILL.md:123-124`.
- **F9.4** RESOLVED: «(after `redesign`, the revised one).» (`flows/task.yaml:51`).
- **F12.10** RESOLVED: «"in progress" or "review"» (`flows/task.yaml:30`).
- **F5.17** RESOLVED: `flows/task.yaml:57`, `skills/review-loop/SKILL.md:68-69`.
- **F5.18** RESOLVED: `agents/reviewer.md:17-19`. Путь пробы — F2.12.
- **F8.4** RESOLVED: `skills/review-loop/SKILL.md:138-140`. Пустая дельта — F8.5.
- **F5.19** RESOLVED: `BLUEPRINT.md:178`.

Список автора в `build-report` сверен с файлами: все 10 подтверждены.

## Вырезанные правила

Визиты 1–3 — в версиях отчёта на d6e1e23, a003e81 и 310267b. Визит 4: ни одно правило не убрано. Перенос строк в «Fixing» разработчика и в шаге 2 `review-loop` правил не теряет (`git diff --word-diff d3af76d c51a486`).

## Пропущено раньше

Находки «Пропущено раньше» визитов 1–3 все RESOLVED. Новые с полного прохода визита 4:

- **F3.18** [medium] `skills/review-loop/SKILL.md:72`
  > 4. **Checks.** Run the project's checks (`sdlc-settings`, **Checks**) and the pull
  Ревьюер приходит сразу после пуша разработчика, когда CI обычно ещё идёт. Критерий остановки требует зелёный CI: «- the pull request's CI is green: no findings over a red pipeline is not mergeable;» (`:132`). Ждать ли CI и сколько, не сказано. Ожидание с потолком есть только у интегратора: «2. Wait for the pull request's CI, at most the CI time ceiling of **Checks**, then merge» (`agents/integrator.md:48`). Один ревьюер ответит `changes` без находок и потратит визиты `build` и `review`, другой отчитается `converged` над незавершённым CI.
  Fix: «…and the pull request's CI state, waiting for a running pipeline up to the CI time ceiling of **Checks**; one still running at the ceiling goes to the supervisor.» Passes: full pass, confirmed

- **F1.5** [low] `agents/reviewer.md:15-16`
  > You are read-only on the code: you read, run checks and comment on the pull request, and
  Проверки проекта могут переписать отслеживаемые файлы («- **A build that rewrites tracked files** (generated types, lockfiles, snapshots): not a», `skills/review-loop/references/false-greens.md:19`). `engineering-rules` велит их восстановить («restore tracked files a build rewrote.», `skills/engineering-rules/SKILL.md:54`), а роль запрещает checkout. Файлы остаются в worktree разработчика. Они не теряются: `git status` их показывает, а разработчик коммитит по имени.
  Fix: «…no checkout, reset or stash in the run's worktree, except restoring the tracked files your checks rewrote,». Passes: full pass, confirmed

## Оставлено планом

План ничего не оставляет («Leave: none»).

## Вопросы к человеку

1. Правило остановки не выполнено четвёртый визит подряд. Видна закономерность: каждая medium этого визита (F3.17, F2.12, F8.5) сидит в тексте, который добавило исправление прошлого визита, а полный проход каждый раз находит 1–4 находки в старом тексте. Ещё один круг, скорее всего, даст ещё 1–3 medium в новом тексте. Варианты: (а) один круг только по F3.17 (одобрение без архива, ближе всего к необратимому) с узкой правкой «merge nothing and send it to the supervisor», без новой оценки, если других правок нет; (б) выпустить 0.1.2 сейчас и перенести F3.17, F2.12, F8.5, F3.18, F1.5 в бэклог 0.1.3. Рекомендация: (б), если путь «принять находки на `merge_ok` без архива» не нужен в 0.1.2: убрать из `archive` и `merge` фразы о принятии (вернуть «the human sends it back»), и F3.17 исчезнет вместе с ними. Это откат одной правки, а не новый текст. F8.5 (пустая дельта после отказа) останется до 0.1.3.
