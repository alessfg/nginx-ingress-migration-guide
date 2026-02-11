# CLAUDE.md

## Project Overview

Documentation-only project: migration guide from the Kubernetes community NGINX Ingress Controller (`kubernetes/ingress-nginx`) to the Official NGINX Ingress Controller (`nginx/kubernetes-ingress`).

- No build system, tests, or package manager
- Self-contained HTML + Markdown documentation
- Owned by F5, Inc., Apache 2.0 license

## Key Files

- `index.html` — **The live guide** served via GitHub Pages. Self-contained HTML (inline CSS/JS, no dependencies). Currently v11.
- `nginx-ingress-migration-guide.md` — Original Markdown reference (~2,170 lines) with annotation tables and CRD examples.
- `nginx-ingress-migration-guide-v*.html` — Version history (v1-v11). Read these to understand feature evolution.

## Hosting

- **Repository**: https://github.com/alessfg/nginx-ingress-migration-guide
- **GitHub Pages**: https://alessfg.github.io/nginx-ingress-migration-guide/ (serves `index.html` from `main` branch)

## Domain Concepts

- **Annotation prefixes**: Community uses `nginx.ingress.kubernetes.io/`, Official uses `nginx.org/` (OSS) or `nginx.com/` (Plus)
- **CRDs**: Official controller supports VirtualServer, VirtualServerRoute, Policy, TransportServer, GlobalConfiguration
- **NGINX Plus**: Only the Official controller supports Plus features (JWT, OIDC, WAF, session affinity)
- **Naming**: Use "Official NGINX" — never "NGINX Inc."

## Research Resources

When verifying annotation mappings, use GitHub MCP tools to fetch from these authoritative sources:

**Community controller** (`kubernetes/ingress-nginx`):

- GitHub: https://github.com/kubernetes/ingress-nginx
- Docs tree: https://github.com/kubernetes/ingress-nginx/blob/main/docs
- Annotations: https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md
- Docs site: https://kubernetes.github.io/ingress-nginx
- Published annotations: https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/

**Official controller** (`nginx/kubernetes-ingress`):

- GitHub: https://github.com/nginx/kubernetes-ingress
- Docs tree: https://github.com/nginx/kubernetes-ingress/tree/main/docs/content
- Annotations: https://github.com/nginx/documentation/blob/main/content/nic/configuration/ingress-resources/advanced-configuration-with-annotations.md
- Docs site: https://docs.nginx.com/nginx-ingress-controller/
- Published annotations: https://docs.nginx.com/nginx-ingress-controller/configuration/ingress-resources/advanced-configuration-with-annotations/
- VirtualServer CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/virtualserver-and-virtualserverroute-resources/
- Policy CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/policy-resource/
- TransportServer CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/transportserver-resource/
- GlobalConfiguration CRD: https://docs.nginx.com/nginx-ingress-controller/configuration/global-configuration/globalconfiguration-resource/

**Official migration guide**: https://docs.nginx.com/nginx-ingress-controller/install/migrate-ingress-nginx

Prefer GitHub MCP tools over WebFetch for documentation sites.
