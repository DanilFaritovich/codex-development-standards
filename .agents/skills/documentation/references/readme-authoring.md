# Writing a Useful README

Use this reference for an explicitly requested README creation, revision or review. Preserve the project's language mode and scope; updating the README does not authorize editing application content, regenerating assets or deploying the site.

## Start with the reader's questions

Explain the actual project before its implementation process:

- **Users:** what can I do with it, and where can I open or run it?
- **Developers:** what are its main technologies, important trade-offs and shortest reproducible setup/check path?
- **HR and technical reviewers, for portfolio work:** what problem was addressed, what was implemented and what does it demonstrate?

These are questions to answer naturally, not mandatory audience-specific headings. Describe a product's value in plain language before introducing architecture terms. Separate commercial work from personal demonstrations where that distinction matters. Describe the author's contribution only from supplied or verified facts; do not infer sole authorship, production usage, seniority or business impact from a repository.

## First screen

Prefer a short purpose paragraph and one clear next action. For an existing public site, make its verified URL a standalone descriptive link immediately after the introduction, before technology badges. For example:

```markdown
**[Open the website → <confirmed host>](<confirmed website URL>)**
```

Replace placeholders with actual supplied/verified values; do not publish placeholders. Use the relevant language URL when the site is localized. If there is no public site, give the useful local-start or documentation path instead; do not invent a demo.

If the owner provides a personal portfolio URL and wants it included, label it as the author's website/portfolio. Keep it distinct from the application's website or demo, and do not copy an example author's domain into another project.

Keep language-switch links visible. Badges may summarize the principal technologies, CI and license where useful, but should not overwhelm the introduction. Use real workflow links and repository dependency versions; a CI badge is not a statement about deployment, release approval or uptime.

## Suggested shape, not a fixed template

Choose the parts that help this project:

1. Purpose and prominent website/demo link when available.
2. A compact set of capabilities and outcomes.
3. Technologies grouped by responsibility, usually a small table.
4. The few engineering choices whose rationale matters.
5. Requirements, installation and a working quick start.
6. Relevant validation commands.
7. A short runtime/delivery overview when applicable.
8. Material limitations, deeper documentation and license.

Combine overlapping features/benefits/demonstration sections. Do not fill every heading, impose a target line count or force frontend, API, Docker or database sections onto projects that do not have them.

The [ProjectRouter README](https://github.com/DanilFaritovich/project-router/tree/main) illustrates a compact bilingual presentation with a visible website link and grouped technology table. Treat it as a structural example; it is not a prerequisite to fetch on every task or a fixed specification for another project.

## Make technologies and decisions informative

Derive the technology list from manifests, configuration and implementation. Group by actual responsibility: interface, API, storage, data processing, testing, runtime or delivery. Omit inapplicable groups. Avoid copying every transitive dependency or pinning badge patch versions that add maintenance without useful context.

Use engineering decisions to explain **why** a choice addresses a project constraint. Do not repeat the technology table in prose. Link to the architecture document for module inventories and lifecycle details. State outcomes supported by evidence; absence of metrics is preferable to invented percentages, traffic, performance or time savings.

## Keep detail in the right place

- Keep enough configuration and commands to reproduce the advertised quick start. Do not shorten away a required prerequisite, environment setting or migration step.
- Link detailed container signatures, image digests, routing aliases, recovery and release procedures from their maintained operational documentation. Keep a consequential trigger such as main-merge deployment visible when it affects contributors.
- Put completed roadmaps, PR numbers, acceptance dates and implementation history in changelogs, release notes or QA reports. Mention status briefly only when it changes how someone can use or evaluate the project.
- Use current screenshots only when they materially improve understanding. A live site often makes a large screenshot gallery redundant. Preserve historical QA artifacts and their provenance; do not present them as current or delete them merely to simplify README.
- Keep licensing distinctions for code, personal materials, datasets or branding. A concise README must not imply broader reuse rights.

Do not create new documentation files simply to relocate paragraphs unless that supporting documentation is in the authorized scope. Prefer existing destinations; do not remove the only useful operational instruction and replace it with a link that does not contain it.

## Review before delivery

Check local links/anchors, relevant external destinations and the existence of documented commands. Distinguish commands merely checked against configuration from commands actually executed. Verify technology names/versions and align EN/RU meaning, destinations and important caveats.

Review the first screen as a newcomer: purpose, next action and project value should be clear without understanding the author's internal workflow. Remove duplicate claims, stale screenshots and unexplained internal status language. Run relevant documentation formatting/link checks; do not add application tests or repeat unrelated suites solely for prose edits.
