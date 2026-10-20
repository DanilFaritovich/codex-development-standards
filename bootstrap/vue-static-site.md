# Bootstrap a Static Vue Site

Source: https://github.com/DanilFaritovich/codex-development-standards

Use this guide when the developer requests initialization of a Vue website deployed as static HTML/CSS/JavaScript assets: a landing page, portfolio, documentation site, product site or similar client-side application. Static deployment does not imply a non-interactive page: Vue, local state, localization, modals and optional calls to external APIs still work. A dedicated application backend and request-time server rendering are not part of this default profile.

## Initialization scope

1. Inspect the target repository and real project requirements. If designs are present, inspect the relevant PNG/SVG/Figma exports without dumping full vector path data.
2. Read `catalog.yaml` first, then `profiles/vue-static-site.yaml` and only the selected core skills.
3. Install the complete local skill packages listed by the profile, including bundled `references/` assets. Leave optional Docker/CD skills for the relevant phase unless explicitly requested now.
4. Record the exact upstream commit and installed skill names in `.codex-standards.lock.yaml`.
5. Create a concise project-local `AGENTS.md`. Create `ARCHITECTURE.md` and `design/DESIGN.md` only when requested, distinguishing planned architecture from features already implemented.
6. Where a visual design exists, place canonical assets under `design/reference/`; do not store redundant large archives and duplicate exports or turn vector screenshots into runtime page markup. If there are no design references, skip this step.
7. For a standards-only bootstrap, do **not** scaffold Vue, install application dependencies, or configure Docker/deployment. Those belong to later stages.
8. Validate installed files and references using targeted checks. Do not claim a frontend build passed before an application exists.
9. Install the core `seo` skill with the profile. Record project-specific indexable routes, metadata strategy and the selected rendering approach in the requested `ARCHITECTURE.md`. A standards-only bootstrap decides/document these requirements but does not yet implement metadata, SSG or a sitemap.
10. For multilingual sites, define independent language URLs (e.g. `/en/` and `/ru/`), route-to-locale mapping, per-language canonical and reciprocal hreflang, and built HTML expectations. Do not impose multilingual routes on monolingual sites.
11. If search engines or social preview crawlers need meaningful initial HTML, consider SSG/prerendering before deciding on client-only rendering. For server-rendered or backend-centric apps, adapt the profile or select a more appropriate one.

## Project-specific delivery authorization

The default `task-development-workflow` requires an explicit user request to push and create PRs. A developer can give **standing permission for one target repository**. Record that permission in the target project's `AGENTS.md`, not in the global default rules.

For example, ProjectRouter has owner-approved automated delivery:

> For normal completed ProjectRouter tasks: create a task branch from `develop`; apply safe fixes and run targeted checks; make a **local Git commit**; push that task branch; and create a Pull Request targeting `develop` using authenticated `gh`. No separate approval is needed for push/PR within this standing instruction. **Never merge** without separate permission. Respect more restrictive per-task stop points.

For other repositories, do not assume this permission exists.

Before PR delivery:
- Confirm `gh auth status`, repository and target branches without exposing tokens.
- Review diff/check results; do not commit secrets, caches or unrelated files.
- Run `gh pr create --base develop --head <task-branch>` only with authorization.
- Report the PR URL and check summary. Stop before merge.
- If GitHub permissions fail, report the blocker; do not tamper with stored credentials or bypass protections.

## Suggested subsequent phases (adapt to the site)

1. Vue 3 / TypeScript / Vite foundation, optional Vue I18n, select CSR vs SSG/prerender based on SEO needs, Make targets and initial CI.
2. Responsive sections/layout and components based on available designs.
3. Typed content/catalog and conditional action links.
4. Localization, per-route SEO metadata and crawlable language paths where applicable, forms/media or other required interactions.
5. Built-HTML SEO checks, sitemap/robots validation, browser visual QA, accessibility, useful unit/component and critical E2E coverage.
6. Install optional `docker` / `continuous-delivery` for production packaging and delivery only when in scope. Actual deployment to live systems requires separate authorization.

Install optional `website-qa` for a full browser/owner acceptance audit, `pdfjs-viewer` for an actual embedded PDF.js viewer, and `pdf-documents` only for authoring PDF artifacts. Docker's static-site and CD's image-producer references apply when the site is packaged into an image consumed by an independently managed deployment controller. Do not install every optional skill during bootstrap.

Read only relevant skills and references. Prefer `rg`, bounded logs, targeted tests and `make fix -> targeted checks -> make check`, with full CI in GitHub rather than repeated local runs.
