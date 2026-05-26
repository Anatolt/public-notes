# Анализ skill `b-on-g/mol_skill`

Дата анализа: 2026-05-26

## Краткий вывод

Skill выглядит полезным как справочник и инструкция для разработки на `$mol/MAM`, но в текущем виде содержит много артефактов, которые выглядят случайно попавшими в публичный репозиторий.

Основная полезная часть skill’а, вероятно:

```text
SKILL.md
README.md
agents/openai.yaml
references/
```

Остальные директории и файлы выглядят как рабочие материалы конкретного проекта или личного агентного workflow.

## Что выглядит нормально

В `SKILL.md` описан ожидаемый workflow для `$mol/MAM`, `view.tree`, `view.ts`, `Giper Baza`, `Tauri`.

В `references/` лежат справочные материалы по:

- `$mol`;
- MAM;
- Giper Baza;
- Tauri;
- стилю написания `$mol`-кода.

Эта часть похожа на содержимое, ожидаемое от публичного coding-agent skill.

## Что выглядит лишним или случайным

В репозиторий, вероятно, попали рабочие артефакты конкретного проекта:

```text
app/
invoicer/
.github/workflows/deploy.yml
```

Особенно выделяются:

```text
invoicer/tasks.json
invoicer/progress.md
invoicer/prd.md
invoicer/prompt.md
invoicer/ralph.sh
invoicer/bot/
invoicer/docker-compose.yml
```

Они выглядят не как часть публичного skill’а, а как внутренний backlog, demo-проект или эксперимент автора.

## Признаки утечки рабочих артефактов

В `invoicer/tasks.json` лежит backlog задач конкретного проекта:

- `INV-001` — настраиваемый LLM endpoint;
- `INV-003` — UX-улучшения;
- `INV-005` — Telegram Mini App;
- `INV-006` — баг с шестерёнкой настроек;
- и другие.

В `progress.md` есть журнал выполнения задач агентами.

В `prompt.md` есть жёсткие инструкции агенту, включая:

```text
git add, git commit, git push
```

Также встречаются абсолютные локальные пути:

```text
/Users/cmyser/code/mam
```

Это почти наверняка не должно быть частью публичного skill’а.

## Про `ralph.sh`

`invoicer/ralph.sh` не выглядит как публичный фреймворк. Это самописный bash-скрипт-автопилот для агентного workflow.

Он делает следующее:

- читает `tasks.json`;
- выбирает задачу со статусом `pending`;
- подставляет ID задачи в `prompt.md`;
- запускает `claude` или `codex`;
- для Codex использует:

```bash
codex exec --full-auto
```

- ждёт маркеры:

```text
RALPH_COMPLETE
RALPH_PARTIAL
```

Похоже, что “Ralph” здесь — имя личной автоматизации автора, а не часть `$mol`, MAM или известного публичного инструмента.

## Почему security scanners могли отметить High Risk

Вероятные причины высокой оценки риска:

- наличие исполняемого файла `ralph.sh`;
- автоматический запуск `claude`/`codex` с широкими полномочиями;
- использование `codex exec --full-auto`;
- GitHub workflow с:

```yaml
permissions: write-all
schedule:
```

- Telegram bot, работающий с `BOT_TOKEN`, `LLM_KEY`, внешними API;
- Docker Compose с Ollama и curl-загрузкой модели;
- инструкции на `commit`/`push`.

Явного вредоносного кода при беглом просмотре не обнаружено, но поверхность риска заметно выше, чем ожидается от документационного skill’а.

## Рекомендации разработчику

1. Оставить в публичном skill минимальный набор:

```text
SKILL.md
README.md
agents/openai.yaml
references/
```

2. Удалить или вынести в отдельный example-репозиторий:

```text
app/
invoicer/
.github/
```

3. Если `invoicer` нужен как пример, явно оформить его как demo/example и почистить:

- убрать `tasks.json`;
- убрать `progress.md`;
- убрать `prompt.md`;
- убрать `ralph.sh`;
- убрать абсолютные локальные пути;
- убрать инструкции `git commit/push`;
- добавить README с явным предупреждением, что это example.

4. Убрать workflow с `permissions: write-all`, если он не нужен для skill’а.

5. Проверить git history на возможные секреты, особенно вокруг:

```text
BOT_TOKEN
LLM_KEY
Authorization
```

6. Добавить в README описание состава репозитория: что является самим skill’ом, а что является примерами.

## Итог

Skill как набор знаний по `$mol` выглядит полезным, но публичный репозиторий сейчас выглядит “грязным”: в него, вероятно, попали внутренние проектные файлы и агентный workflow. Это может объяснять `High Risk` оценку и снижает доверие к skill’у.
