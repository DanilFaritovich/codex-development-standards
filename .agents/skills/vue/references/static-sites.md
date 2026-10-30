# Static Vue Sites and Prerendering

Read for Vue sites whose content is local or whose routes are generated as HTML at build time. Static deployment and rendering strategy are separate decisions: an interactive Vue site may use CSR, SSG or another rendering mode according to its discoverability needs.

## Content ownership

- Keep stable identities, catalog facts, URLs and asset metadata in typed data rather than duplicating them in components. Keep translated copy separate when it helps language parity; validate the shape without assuming a fixed project count or catalog taxonomy.
- Use actual configured destinations. An absent website, release or document must not produce a fabricated URL or a broken action. Choose how to communicate unavailable content according to the product.
- Render local content directly; do not wrap it in a pretend API or add a backend/store just to match a full-stack architecture. External data may still justify an API client.
- Preserve original source assets and licensing boundaries. Public availability is not permission to modify or reuse personal materials.

## SSG and browser boundaries

- Keep browser-only features out of imports evaluated during static generation. Access `window`, `document`, storage, canvas and worker-dependent libraries only in an appropriate client lifecycle; use lazy imports when the library itself requires browser globals.
- Create mutable router, locale and head state per application/render when the renderer creates multiple page instances. Shared immutable content is fine; a process-global current locale can leak one page's language into another.
- Start hydration from the same route, locale and content as the generated HTML. Apply browser preferences after the route's initial content is established; do not replace an explicit language URL using stored preference.
- Clean up event listeners, observers, pending work and feature resources on close/unmount. Guard late asynchronous results when navigation or a new resource makes earlier work stale.
- Keep state independent when the product requires it: a document-language selector need not change the interface locale, for example. This is not a requirement to add multiple language controls to every site.

## Navigation and assets

Use real links for destinations and explicit routes for directly loadable pages. For localized sites, switching language should preserve the equivalent page and useful query/hash or section context where supported. Back/Forward should restore coherent route and UI state.

Choose imported/fingerprinted assets or stable public paths according to the build and download contract. Verify generated references and worker URLs in the built output; a working development server does not prove nested routes or production asset loading work.

Use the existing `seo` skill for metadata, indexability and multilingual URL policy; it owns those rules rather than duplicating them here. Select `pdfjs-viewer` only for actual PDF.js work and `website-qa` when a browser acceptance audit is in scope.

## Focused validation

Test the failure modes introduced by the change: missing content/destinations, locale transitions, direct nested routes, hydration and asynchronous teardown where relevant. For prerendering changes, inspect built HTML before JavaScript and exercise the hydrated critical path. Build and verify with the actual static runtime when its route or asset behavior is affected.

Do not force unrelated browser suites, optional skill installation or a rendering-framework migration for a simple copy/layout edit.
