# Codex Development Standards

Reusable development workflows, testing conventions, architecture guidelines, and CI/CD standards for Codex-driven software projects.

**Repository:** https://github.com/DanilFaritovich/codex-development-standards

## Overview

This repository provides modular development standards that can be reused across software projects developed with Codex.

The goal is to keep agent-driven development predictable, efficient, and easy to maintain without loading unnecessary instructions into context.

The standards are organized by responsibility:

- **Workflow** — how a development task is executed from analysis to Pull Request.
- **Testing** — how tests are structured, named, written, and validated.
- **Architecture** — how application layers, boundaries, ports, adapters, and services are organized.
- **Stacks** — conventions specific to technologies such as FastAPI, Vue, SQLAlchemy, Docker, and Airflow.
- **CI/CD** — automated validation and delivery conventions.
- **Profiles** — predefined combinations of standards for common project types.

Only the standards relevant to a project should be installed.

## Core principles

- Read only the context required for the current task.
- Use `AGENTS.md` as the project-specific development map.
- Keep reusable rules separate from project-specific instructions.
- Implement the complete requested scope before final validation.
- Add or update tests when application logic changes.
- Prefer targeted checks while developing.
- Do not rerun the full validation pipeline after every small fix.
- Do not reread unchanged or successfully edited files without a concrete reason.
- Run one broader project check after the implementation stabilizes.
- Use GitHub Actions as the final independent CI validation.
- Develop in task branches.
- Keep the final merge decision with the developer.

## Repository structure

```text
.
├── README.md
├── catalog.yaml
├── .agents/
│   └── skills/
├── profiles/
└── bootstrap/
```

The repository can grow gradually. Not every directory has to exist from the first version.

### `catalog.yaml`

A machine-readable index of available standards.

Codex should read this file first when selecting instructions for a project instead of scanning the entire repository.

Example:

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

Contains reusable engineering standards.

Possible skills:

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

Each skill should focus on one reusable concern.

For example:

- `python-testing` should define Python test structure and pytest conventions.
- `fastapi` should define FastAPI-specific rules.
- `backend-layered-architecture` should define architecture independently of a particular framework.
- `github-actions` should define CI behavior.

Avoid putting unrelated concerns into one large instruction file.

### `profiles/`

Profiles combine reusable standards for common project types.

Example:

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

Profiles should reference existing standards rather than duplicate their contents.

### `bootstrap/`

Contains initialization instructions for applying the standards to an existing or new repository.

The bootstrap process should inspect the target project, determine its real stack and structure, select only relevant standards, and adapt them to the project.

## Usage

### Option 1 — Select a predefined profile

For a project using FastAPI and Vue:

```text
Initialize this repository using the development standards from:

https://github.com/DanilFaritovich/codex-development-standards

Read catalog.yaml first.

Use the fastapi-vue profile.

Install only the standards required by that profile.

Adapt the standards to the actual repository structure, architecture, tools, and existing conventions.

Do not modify application business logic during initialization unless required for the development infrastructure.
```

### Option 2 — Describe the stack

You do not need to know the profile name.

Example:

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

### Option 3 — Let Codex inspect the project

For an existing repository:

```text
Inspect the current repository and determine its actual stack, architecture, testing tools, and CI configuration.

Then use the standards from:

https://github.com/DanilFaritovich/codex-development-standards

Read catalog.yaml first.

Select only the standards relevant to this project.

Do not introduce technologies that are not already used unless they are explicitly required.

Install and adapt the selected standards for continued Codex development.
```

## Recommended installation model

This repository should normally be used as an **installation source**, not as a runtime dependency for every development task.

Relevant standards should be copied or adapted into the target repository.

Example:

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

After installation, Codex should use the local project instructions instead of repeatedly fetching this repository.

This makes the project:

- reproducible;
- independent from network availability;
- protected from unexpected upstream changes;
- more efficient in context usage.

## Standard development lifecycle

A normal task should follow the same high-level workflow:

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

The agent should not use the full CI pipeline as its normal debugging loop.

## Versioning

Projects should preferably consume a tagged release instead of depending permanently on the latest `main`.

Example:

```text
codex-development-standards@v0.1.0
```

This keeps the installed development rules reproducible.

A project can intentionally upgrade to a newer standards release later.

## Updating installed standards

When upgrading an existing project, compare its installed rules with the desired release and preserve project-specific changes.

Example:

```text
Compare the Codex development standards installed in this repository with the latest compatible release from:

https://github.com/DanilFaritovich/codex-development-standards

Inspect only the standards already installed in this project.

Preserve project-specific AGENTS.md rules.

Show meaningful differences before applying updates.
```

## Contributing

Contributions should keep standards modular and reusable.

A new standard should be introduced as a separate module when it represents an independent concern that can be reused across multiple project types.

Avoid duplicating the same rules across profiles.

## License

MIT
