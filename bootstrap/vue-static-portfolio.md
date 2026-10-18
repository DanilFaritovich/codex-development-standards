# Bootstrap a Static Vue Portfolio

Source: https://github.com/DanilFaritovich/codex-development-standards

Use this guide only when the developer explicitly requests initialization of a static Vue portfolio.

## Initialization scope

1. Inspect the target repository and its actual design source without dumping full SVG vector path data.
2. Read `catalog.yaml` first, then `profiles/vue-static-portfolio.yaml` and only the selected core skills.
3. Install the complete local skill packages listed by the profile, including bundled `references/` assets. Leave optional Docker/CD skills for the relevant phase, unless explicitly requested now.
4. Record the exact upstream commit and installed skill names in `.codex-standards.lock.yaml`.
5. Create a concise project-local `AGENTS.md` as the stable entry point. Create `ARCHITECTURE.md` and `design/DESIGN.md` only when requested, distinguishing target design from implemented features.
6. Keep imported Figma/SVG/PNG assets in `design/reference/`, with a small set of canonical files rather than duplicated archive copies. Design exports are references, not runtime UI components.
7. On a standards-only bootstrap, do **not** scaffold Vue, install application dependencies, or start Docker/deployment. Those belong to later feature stages.
8. Validate installed files and internal references using small, targeted checks; do not claim a frontend build passes before one exists.

## Project-specific delivery authorization

The **global** task workflow requires explicit permission to push or create a PR. If, as with ProjectRouter, the owner gives standing authorization for this particular repository, record it in project-local `AGENTS.md`:

> For normal completed ProjectRouter tasks, create a task branch from `develop`, perform safe local fixes and checks, make a **local Git commit**, push that task branch and create a Pull Request targeting `develop` using an authenticated `gh` CLI. No extra approval is required for push/PR as covered by the owner's standing instruction. **Never merge** without separate explicit authorization. Respect any narrower per-task stop point.

This is a project-specific owner decision, **not a change to the default PR policy for other repositories**.

Before PR delivery:

- Confirm `gh auth status` and the target repository/branch (avoid printing token values).
- Confirm the diff and checks; do not commit secrets, generated caches or unrelated work.
- Use `gh pr create --base develop --head <task-branch>` for the approved task.
- If GitHub auth or a necessary permission fails, report the blocker; do not rewrite shell credentials or silently bypass protection.
- Record PR URL and brief validation results; stop before merge.

## Subsequent development stages

1. Vue 3 / TypeScript / Vite / Vue I18n foundation, quality Make targets and initial CI.
2. Responsive layout from the design references.
3. Typed catalog, project/tool cards and conditional links.
4. Translation coverage and PDF resume interactions.
5. Browser visual QA, accessibility and critical E2E.
6. Install the optional `docker` and `continuous-delivery` skills for production packaging/CD; configure Caddy/VPS only in a separately authorized deployment stage.

Load task-specific skills and references only when used. Use `rg`, bounded output, targeted tests and `make fix -> targeted checks -> make check`; do not repeatedly run full CI locally.
