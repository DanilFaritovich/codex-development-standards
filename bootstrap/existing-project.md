# Existing Project Bootstrap

Use this bootstrap when applying Codex Development Standards to an existing repository.

## Source

Standards repository:

https://github.com/DanilFaritovich/codex-development-standards

## Goal

Prepare the current repository for continued Codex-driven development without replacing valid existing architecture or business logic.

Use the standards repository as an installation source. After installation, the target project should rely on its local `AGENTS.md`, `ARCHITECTURE.md`, selected skills, Makefiles, and CI configuration.

## Procedure

1. Inspect the target repository enough to determine:
   - languages and frameworks;
   - backend/frontend structure;
   - architecture;
   - testing tools;
   - persistence/database;
   - Docker/Compose;
   - CI/CD;
   - existing Makefiles/package scripts;
   - documentation;
   - current license;
   - branch strategy.

2. Read only `catalog.yaml` from the standards repository first.

3. Select either:
   - a matching predefined profile; or
   - only the individual skills relevant to the detected stack.

4. Do not load unrelated skills.

5. Preserve existing project decisions when they are compatible with the selected standards.

6. Do not introduce unused technologies merely because a profile supports them.

7. Create or adapt project-local development guidance:
   - `AGENTS.md`;
   - `ARCHITECTURE.md` when architecture is non-trivial;
   - relevant local skills under `.agents/skills/` when the project uses them;
   - Makefiles or equivalent stable validation commands;
   - GitHub Actions CI when GitHub is the CI provider.

8. Ask for user input only when a policy decision cannot be safely inferred, especially:
   - documentation language mode: English, Russian, or both;
   - license choice when no license exists;
   - destructive architecture changes;
   - adding a technology not already used by the project.

9. Do not modify application business logic unless required to make the development infrastructure valid.

10. Validate the installation using targeted checks first, then the project's normal fast check.

11. Do not repeatedly run full local CI. Leave complete CI verification to GitHub Actions unless there is a concrete reason to run it locally.

12. Review the final diff once before commit/push.

## Recommended standard profile

For a project using:

- Python;
- FastAPI;
- layered clean architecture;
- ports and adapters;
- SQLAlchemy;
- Alembic;
- Vue;
- Docker;
- GitHub Actions;

use:

`profiles/fastapi-vue-clean.yaml`

## Expected target project

A configured project may contain:

```text
project/
├── AGENTS.md
├── ARCHITECTURE.md
├── README.md
├── README.ru.md          # when bilingual docs are enabled
├── Makefile
├── .agents/
│   └── skills/
├── .github/
│   └── workflows/
├── backend/
└── frontend/
```

The exact structure must be adapted to the existing repository.

## Task workflow after installation

Normal future tasks should follow:

```text
task
 -> AGENTS.md
 -> relevant files
 -> implementation
 -> tests
 -> targeted validation
 -> make check
 -> optional make verify
 -> final diff
 -> documentation when required
 -> commit
 -> push task branch
 -> Pull Request
 -> GitHub CI
 -> developer review/merge
```

Codex must not automatically merge the Pull Request unless the user explicitly requests that separate action.
