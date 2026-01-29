# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **documentation-only** project providing a migration guide from the Kubernetes community NGINX Ingress Controller ([`kubernetes/ingress-nginx`](https://github.com/kubernetes/ingress-nginx)) to the Official NGINX Ingress Controller ([`nginx/kubernetes-ingress`](https://github.com/nginx/kubernetes-ingress)). It is owned by F5, Inc. and licensed under Apache 2.0.

There is no build system, test framework, or package manager. The project consists entirely of Markdown documentation and a self-contained HTML page.

## Hosting

- **Repository**: https://github.com/alessfg/nginx-ingress-migration-guide
- **GitHub Pages**: https://alessfg.github.io/nginx-ingress-migration-guide/
- GitHub Pages serves from the `main` branch root (`/`), rendering `index.html` (the latest version, currently v5)

## Key Files

- `index.html` — **The live interactive guide** served by GitHub Pages. Self-contained HTML (no external dependencies, no build step). Currently equivalent to v5.
- `nginx-ingress-migration-guide.md` — The original Markdown guide (~2,170 lines) with annotation mapping tables and CRD examples. Serves as reference material.
- `nginx-ingress-migration-guide.html` — v1: Separate expandable CRD examples section with search.
- `nginx-ingress-migration-guide-v2.html` — v2: Inline expandable table rows combining mappings and CRD examples.
- `nginx-ingress-migration-guide-v3.html` — v3: Grouped related annotations into single expandable rows; added CRD install instructions per dropdown.
- `nginx-ingress-migration-guide-v4.html` — v4: Split into OSS/Plus sections; alphabetical ordering; expanded CRD Features Summary; "How to Read the Mappings" guide.
- `nginx-ingress-migration-guide-v5.html` — v5: All annotations from markdown guide; expandable examples for every row; dual Annotation/CRD tabs for rewrites, rate limiting, basic auth; improved search filtering that hides entire categories; GitHub contribution link.

## Key Domain Concepts

- **Annotation prefix difference**: Community uses `nginx.ingress.kubernetes.io/`, Official uses `nginx.org/` (open source) or `nginx.com/` (NGINX Plus)
- **CRD support**: The Official controller supports VirtualServer, VirtualServerRoute, Policy, TransportServer, and GlobalConfiguration CRDs
- **NGINX Plus**: Only the Official controller supports NGINX Plus features (session affinity, JWT, OIDC, API key auth, WAF)
- **Naming**: Do not refer to "NGINX Inc." — use "Official NGINX" or "Official NGINX Ingress Controller"
- **Mapping types**:
  - Annotation-to-annotation mappings (expandable rows with before/after Ingress YAML)
  - CRD-based mappings where multiple community annotations consolidate into a single CRD (expandable rows with install-box)
  - Dual-approach mappings where an annotation maps to either an annotation OR a CRD (expandable rows with tabbed interface)
  - Plus-only features with no community equivalent
  - Official-only features with no community equivalent (marked N/A on left column)

## HTML Structure and Conventions

The HTML guide (`index.html`) follows these patterns:

- **Self-contained**: All CSS and JavaScript are inline. No external dependencies.
- **Sections**: Key Differences, OSS Mappings, Plus Mappings, CRD Features Summary, Installing CRDs, Migration Checklist, Additional Resources.
- **Table pattern**: Each category uses `<table class="mapping-table">` with two columns: community annotation → official equivalent.
- **Expandable rows**: CRD-based mappings use paired rows:
  ```html
  <tr class="expandable" onclick="toggleRow(this)">
      <td><div class="annotation-list"><code>annotation-1</code><code>annotation-2</code></div></td>
      <td><span class="badge badge-policy">Policy</span> <code>crdField</code></td>
  </tr>
  <tr class="example-row"><td colspan="2"><div class="example-content">
      <!-- install-box, comparison blocks, info-box -->
  </div></td></tr>
  ```
