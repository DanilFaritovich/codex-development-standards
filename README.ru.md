# Codex Development Standards

[English](./README.md) | **Русский**

Переиспользуемые процессы разработки, соглашения по тестированию, архитектурные рекомендации и стандарты CI/CD для программных проектов, разрабатываемых с помощью Codex.

**Репозиторий:** https://github.com/DanilFaritovich/codex-development-standards

## Обзор

Этот репозиторий содержит модульные стандарты разработки, которые можно переиспользовать в разных программных проектах, разрабатываемых с помощью Codex.

Цель — сделать агентную разработку предсказуемой, эффективной и удобной для поддержки, не загружая в контекст лишние инструкции.

Стандарты разделены по ответственности:

- **Workflow** — как выполняется задача разработки от анализа до Pull Request.
- **Testing** — как организуются, именуются, пишутся и проверяются тесты.
- **Architecture** — как организованы слои приложения, границы, порты, адаптеры и сервисы.
- **Stacks** — соглашения для конкретных технологий, таких как FastAPI, Vue, SQLAlchemy, Docker и Airflow.
- **CI/CD** — соглашения для автоматизированной проверки и доставки.
- **Profiles** — готовые комбинации стандартов для распространённых типов проектов.

В проект следует устанавливать только те стандарты, которые действительно ему подходят.

## Основные принципы

- Читать только тот контекст, который требуется для текущей задачи.
- Использовать `AGENTS.md` как карту разработки конкретного проекта.
- Хранить переиспользуемые правила отдельно от проектных инструкций.
- Реализовывать весь запрошенный объём изменений до финальной проверки.
- Добавлять или обновлять тесты при изменении логики приложения.
- Во время разработки предпочитать точечные проверки.
- Не перезапускать весь pipeline проверки после каждого небольшого исправления.
- Не перечитывать неизменённые или успешно отредактированные файлы без конкретной причины.
- Выполнять одну более широкую проверку проекта после стабилизации реализации.
- Использовать GitHub Actions как финальную независимую CI-проверку.
- Разрабатывать изменения в отдельных task-ветках.
- Оставлять финальное решение о merge за разработчиком.

## Структура репозитория

```text
.
├── README.md
├── README.ru.md
├── catalog.yaml
├── .agents/
│   └── skills/
├── profiles/
└── bootstrap/
```

Репозиторий может развиваться постепенно. Не все директории обязаны существовать уже в первой версии.

### `catalog.yaml`

Машиночитаемый индекс доступных стандартов.

При выборе инструкций для проекта Codex должен сначала читать этот файл, а не сканировать весь репозиторий.

Пример:

```yaml
version: 1

skills:
  task-workflow:
    path: .agents/skills/task-development-workflow
    tags: [workflow, development]
    description: Standard workflow for implementing one development task.

  python-testing:
    path: .agents/skills/python-testing
    tags: [python, testing, pytest]
    description: Python testing structure, naming, fixtures, and validation rules.

  fastapi:
    path: .agents/skills/fastapi
    tags: [python, backend, fastapi]
    description: FastAPI-specific backend conventions.

  vue:
    path: .agents/skills/vue
    tags: [frontend, vue, typescript]
    description: Vue frontend conventions.
```

### `.agents/skills/`

Содержит переиспользуемые инженерные стандарты.

Возможные skills:

```text
task-development-workflow
python-testing
vue-testing
backend-layered-architecture
fastapi
vue
sqlalchemy
postgres
docker
airflow
github-actions
```

Каждый skill должен описывать одну переиспользуемую область ответственности.

Например:

- `python-testing` определяет структуру Python-тестов и соглашения pytest.
- `fastapi` определяет правила, специфичные для FastAPI.
- `backend-layered-architecture` определяет архитектуру независимо от конкретного framework.
- `github-actions` определяет поведение CI.

Не объединяйте несвязанные области в один большой файл инструкций.

### `profiles/`

Profiles объединяют переиспользуемые стандарты для распространённых типов проектов.

Пример:

```yaml
name: fastapi-vue

skills:
  - task-development-workflow
  - python-testing
  - vue-testing
  - backend-layered-architecture
  - fastapi
  - sqlalchemy
  - postgres
  - vue
  - docker
  - github-actions
```

