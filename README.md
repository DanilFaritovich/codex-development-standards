# Codex Development Standards

**English** | [Русский](./README.ru.md)

Reusable development workflows, testing conventions, architecture guidelines, and CI/CD standards for Codex-driven software projects.

**Repository:** https://github.com/DanilFaritovich/codex-development-standards

## Overview

This repository provides modular development standards that can be reused across software projects developed with Codex.

The goal is to keep agent-driven development predictable, efficient, and easy to maintain without loading unnecessary instructions into context.

The standards are organized by responsibility:

- **Workflow** — how a task moves from branch selection to a local commit, with optional user-requested publication.
- **Testing** — how tests are structured, named, written, and validated.
- **Architecture** — how application layers, boundaries, ports, adapters, and services are organized.
- **Stacks** — conventions specific to technologies such as FastAPI, Vue, SQLAlchemy, Docker, and Airflow.
- **Observability** — structured JSON logging and centralized log collection conventions.
- **API guardrails** — rate limits, bounded request bodies, and input constraints.
- **CI/CD** — automated validation and delivery conventions.
- **Infrastructure as Code** — safe, idempotent Ansible host provisioning and validation.
- **PDF artifacts and viewers** — evidence-based multilingual documents and safe PDF.js interaction layers.
- **Website acceptance** — real-browser responsive, visual, accessibility and localized QA.
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
- Use GitHub Actions for independent CI after a user-requested Pull Request.
- New task branches start only from `develop`. On `main`, first resolve or create `develop`; otherwise continue the active task branch.
- Default delivery is a local commit; push and PR only when explicitly requested.
- Change documentation only when requested, except a minimal update to an existing `ARCHITECTURE.md` for material architectural changes.
- Leave the final merge decision with the developer.

## Repository structure

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

  code-quality:
    path: .agents/skills/code-quality
    tags: [quality, formatting, lint, types, autofix]
    description: Safe auto-fix before read-only quality validation.

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
standards-sync
backend-clean-architecture
python-testing
code-quality
fastapi
api-guardrails
structured-logging
sqlalchemy-alembic
vue
vue-testing
docker
ansible
github-actions
continuous-delivery
documentation
licensing
```

Each skill should focus on one reusable concern.

For example:

- `python-testing` should define Python test structure and pytest conventions.
- `fastapi` should define FastAPI-specific rules.
- `backend-clean-architecture` should define layered/clean architecture with services, ports, and adapters independently of a particular framework.
- `github-actions` should define CI behavior.

Avoid putting unrelated concerns into one large instruction file.

For portfolio-style websites, select [`website-qa`](./.agents/skills/website-qa/SKILL.md) when auditing complete user journeys, [`pdfjs-viewer`](./.agents/skills/pdfjs-viewer/SKILL.md) for an embedded PDF.js viewer, and [`pdf-documents`](./.agents/skills/pdf-documents/SKILL.md) when creating or updating downloadable PDF artifacts. These are independent, optional concerns; adding them to a profile does not mean Codex should load all three on every task.

The existing [Docker skill](./.agents/skills/docker/SKILL.md) contains a static-site HTTP/container reference. The existing [Continuous Delivery skill](./.agents/skills/continuous-delivery/SKILL.md) contains a GHCR image-producer reference for repositories whose VPS deployment and shared reverse proxy are managed elsewhere. No unimplemented deployment controller is bundled with these standards.

### `profiles/`

Profiles combine reusable standards for common project types.

Example:

```yaml
name: fastapi-vue-clean

skills:
  - task-development-workflow
  - standards-sync
  - backend-clean-architecture
  - python-testing
  - code-quality
  - fastapi
  - api-guardrails
  - structured-logging
  - sqlalchemy-alembic
  - vue
  - vue-testing
  - docker
  - github-actions
  - documentation
  - licensing
