# Git, Documentation, and Delivery Reference

Read this reference when starting/continuing a task branch, committing, or (only when explicitly requested) changing documentation, pushing, or creating a Pull Request.

## Branch setup

Use the repository's configured development branch, normally `develop`.

Before editing:

1. Check the current branch and working tree, including staged, unstaged, and untracked files.
2. On `develop` or `main`, create and switch to a descriptive task branch before editing. Prefer the configured development base (normally `develop`).
3. On an existing non-base development/task branch, **continue there**; do not create another branch or switch to the base just because a new task started.
4. Preserve unrelated changes. If checkout would overwrite work, stop and ask; never force-switch, stash, reset, rebase, or discard without authorization.
5. On detached HEAD or ambiguous/unrelated branch, ask before modifying files.

Typical prefixes:

- `feature/`;
- `fix/`;
- `refactor/`;
- `chore/`;
- `docs/`.

Do not perform normal task development directly in `develop` or `main`.

## Documentation timing

Update documentation **only if explicitly requested**, after implementation and validation are stable. Never treat automatic documentation synchronization as routine feature/fix cleanup.

Potential files include:

- `AGENTS.md`;
- `ARCHITECTURE.md`;
- root/component READMEs;
- API docs;
- code docstrings.

Do not continuously rewrite documentation while implementation is changing.

## Commit planning

Before creating commits, inspect the complete task diff using compact Git metadata and identify logically independent change groups.

Prefer atomic commits that are independently understandable and revertable.

Examples of distinct concerns that often deserve separate commits:

- application feature/behavior changes;
- logging/observability infrastructure;
- tests;
- Docker/deployment infrastructure;
- CI/tooling;
- documentation;
- project-local standards synchronization.

Do not split mechanically by file. Files that implement one logical change across several layers should remain in one commit.

Do not combine independently meaningful changes merely because they were requested in the same task or Pull Request.

Before each commit:

1. stage only the files/hunks for one coherent change;
2. inspect at minimum `git diff --cached --stat` and `git diff --cached --name-status`;
3. inspect the staged patch when needed to verify the logical boundary;
4. use a commit message that describes the actual staged change rather than only the overall task/PR.

If one commit intentionally contains several tightly coupled changes, its message must describe that combined scope.

## Commit and push

Stage only files belonging to the task and the current logical commit.

Never include:

- secrets;
- local environment files;
- unrelated untracked files;
- accidental generated artifacts.

Create the planned logical commits **locally and stop**. Do not push any branch, open/update a PR, or merge by default.

Only explicit user authorization permits remote publication. A direct request to create a PR also authorizes the necessary task-branch push, but not merging.

## Push authentication failures

During explicitly authorized remote delivery only, if `git push` fails because authentication or credentials are unavailable:

- do not retry the same push repeatedly;
- do not automatically switch the remote between HTTPS and SSH;
- do not start a new interactive login unless the user explicitly requests it;
- preserve the local branch and commit;
- report the credential/environment failure clearly and stop the delivery step.

A credential failure does not invalidate the completed local implementation or commit.

After credentials are fixed by the developer or environment, retry the push once from the same branch.

## Pull Request and CI

Default flow:

```text
task branch -> local commit -> STOP
```

When explicitly requested, push/open a PR against the configured development branch (normally `develop`); GitHub Actions then performs independent CI. Do not create or update a PR just because local checks passed.

If CI fails:

1. inspect only the relevant failure;
2. reproduce it locally with the smallest practical command when possible;
3. make a targeted fix;
4. run targeted validation;
5. run the affected fast aggregate check if necessary;
6. push and let CI rerun.

Do not ingest unrelated successful CI logs.

### Optional delegated GitHub/CI inspection

When an optional execution worker is available, it may gather and compress noisy remote
delivery context such as:

- Pull Request metadata and changed-file summaries;
- review comments or unresolved review findings;
- GitHub Actions/check status;
- relevant failing CI logs;
- other read-only GitHub evidence needed to prepare a delivery/merge review.

The worker should return a compact result and must not own Git delivery or merge.

Before any actual merge, the primary Codex model must independently verify the
merge-critical current state from an authoritative GitHub source, including the target
Pull Request, required CI/check status, and mergeability. Do not merge solely from a
worker summary that may be stale or incomplete.

Git staging, commits, branch/ref mutation, push, Pull Request mutation, and merge remain
primary-model responsibilities unless a narrower project rule explicitly permits an
action. The merge boundary below still applies.

## Merge boundary

Codex must not automatically merge the Pull Request.

Responsibility:

```text
Codex by default:
analyze -> implement -> validate -> local commit -> STOP

Codex only on explicit request:
push / Pull Request / CI inspection

Developer:
review -> merge (unless separately authorized)
```

Merge only when the user explicitly requests that separate action.

## Completion report

Keep the final report concise.

Include:

- what changed;
- tests/checks performed;
- whether extended verification was needed;
- requested documentation changes (or confirmation none);
- branch/local commit;
- push/PR/CI status, including steps intentionally left to the developer;
- remaining limitations.

Do not include long successful logs or a chronological list of tool calls.
