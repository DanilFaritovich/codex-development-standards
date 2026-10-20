---
name: pdf-documents
description: Create and verify professional, localized, downloadable PDF documents (including role-targeted resumes) with defensible content, accessible typography, clickable links and tested output. Use when building or revising real PDF artifacts, not for implementing a browser viewer.
---

# Professional PDF Documents

Create actual PDF deliverables, not image previews masquerading as documents. Prefer the target project's branding and documented content evidence over generic resume templates.

## Scope and evidence

- Inspect the requested document type, audience, locale, source material, approved visual system and file-delivery format before generation.
- Separate verified commercial work, freelance work, personal projects, education and aspirations. Never promote portfolio work to paid production experience.
- Do not invent achievements, metrics, dates, certifications, URLs or proficiency levels. Mark unknowns for the owner rather than silently filling them.
- If a metric is self-reported or approximate, preserve that qualifier. Keep claims supported by the current source documents.
- Exclude private IDs, account numbers, secrets and unapproved contact details. Confirm which contact channels belong in a publicly downloadable document.

## Versioning and localization

- For several role targets, adapt the summary, evidence order and technical emphasis to each role; do not produce identical PDFs with only the title changed.
- Maintain one factual source for overlapping experience across versions. Distinct emphasis must not create contradictory claims.
- Translate professional meaning and terminology, not word-for-word sentence lengths. Review line wraps separately in each language.
- Keep locale and role visible in output naming, for example `backend-en.pdf` and `backend-ru.pdf`.
- Do not claim translation quality or facts were manually verified when they were not.

## Visual production

- Establish page size, margins, usable content area, grid, hierarchy and expected page count before laying out text.
- Use accessible, readable text sizes; avoid fitting everything by shrinking body text indiscriminately.
- Prefer selectable embedded text to flattened page images. Use legally distributable fonts or font references permitted by the target environment; do not commit or redistribute proprietary font files without rights.
- Preserve a coherent brand (typography, palette, spacing and headings) without letting ornament reduce readability.
- Use genuine PDF link annotations for web, repository and contact destinations. Validate both URL targets and on-page hit regions.
- Keep headers, footers, page numbers and section spacing consistent across pages and translations.
- For generated documents, keep the source generator or reproducible generation instructions when the repository owns the artifact; do not modify owner-provided original PDFs unless specifically requested.

## Quality gates

1. Generate each requested output and check that every file exists, has a PDF signature, opens and contains the expected pages.
2. Extract text directly and spot-check the role, locale, contact details, section headings and completeness. OCR is not a normal validation technique.
3. Inspect rendered pages visually, including page breaks, line wrapping, margins, accidental overlap, excessive whitespace and visual alignment.
4. Enumerate PDF annotations, verify expected clickable URLs and identify missing or unsafe links.
5. Check file size and embedded resources; avoid unexpected blank pages and broken characters.
6. Where feasible, verify output on a second PDF reader. Report any reader/accessibility limits rather than implying certification.
7. If packaging a set, use clear deterministic filenames and validate archive membership and integrity.

## Delivery and reuse

- Deliver only verified files and their actual paths; never invent a downloadable artifact path.
- Report the document variants, page counts, link verification and any factual limitations concisely.
- Keep document creation separate from a site's PDF viewer. Use the `pdfjs-viewer` skill when the task is interactive on-page PDF rendering.
- Run the project's relevant checks, not every unrelated test suite merely because PDFs were generated.
