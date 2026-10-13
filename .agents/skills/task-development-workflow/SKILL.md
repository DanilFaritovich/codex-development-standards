---
name: task-development-workflow
description: Core workflow from scoped implementation and validation to local commit; push/PR and documentation changes only on explicit user request.
---

# Task Development Workflow

Use this core workflow for normal feature, fix, refactor, chore, and documentation tasks unless project-local instructions are stricter.

## Core goals

- implement the complete requested scope;
- read only context required for the task;
- preserve unrelated user changes;
- follow existing architecture and conventions;
- normalize before read-only validation;
- use targeted checks during development;
- use an available optional execution worker for bounded high-output or mechanical loops
  when delegation clearly reduces primary-model context;
- avoid repeated reads/checks;
- leave remote publication, Pull Requests, and merge to the developer unless explicitly requested.

## Core workflow

```text
inspect branch/worktree
-> create task branch on develop/main OR continue the current task branch
-> determine scope and read relevant context
-> implement complete change
-> make fix / component fix
-> targeted validation
-> make check once
-> optional make verify
-> documentation ONLY if explicitly requested
-> final review
-> local commit
-> STOP (push/PR/merge only on explicit request)
```

## Explicit task stop points

The default delivery boundary is a local commit, not remote publication. Explicit user instructions can request an earlier stop or separately authorize a push/PR.

If the caller explicitly requests a staged workflow such as:

```text
implement only
-> developer reviews/runs checks
-> later request performs commit/PR
```

stop at the requested stage. In particular:

- do not run validation commands when the caller reserved validation for manual execution;
- do not contact external/live systems merely because later validation would normally do so;
- do not stage or commit if the caller reserved those steps; do not push, open a Pull Request, or wait on CI without an explicit request;
- still prepare the code/configuration/documented commands required for the developer's next manual step;
- report later workflow stages as pending rather than treating them as failed.

A later explicit request may resume from the verified working tree without restarting completed implementation work.

## Start safely

Use the repository's configured development branch, normally `develop`, as the base for new tasks.

Before editing:

1. Check the current branch and staged, unstaged, and untracked changes.
2. On `develop` or `main`, create/switch to a descriptive task branch (e.g. `feature/<topic>`, `fix/<topic>`, `chore/<topic>`, `docs/<topic>`) before editing. Prefer the configured development base.
3. On an existing non-base task/development branch, **continue on that branch**; do not create another branch or switch back to `develop` unless explicitly requested.
4. Preserve unrelated changes. If switching would overwrite work, stop and ask rather than forcibly checking out, stashing, resetting, rebasing, or discarding it.
5. If detached HEAD or the branch's purpose is ambiguous, ask before changing files.

Never perform normal task development directly on `develop` or `main`.

## Load only relevant context

Read project-local `AGENTS.md` first.

Read `ARCHITECTURE.md` only when the task affects architecture, persistence, service communication, infrastructure, deployment, or external integrations.

Inspect only relevant source, tests, interfaces, configuration, analogous implementations, and documentation.

For large repositories, continuations, noisy commands, large diffs, or optional delegated
execution workers, read [references/context-efficiency.md](./references/context-efficiency.md).

An execution worker is an optional capability, not a project dependency. When
`codex-free-worker-mcp` is available, prefer its `inspect_task` for bounded
high-output inspection/diagnostics and `fix_task` for unambiguous mechanical
diagnose/fix/rerun loops when delegation will materially reduce primary-model context.
Follow [references/context-efficiency.md](./references/context-efficiency.md) for routing
and safety boundaries. If that MCP is unavailable, continue directly without treating
that as an error or changing the project to install/configure it.

## Determine and implement scope

Before editing, identify:

- required behavior;
- affected components/files;
- existing tests;
- relevant project conventions;
- whether documentation changes were explicitly requested.

Avoid unrelated refactoring.

Implement every requirement in the requested scope and prefer existing patterns over parallel abstractions.

## Validation

When behavior, tests, build configuration, integration boundaries, or CI are affected, read [references/validation.md](./references/validation.md).

Default local sequence:

```text
implementation
-> safe deterministic fix
-> changed/new targeted checks
-> targeted corrections
-> make check once
-> make verify only when justified
```

Do not use the full CI pipeline as a normal debugging loop.

Do not repeat a successful check unless later changes could affect it.

## Documentation

Do **not** create, update, or refresh documentation as routine housekeeping. Documentation file changes require an explicit request (including a specific request to synchronize agent standards/instructions).

When requested, update documentation after implementation and necessary validation stabilize. Use the documentation skill for details.

## Final review

Start with compact status/diff metadata.

Do not dump a large full patch into context. Use targeted risk-area review when the patch is large.

For detailed context/diff rules, read [references/context-efficiency.md](./references/context-efficiency.md).

## Git delivery

When starting/continuing branches, committing, pushing, preparing PRs, or handling CI, read [references/git-delivery.md](./references/git-delivery.md).

Normal local endpoint:

```text
task branch -> validated changes -> local commit -> STOP
```

Stage only task-related files. **Do not push, create/update a Pull Request, or merge without a specific explicit user request.** An explicit request to create a PR also authorizes pushing its task branch, but not a merge. For requested remote delivery, target the configured development branch (normally `develop`).

## Completion criteria

For a normal task with local-only delivery, completion means:

- requested scope is implemented;
- required tests are updated;
- relevant targeted checks pass;
- the normal project check passes;
- extended verification was used when justified;
- final review is complete;
- documentation was changed only when explicitly requested;
- task changes are locally committed;
- push/PR/merge were not performed without explicit user authorization.

When the caller defines an earlier or later explicit stop point, complete only those authorized stages and report remaining delivery as pending.

Keep the final report concise; report results/status, not a chronological tool log.
