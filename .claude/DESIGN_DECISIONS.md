# Design Decisions

This document captures design decisions made during development of the NGINX Ingress Migration Guide. Reference this when making changes to understand why things are structured the way they are.

## Core Architecture

1. **Inline CRD examples** — CRD examples are expandable table rows, not a separate section
2. **Grouped annotations** — Related annotations (e.g., all `session-cookie-*`) grouped into single expandable rows
3. **CRD install instructions** — Each CRD dropdown includes `kubectl apply` commands specific to that CRD
4. **OSS/Plus split** — Mappings split into separate sections to distinguish subscription requirements
5. **Alphabetical ordering** — Categories within sections and annotations within rows sorted alphabetically
6. **"How to Read" guide** — Intro box explains row types (annotation-to-annotation, CRD-based, grouped, official-only)
7. **Every row expandable** — All rows have examples: annotation rows show before/after YAML, CRD rows show install-box + YAML
8. **Search behavior** — Hides entire categories when no rows match, collapses expanded rows during search

## Navigation & Links

9. **GitHub contribution links** — Present in header, Additional Resources, and footer
10. **Anchor IDs** — All `<h2>` and `<h3>` headings have `id` attributes with hover-to-reveal permalink icons
11. **Active nav highlighting** — `IntersectionObserver` marks current section's nav link as `.active`

## Badge System

12. **Badge flairs** mark mapping types:
    - `Annotation` (teal), `ConfigMap` (yellow)
    - `VirtualServer CRD` (blue), `VirtualServerRoute CRD` (indigo)
    - `Policy CRD` (purple), `TransportServer CRD` (orange)
    - `GlobalConfiguration CRD` (brown), `Plus Required` (orange)
    - CRD badge text includes "CRD" suffix

## Table Layout

13. **Full annotation prefix** — Community annotations display full `nginx.ingress.kubernetes.io/` prefix
14. **50% columns** — Mapping table columns use `width: 50%; white-space: nowrap`
15. **Route Delegation category** — Added to OSS Mappings for VirtualServerRoute CRD (N/A on community side)

## YAML Analyzer (v7+)

16. **Position** — First section, before Key Differences
17. **Annotation database** — `ANNOTATION_MAPPINGS` array (52 entries) with `ANNOTATION_LOOKUP` Map for O(1) access
18. **YAML parser** — Line-based (no external library), handles multi-document YAML, inline annotations, quotes, comments
19. **Safe rendering** — DOM methods (`createElement`/`textContent`) only, no `innerHTML`
20. **Summary pills** — Show annotation count, migration paths, CRD requirements, unrecognized counts
21. **Section linking** — "View full migration example" scrolls to anchor with 2-second yellow highlight

## Stepped Migration Output (v8+)

22. **Three steps**: (1) Annotation Swaps with before/after comparison, (2) ConfigMap Changes, (3) CRD Resources grouped by kind
23. **Transform types** in `officialMapping`: `direct`, `booleanInvert`, `appendRateUnit`, `appendTimeUnit`, `appendBufferSize`, `snippetWrap`, `corsSnippet`, `proxyRedirect`
24. **CRD generators** — 13+ functions produce valid YAML from user values
25. **Dual-approach entries** — Appear in both Step 1 and Step 3
26. **Side-by-side comparison** — `.analyzer-comparison` grid with diff-highlighted annotation lines

## YAML Syntax Highlighting (v8+)

27. **`highlightYaml()` function** — Line-by-line parsing with CSS classes: `.yaml-key` (blue), `.yaml-value` (orange), `.yaml-comment` (green italic), `.yaml-keyword` (blue bold), `.yaml-bool` (blue), `.yaml-number` (green), `.yaml-separator` (gray)

## Dark Mode (v8+)

28. **Toggle** — Header button with sun/moon SVG icon swap
29. **Implementation** — `body.dark-mode` class with CSS custom property overrides
30. **Persistence** — `localStorage.setItem('darkMode')`

## UX Features (v8+)

31. **Drag-and-drop** — `#dropZone` accepts `.yaml`, `.yml`, `.txt` files
32. **Live input status** — Line count + annotation count below textarea
33. **Keyboard shortcuts** — Ctrl+Enter analyzes, Tab inserts 2 spaces
34. **Annotation tooltips** — `ANNOTATION_TIPS` object (50+ entries) with CSS-only `::after` tooltips
35. **Complexity indicator** — Simple (1 green dot), Moderate (2 yellow), Advanced (3 red)
36. **Collapsible YAML** — Blocks >12 lines start collapsed with gradient fade
37. **Export actions** — "Copy All" aggregates steps, "Download" creates `.yaml` file with comments
38. **What's Next?** — Contextual links at bottom of results
39. **Edit & Re-Analyze** — Button scrolls back to textarea and focuses it
40. **Empty state** — File icon SVG with hints when no results
41. **Fade-in animations** — `@keyframes analyzerFadeIn` with staggered delays
42. **Nav badge** — Shows annotation count after analysis

## Print & Responsive (v8+)

