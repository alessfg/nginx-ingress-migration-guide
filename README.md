
[![Project Status: Concept – Minimal or no implementation has been done yet, or the repository is only intended to be a limited example, demo, or proof-of-concept.](https://www.repostatus.org/badges/latest/concept.svg)](https://www.repostatus.org/#concept)
[![OpenSSF Scorecard](https://api.securityscorecards.dev/projects/github.com/alessfg/nginx-ingress-migration-guide/badge)](https://securityscorecards.dev/viewer/?uri=github.com/alessfg/nginx-ingress-migration-guide)
[![Community Support](https://badgen.net/badge/support/community/cyan?icon=awesome)](/SUPPORT.md) <!-- [![Commercial Support](https://badgen.net/badge/support/commercial/cyan?icon=awesome)](<Insert URL>) -->
[![Community Forum](https://img.shields.io/badge/community-forum-009639?logo=discourse&link=https%3A%2F%2Fcommunity.nginx.org)](https://community.nginx.org)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/license/apache-2-0)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-2.1-4baaaa.svg)](/CODE_OF_CONDUCT.md)

# nginx_ingress_migration_guide

## Requirements

This guide is useful for teams and individuals who:

- Are currently using the Kubernetes community NGINX Ingress Controller (`kubernetes/ingress-nginx`)
- Want to migrate to the Official NGINX Ingress Controller (`nginx/kubernetes-ingress`)
- Need to understand annotation mappings and configuration differences between the two controllers
- Want to leverage advanced features like CRDs (VirtualServer, Policy, TransportServer) available in the Official controller

No special software requirements are needed to view this guide - it can be accessed via the HTML interface or read directly from the markdown file.

## Getting Started

You can access this migration guide in three ways:

1. **Online**: Visit the hosted version at <https://alessfg.github.io/nginx-ingress-migration-guide/>
2. **HTML Interface**: Open `index.html` in a web browser for an interactive experience with searchable tables and expandable examples
3. **Markdown Document**: Read `nginx-ingress-migration-guide.md` directly for a comprehensive text-based reference

The guide is organized into sections covering:

- Key differences between the two ingress controllers
- Comprehensive annotation mapping tables
- Configuration examples and migration patterns
- CRD usage for advanced features

## How to Use

This migration guide provides side-by-side comparisons of annotations and configurations:

1. **Find your current annotation**: Look up the Kubernetes NGINX Ingress annotation you're currently using (prefix: `nginx.ingress.kubernetes.io/`)
2. **Identify the equivalent**: Find the corresponding Official NGINX Ingress annotation (prefix: `nginx.org/` or `nginx.com/`) or CRD configuration
3. **Review examples**: Check the detailed examples provided for complex features like TLS configuration, rate limiting, and canary deployments
4. **Plan your migration**: Use the tables to create a comprehensive migration plan for your ingress resources

For features that require CRDs (Custom Resource Definitions) instead of annotations, refer to the specific CRD sections which provide YAML examples and configuration patterns.

## Contributing

Please see the [contributing guide](/CONTRIBUTING.md) for guidelines on how to best contribute to this project.

## License

[Apache License, Version 2.0](/LICENSE)

&copy; [F5, Inc.](https://www.f5.com/) 2025