Profiles должны ссылаться на существующие стандарты, а не дублировать их содержимое.

### `bootstrap/`

Содержит инструкции для применения стандартов к существующему или новому репозиторию.

Bootstrap-процесс должен изучить целевой проект, определить его фактический стек и структуру, выбрать только релевантные стандарты и адаптировать их под проект.

## Использование

### Вариант 1 — выбрать готовый профиль

Для проекта на FastAPI и Vue:

```text
Initialize this repository using the development standards from:

https://github.com/DanilFaritovich/codex-development-standards

Read catalog.yaml first.

Use the fastapi-vue profile.

Install only the standards required by that profile.

Adapt the standards to the actual repository structure, architecture, tools, and existing conventions.

Do not modify application business logic during initialization unless required for the development infrastructure.
```

### Вариант 2 — описать стек проекта

Знать имя готового профиля необязательно.

Пример:

```text
Use the development standards from:

https://github.com/DanilFaritovich/codex-development-standards

Current project stack:

- Backend: Python + FastAPI
- ORM: SQLAlchemy
- Database: PostgreSQL
- Frontend: Vue + TypeScript
- Infrastructure: Docker
- CI: GitHub Actions

Read catalog.yaml first.

Select only the standards relevant to this stack.

Do not read or install unrelated standards.

Adapt the selected standards to the current repository instead of copying generic structures blindly.
```

### Вариант 3 — позволить Codex самостоятельно изучить проект

Для существующего репозитория:

```text
Inspect the current repository and determine its actual stack, architecture, testing tools, and CI configuration.

Then use the standards from:

https://github.com/DanilFaritovich/codex-development-standards

Read catalog.yaml first.

Select only the standards relevant to this project.

Do not introduce technologies that are not already used unless they are explicitly required.

Install and adapt the selected standards for continued Codex development.
```

## Рекомендуемая модель установки

Обычно этот репозиторий следует использовать как **источник для установки стандартов**, а не как runtime-зависимость при выполнении каждой задачи разработки.

Нужные стандарты следует копировать или адаптировать в целевой репозиторий.

Пример:

```text
target-project/
├── AGENTS.md
├── ARCHITECTURE.md
└── .agents/
    └── skills/
        ├── task-development-workflow/
        ├── python-testing/
        ├── backend-layered-architecture/
        ├── fastapi/
        └── github-actions/
```

После установки Codex должен использовать локальные инструкции проекта вместо повторного обращения к этому репозиторию.

Это делает проект:

- воспроизводимым;
- независимым от доступности сети;
- защищённым от неожиданных изменений upstream-стандартов;
- более эффективным с точки зрения использования контекста.

## Стандартный жизненный цикл разработки

Обычная задача должна проходить по одному и тому же высокоуровневому workflow:

```text
task
↓
AGENTS.md
↓
relevant source files
↓
implementation
↓
tests
↓
targeted validation
↓
project check
↓
final diff
↓
documentation if required
↓
commit
↓
push task branch
↓
Pull Request
↓
GitHub CI
↓
developer review and merge
```

Агент не должен использовать полный CI pipeline как обычный цикл отладки.

## Версионирование

Проектам рекомендуется использовать tagged release стандартов вместо постоянной зависимости от последнего состояния `main`.

Пример:

```text
codex-development-standards@v0.1.0
```

Это делает установленные правила разработки воспроизводимыми.

Позже проект может осознанно обновиться до новой версии стандартов.

## Обновление установленных стандартов

При обновлении существующего проекта следует сравнивать его установленные правила с нужной версией и сохранять проектные изменения.

Пример:

```text
Compare the Codex development standards installed in this repository with the latest compatible release from:

https://github.com/DanilFaritovich/codex-development-standards

Inspect only the standards already installed in this project.

Preserve project-specific AGENTS.md rules.

Show meaningful differences before applying updates.
```

## Вклад в проект

Изменения должны сохранять модульность и переиспользуемость стандартов.

Новый стандарт стоит добавлять как отдельный модуль, если он описывает независимую область, которую можно применять в нескольких типах проектов.

Не дублируйте одинаковые правила в разных profiles.

## Лицензия

MIT