43. **Print styles** — `@media print` hides interactive elements, light backgrounds
44. **Mobile layout** — `@media (max-width: 600px)` stacks buttons, adjusts fonts

## v9 Updates

45. **CRD version** — Updated to v5.3.2 (single `deploy/crds.yaml`)
46. **Sample presets dropdown** — Simple, Moderate, Advanced complexity levels
47. **Scroll-to-top button** — Appears after scrolling 400px
48. **Accessibility** — Skip link, ARIA roles/labels, keyboard-accessible dropdowns and expandable rows, `rel="noopener noreferrer"` on external links
49. **Fixed duplicate IDs** — `<h2>` IDs renamed to `*-heading` while section IDs unchanged
50. **Clipboard fallback** — `document.execCommand('copy')` for older browsers
51. **Error handling** — `analyzeYaml()` wrapped in try-catch
52. **SEO meta tags** — description, keywords, author

## v10 Updates (Accuracy Review)

53. **CRD URLs fixed** — All individual CRD file paths (`deploy/crds/k8s.nginx.org_*.yaml`) replaced with correct combined `deploy/crds.yaml`; note added that individual CRD files are not provided separately
54. **SSL redirect corrected** — Option 2 changed from deprecated `ingress.kubernetes.io/ssl-redirect` to `nginx.org/ssl-redirect` with deprecation notice
55. **proxy-request-buffering** — Changed from non-existent ConfigMap key to `nginx.org/location-snippets` with `proxy_request_buffering` directive
56. **http2-push-preload** — Marked as "Not supported" since HTTP/2 Server Push was removed from NGINX 1.25.1
57. **rewrite-log** — Changed from non-existent ConfigMap key to `nginx.org/location-snippets` with `rewrite_log` directive
58. **OpenTelemetry** — Changed from vague "See docs" to specific `otel-*` ConfigMap keys with example
59. **backend-protocol analyzer** — Added `backendProtocol` transform type that selects `nginx.org/grpc-services` for GRPC/GRPCS values, `nginx.org/ssl-services` otherwise
60. **Missing official annotations** — Added listen-ports, listen-ports-ssl, mergeable-ingress-type, proxy-set-headers, http-redirect-code to Miscellaneous section
61. **Additional context boxes** — Added info boxes for rate limiting annotations, WAF `appprotect.f5.com/` annotations, JWT annotation-based approach, OIDC native support, session affinity `sticky-cookie-services`, default lb-method `random two least_conn`, and rewrite-target/app-root annotations

## v10 Updates (UX Review)

62. **Copy All clipboard fallback** — Copy All Migration YAML button now uses `fallbackCopy()` when `navigator.clipboard` is unavailable
63. **Plus section search + controls** — Added `searchInputPlus` input, Expand All, and Collapse All buttons to Plus Mappings section; synced with OSS search input
64. **Expandable official-only rows** — Four plain `<tr>` rows (listen-ports, mergeable-ingress-type, proxy-set-headers, http-redirect-code) converted to expandable rows with before/after examples
65. **Escape key releases textarea focus** — Keyboard users can press Escape to blur the YAML textarea (fixes Tab key trap)
66. **Tooltip accessibility** — Added `:focus` trigger to `annotation-tooltip` CSS, plus `title` and `tabindex="0"` attributes for screen reader and keyboard access
67. **Badge contrast (WCAG AA)** — Darkened text colors for `.badge-configmap` (#8c6d00), `.badge-plus` (#bf360c), `.badge-transportserver` (#bf4000), `.analyzer-pill.crds` (#bf4000)
68. **Arrow key tab navigation** — Approach tabs now support Left/Right and Up/Down arrow keys per WAI-ARIA tabs pattern
69. **td:first-child rendering** — Changed from `display: flex` to `position: relative` + absolute `::before` for the expand arrow, avoiding layout issues on table cells
70. **Search result count** — `filterTable()` now shows "X of Y rows" count via `#searchCount` / `#searchCountPlus` spans
71. **Expanded row state preservation** — Search saves expanded row state and restores it when search is cleared
72. **Plus section N/A styling** — Changed `<code>No direct equivalent</code>` to `<em>No direct equivalent</em>` in Plus Mappings for semantic consistency
73. **Sample dropdown mobile clipping** — Added `max-width: calc(100vw - 40px)` to `.sample-dropdown-menu`
74. **Header inline styles extracted** — Replaced inline `style` on header divs with `.header-layout` and `.header-actions` CSS classes
75. **Scoped Expand/Collapse** — `expandAllExamples(btn)` and `collapseAllExamples(btn)` now scope to the nearest `<section>` via `btn.closest('section')`
76. **External link rel attribute** — Added `rel="noopener noreferrer"` to dynamically created external links in "What's Next?" section
77. **Print stylesheet expansion** — Added `tr.example-row { display: table-row !important; }` to print media query so all examples are visible when printed
78. **aria-live scoping** — Moved `aria-live="polite"` from entire `#analyzerResults` region to a dedicated `#analyzerLiveStatus` `.sr-only` element with screen-reader-only summary text
