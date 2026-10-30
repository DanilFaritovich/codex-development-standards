---
name: pdfjs-viewer
description: Build and verify secure, responsive PDF.js document previews with lazy loading, canvas rendering, accessible navigation, safe clickable annotations and deterministic race-condition tests. Use for embedded PDF preview UX, not PDF authoring.
---

# PDF.js Viewer Standard

Use an embedded PDF viewer only when users need on-page preview. A direct download or native-browser PDF link may be enough for simpler tasks.

## Document contract

- Keep document identity, language, URL and actual availability in a single typed metadata source; avoid scattered filename assembly inside UI components.
- A preview, its download action and its open-original fallback must refer to the same selected file.
- Support distinct locale choices when users need document language independent from the site language; initialize from locale without silently switching later.
- Do not fabricate files, pages, download endpoints or successful loads.

## Runtime and rendering

- Lazy-load PDF.js and its compatible worker only when needed. For SSG/SSR, defer browser-only canvas/worker APIs until the client mounts.
- Use the installed PDF.js version's documented APIs, bundling a local compatible worker rather than relying on a third-party CDN.
- Render with a PDF page viewport and explicit device-pixel ratio. Bound canvas memory usage at large zoom/high-density displays.
- Compute initial or fit-width scale from the *actual viewer content box* (including padding/gutters). Avoid initial horizontal scroll; manual zoom may scroll.
- Center the page without clipping; keep zoom, fit, page navigation and viewport resize behavior consistent. Preserve deliberate manual zoom on resize unless product requirements differ.
- Manage loading, ready, error and retry states. Keep page count and disabled navigation controls honest.

## Link annotations and security

- Canvas is not interactive PDF text. For clickable PDF URLs, render PDF.js link annotations aligned to the same CSS-pixel viewport as the canvas.
- Admit only intended URL Link annotations. Explicitly allowlist URL schemes (normally HTTP(S) and mailto); reject script actions, attachments, forms and unsafe schemes.
- Use safe new-tab attributes for external HTTP(S) links; make native anchors keyboard-operable with visible focus and meaningful names.
- Do not import the entire full-featured PDF viewer merely to support links. Full Text Layer, search and selection are separate optional requirements.
- Recheck annotation positioning after page, locale, zoom, fit-width and viewport changes. Small link rectangles may have WCAG inline-link exceptions; document narrowly scoped exceptions, never disable a whole accessibility audit.

## Async lifecycle

- Treat page renders and document switches as cancelable generations; stale loads/renders/annotations must not overwrite the current selection.
- Destroy the loading task, render task, PDF resources and annotation layer when replaced or unmounted.
- Avoid leaving focused or invisible links in stale layers. Restore focus to an appropriate control before removing focused annotations.
- A native modal should close on Escape, contain focus, prevent unwanted background scrolling and restore focus to its opener on close.
- Do not leak observers, handlers or scroll locks when a user closes during an in-flight request.

## Verification

- Use real representative PDFs in browser tests, not only mocks. Confirm both pages, content-bearing canvas, locale, file bytes, download name and original-file fallback.
- Compare each rendered URL and bounding rectangle with the PDF's source annotations at the selected viewport scale.
- Test pointer and keyboard activation while intercepting external destinations; never send actual contact messages from E2E.
- Exercise failed requests/retry, close-while-loading, quick language switches, page changes, zoom and resize.
- Use condition-based awaits (`aria-busy`, visible canvas, expected link count/geometry), never arbitrary sleeps or timeout inflation to hide races.
- Test narrow/mobile, tablet and desktop widths, and both locales when present.
- Distinguish a named canvas and clickable links from a fully accessible text-reading experience; provide an original-file fallback and record screen-reader limitations.

## Boundaries

- Preserve original PDF bytes unless the user explicitly requested edits.
- Keep the viewer component independent of global SEO, domain routing and production deployment.
- Prefer targeted tests during iteration and one complete project verification before delivery; capture real evidence and report known limitations.