```

Profiles should reference existing standards rather than duplicate their contents.

Optional standards may be listed separately when they should only be applied for projects that actually need them. For example, `continuous-delivery` is optional for `fastapi-vue-clean` and should be enabled when the project is deployed to a production environment.

For Linux VPS provisioning, use [`ansible-infrastructure`](./profiles/ansible-infrastructure.yaml). It combines the [Ansible skill](./.agents/skills/ansible/SKILL.md) with task workflow, quality, GitHub Actions, documentation, and licensing standards. Add the optional `docker` skill when Docker-host setup is in scope, and `continuous-delivery` only when release deployment is in scope. Token-efficiency rules stay in the existing workflow/quality standards; Ansible-specific recap and validation guidance is in the Ansible skill.

### Infrastructure acceptance and release lifecycle

For multi-stage Linux host provisioning, the [Ansible skill](./.agents/skills/ansible/SKILL.md) routes to [staged initialization](./.agents/skills/ansible/references/staged-initialization.md) and [isolated VM E2E](./.agents/skills/ansible/references/vm-e2e.md) **only when relevant**. Keep independent stage verification, fail-closed recovery and SSH route transitions explicit. A recommended acceptance sequence is: targeted offline checks → independent CI → separately authorized VM E2E → separately authorized real-host provisioning/verification → post-reboot acceptance when required.

Git delivery remains an independent approval boundary: feature PR to `develop` → release PR to `main` → user-authorized version tag/GitHub Release. Read the [Git delivery reference](./.agents/skills/task-development-workflow/references/git-delivery.md) for branch divergence, merge history and release checks. These are optional workflow phases, not automatic actions after successful CI.

### `bootstrap/`

Contains initialization instructions for applying the standards to an existing or new repository.

Current bootstrap:

- [`bootstrap/existing-project.md`](./bootstrap/existing-project.md) — initialize an existing repository using the catalog/profile model.

The bootstrap process should inspect the target project, determine its real stack and structure, select only relevant standards, and adapt them to the project.

## Usage

### Option 1 — Select a predefined profile

For a project using FastAPI and Vue:

```text
Initialize this repository using the development standards from:

https://github.com/DanilFaritovich/codex-development-standards

Read catalog.yaml first.

Use the fastapi-vue-clean profile.

Install only the standards required by that profile.

Adapt the standards to the actual repository structure, architecture, tools, and existing conventions.

Do not modify application business logic during initialization unless required for the development infrastructure.
```

### Static Vue sites

For a Vue 3 website served as built HTML/CSS/JavaScript files, use [`vue-static-site`](./profiles/vue-static-site.yaml). It fits portfolios, landing pages, documentation and other interactive sites without a required application backend. Static deployment does **not** mean the interface is non-interactive: localization, menus, modals and external API calls can still work. The profile installs the framework-agnostic [`seo`](./.agents/skills/seo/SKILL.md) skill: select a rendering/metadata approach, configure crawlable URLs, canonical/hreflang for multilingual sites, and verify built HTML. Use SSG/prerendering when indexable content needs to be present in the initial HTML.

The [`design-to-vue`](./.agents/skills/design-to-vue/SKILL.md) skill applies when translating design references into Vue. The [bootstrap guide](./bootstrap/vue-static-site.md) covers a standards-only start and optional repository-specific authorization for `gh` PR delivery without automatic merge. Docker/CD remain optional. For server-side rendering or a mandatory backend, use another profile or extend the project deliberately.

### Ansible infrastructure project

For an Ansible-based Linux server repository, select the `ansible-infrastructure` profile after reading `catalog.yaml`. Install only its applicable skills and keep `.codex-standards.lock.yaml` pinned to the exact upstream revision. Static CI must not contact production; applying playbooks, even in `--check` mode, requires separate authorization for a live host.

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
        ├── backend-clean-architecture/
        ├── fastapi/
        └── github-actions/
```

After installation, Codex should use the local project instructions instead of repeatedly fetching this repository.

Projects should also commit a `.codex-standards.lock.yaml` file recording the exact upstream commit, active profile/version, and installed skills.

### Incremental standards sync

For future updates, Codex should read the lock file first and compare its recorded upstream commit with the current standards commit.

It should inspect the changed-file list and fetch only changed installed skills, changed profile/catalog files, and newly applicable skills.

Do not reread every profile skill when most standards are unchanged.

See [the lock-file example](./bootstrap/codex-standards-lock.example.yaml) and the `standards-sync` skill.

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
AGENTS.md / relevant files
↓
if main, resolve/create develop; new task branches ONLY from develop; otherwise continue task branch
↓
implementation / tests
↓
targeted validation / project check
↓
requested documentation OR warranted existing ARCHITECTURE.md updates
↓
final diff
↓
local commit
↓
STOP — developer handles push / PR unless explicitly requested
```

The agent should not use the full CI pipeline as its normal debugging loop. A request to create a PR also authorizes the task-branch push required for it.

## Versioning

Projects should preferably consume a tagged release instead of depending permanently on the latest `main`.

Example:

```text
codex-development-standards@v0.1.0
```

This keeps the installed development rules reproducible.

A project can intentionally upgrade to a newer standards release later.

## Updating installed standards

When upgrading an existing project, use the committed standards lock to compare the previously installed upstream revision with the desired revision and preserve project-specific changes.

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