- **Grouped annotations**: Related annotations (e.g., all `session-cookie-*`) are listed vertically in `<div class="annotation-list">`.
- **Install boxes**: Each CRD dropdown includes a `.install-box` with `kubectl apply` commands and a copy button.
- **Badge classes**: `.badge-virtualserver`, `.badge-policy`, `.badge-transportserver`, `.badge-plus`.
- **Comparison blocks**: Side-by-side YAML using `.comparison > .comparison-block.old` / `.comparison-block.new`.
- **Alphabetical ordering**: Categories within each section (OSS/Plus) and annotations within grouped rows are sorted alphabetically.
- **Dual-example tabs**: Where a community annotation maps to either an annotation or a CRD, the example row uses a tabbed interface:
  ```html
  <div class="approach-tabs">
      <button class="approach-tab active" onclick="switchApproach(this, 'annotation')">Annotation Approach</button>
      <button class="approach-tab" onclick="switchApproach(this, 'crd')">CRD Approach</button>
  </div>
  <div class="approach-content active" data-approach="annotation">...</div>
  <div class="approach-content" data-approach="crd">...</div>
  ```
  The CRD tab includes the `.install-box`; the annotation tab does not. Currently used in: Authentication (Basic), Rate Limiting, Rewrites.
- **GitHub contribution link**: Present in the header (button with GitHub SVG icon), Additional Resources list, and footer. Points to `https://github.com/alessfg/nginx-ingress-migration-guide`.

## Markdown Guide Structure

The migration guide (`nginx-ingress-migration-guide.md`) is organized as:

1. **Key Differences table** — high-level comparison of the two controllers
2. **Annotation Mapping Tables** — grouped by feature category (14 categories), each with a two-column table: community annotation → official annotation/CRD
3. **CRD-Based Migration Examples** — YAML before/after examples for features that require CRDs (SSL passthrough, mTLS, upstream config, health checks, rewrites, redirects, auth, rate limiting, canary, access control, headers, error handling, WAF, caching, gRPC, traffic splitting, VirtualServerRoute delegation, CORS)
4. **CRD-Only Features** — tables of VirtualServer, Policy, TransportServer, and GlobalConfiguration features with no community annotation equivalent
5. **TransportServer Examples** — TCP and UDP load balancing
6. **Installing CRDs** — kubectl commands for CRD installation
7. **Quick Reference** — concise annotation-to-CRD mapping cheat sheet
8. **Migration Checklist** — step-by-step migration steps
9. **Additional Resources** — links to official docs

## Research Approach

When updating or verifying annotation mappings, use the GitHub MCP tools or API access to fetch source documentation directly from the official documentation and source repositories:

- **Community controller annotations**: `kubernetes/ingress-nginx` at `docs/user-guide/nginx-configuration/annotations.md`
  - GitHub: https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md
  - Published docs: https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/
- **Official controller annotations**: `nginx/kubernetes-ingress` — check `docs/content/` directory tree for annotation and CRD documentation
  - GitHub: https://github.com/nginx/kubernetes-ingress/tree/main/docs/content
  - Annotations: https://docs.nginx.com/nginx-ingress-controller/configuration/ingress-resources/advanced-configuration-with-annotations/
  - VirtualServer/VirtualServerRoute CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/virtualserver-and-virtualserverroute-resources/
  - Policy CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/policy-resource/
  - TransportServer CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/transportserver-resource/
  - GlobalConfiguration CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/global-configuration/globalconfiguration-resource/
- **Official migration guide**: https://docs.nginx.com/nginx-ingress-controller/install/migrate-ingress-nginx — F5/NGINX's own migration documentation from the community controller
- **Avoid WebFetch** for external documentation sites; prefer GitHub MCP tools or API access to raw source docs in the repos

## Design Decisions (History)

These decisions were made iteratively during development and should be preserved:

1. CRD examples are **inline** (expandable table rows), not in a separate section.
2. Related annotations are **grouped** into single expandable rows (e.g., all `session-cookie-*` annotations in one row).
3. Each CRD dropdown includes **installation instructions** (`kubectl apply` commands) specific to the CRDs needed.
4. Mappings are **split into OSS and Plus** sections to distinguish what requires a Plus subscription.
5. All categories and annotations are in **alphabetical order**.
6. A **"How to Read the Mappings"** box explains the row types (annotation-to-annotation, CRD-based, grouped, official-only).
7. The CRD Features Summary includes **descriptions and context** for each CRD type, not just bullet lists.
8. **Every row is expandable** (v5+) — annotation-to-annotation rows show before/after YAML; CRD rows show install-box + CRD YAML; dual-approach rows use tabs.
9. **Search hides entire categories** when no rows match, and collapses expanded example rows during search.
10. A **GitHub contribution link** appears in the header, Additional Resources, and footer.
