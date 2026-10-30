---
name: website-qa
description: Plan and perform evidence-based functional, visual, responsive, multilingual, accessibility and SEO/SSG QA of websites using real browser interactions. Use for release-readiness audits and owner acceptance, not automatic redesign.
---

# Website Testing and Visual QA

An automated green test suite is not visual acceptance. Verify actual user journeys in a real browser and clearly distinguish automated checks, agent-inspected screenshots, owner review and production-only checks.

## Establish the audit boundary

- Read the project's current product structure, design references, deployed/SSG model, existing tests and known constraints. Do not start by rewriting all tests.
- Establish a baseline: commit SHA, runtime versions, current check results and configured origin. Record failures present before changes.
- Define a viewport-by-locale matrix covering a genuinely narrow mobile size, typical mobile, tablet/intermediate and desktop; adapt exact widths to the product.
- For multilingual sites inspect both languages, especially long translations and section headings.
- Build/serve the actual production output locally when possible, not only the dev server.

## Functional browser walkthrough

- Visit every key section and important user journey; click primary navigation, contact and real project links using browser automation.
- Verify direct entry, refresh, anchor/query preservation, Back/Forward, language switches, responsive menus, focus return and modal interactions.
- Check optional media/actions: no invented links, empty buttons, missing images, fake releases or silent error states.
- Cover document previews/downloads, failures, retries and fast repeated interactions when present; use the `pdfjs-viewer` skill for PDF.js-specific assertions.
- Intercept external URLs; inspect destinations and new-tab safety without relying on third-party network availability or sending messages.

## Visual and responsive inspection

- Compare actual rendered screenshots with the relevant approved references and working design tokens; do not demand pixel-perfect equivalence when content has evolved.
- Inspect real screenshots rather than claiming that generating files constitutes a review.
- Detect horizontal overflow and *element-to-element overlap*. A page may have no overflow while long headings are covered by neighboring cards; inspect text ranges and bounding boxes.
- Check legibility, line wrapping, image proportions, modal/toolbars, section spacing, focus/hover and disabled states across viewports.
- Record cosmetic preferences separately from confirmed usability defects. Do not make a broad design overhaul during a QA-only task.

## Accessibility and SEO

- Exercise keyboard navigation (Tab, Shift+Tab, Enter, Escape), skip links, menus and modal focus/scroll management.
- Use semantic/ARIA checks and an automated axe-style WCAG audit where practical; investigate findings individually instead of disabling broad rules.
- Treat automated accessibility results as partial evidence, not certification or a substitute for screen-reader/physical-device review.
- For indexable SSG pages, inspect built HTML and actual HTTP statuses: localized entry, title/meta, canonical/hreflang, OG, JSON-LD, robots/sitemap and real 404.
- A test-only origin can verify generation; it is not proof of live HTTPS, public crawlability, indexing or Core Web Vitals.
- Performance metrics must name the tool/environment; never invent Lighthouse scores or claim field CWV from unthrottled localhost.

## Deterministic testing

- Reuse existing unit/component/build/E2E checks and add focused regressions for uncovered product failures.
- Wait on observable UI readiness, network conditions or state predicates. Do not use arbitrary sleeps, broad timeout increases or unconditional test retries to conceal races.
- If a scenario is flaky, reproduce in isolation, diagnose the cause, and preserve the original assertion strength.
- Keep browser matrices and large screenshots out of Git unless review artifacts were explicitly requested; prefer a small, optimized representative selection.
- Use the project's Makefile and compact logs; only broaden failure diagnostics when needed.

## Report and handoff

- Classify P0 (critical), P1 (broken user journey or accessibility), P2 (cosmetic) with reproductions and fix evidence.
- Fix confirmed in-scope defects and add regression coverage. Escalate architectural redesign or speculative polish rather than silently broadening scope.
- Report exact commands/results, viewport × locale coverage, inspected screenshots, exclusions, production-only checks, and remaining issues.
- Give the owner a short manual acceptance checklist focusing on subjective visual judgment, physical devices, screen readers and native browser behavior.
- A passing CI/PR does not authorize merge, release or production deployment. Keep owner acceptance distinct from technical QA.
