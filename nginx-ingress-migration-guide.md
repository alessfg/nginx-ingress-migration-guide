# Migration Guide: Kubernetes NGINX Ingress to Official NGINX Ingress Controller

This guide helps you migrate from the **Kubernetes community NGINX Ingress Controller** (`kubernetes/ingress-nginx`) to the **Official NGINX Ingress Controller** (`nginx/kubernetes-ingress`).

## Key Differences

| Aspect | Kubernetes NGINX Ingress | Official NGINX Ingress |
| -------- | -------------------------- | --------------------- |
| Annotation Prefix | `nginx.ingress.kubernetes.io/` | `nginx.org/` or `nginx.com/` |
| CRD Support | Limited | VirtualServer, VirtualServerRoute, Policy, TransportServer |
| NGINX Plus Support | No | Yes (with `nginx.com/` annotations) |
| Configuration Style | Primarily annotations | Annotations + CRDs for advanced features |

## Annotation Mapping Table

### Timeouts and Buffering

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/proxy-connect-timeout` | `nginx.org/proxy-connect-timeout` |
| `nginx.ingress.kubernetes.io/proxy-send-timeout` | `nginx.org/proxy-send-timeout` |
| `nginx.ingress.kubernetes.io/proxy-read-timeout` | `nginx.org/proxy-read-timeout` |
| `nginx.ingress.kubernetes.io/proxy-body-size` | `nginx.org/client-max-body-size` |
| `nginx.ingress.kubernetes.io/client-body-buffer-size` | `nginx.org/client-body-buffer-size` |
| `nginx.ingress.kubernetes.io/proxy-buffering` | `nginx.org/proxy-buffering` |
| `nginx.ingress.kubernetes.io/proxy-buffers-number` | `nginx.org/proxy-buffers` (format: `"number size"`) |
| `nginx.ingress.kubernetes.io/proxy-buffer-size` | `nginx.org/proxy-buffer-size` |
| `nginx.ingress.kubernetes.io/proxy-busy-buffers-size` | `nginx.org/proxy-busy-buffers-size` |
| `nginx.ingress.kubernetes.io/proxy-max-temp-file-size` | `nginx.org/proxy-max-temp-file-size` |
| `nginx.ingress.kubernetes.io/proxy-request-buffering` | ConfigMap: `proxy-request-buffering` |

### SSL/TLS Configuration

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/ssl-redirect` | `ingress.kubernetes.io/ssl-redirect` or `nginx.org/redirect-to-https` |
| `nginx.ingress.kubernetes.io/force-ssl-redirect` | `nginx.org/redirect-to-https` |
| `nginx.ingress.kubernetes.io/ssl-passthrough` | Use TransportServer CRD with `listener.protocol: TLS_PASSTHROUGH` |
| `nginx.ingress.kubernetes.io/ssl-ciphers` | `nginx.org/ssl-ciphers` |
| `nginx.ingress.kubernetes.io/ssl-prefer-server-ciphers` | `nginx.org/ssl-prefer-server-ciphers` |
| `nginx.ingress.kubernetes.io/auth-tls-secret` | **Policy CRD**: `IngressMTLS` with `clientCertSecret` |
| `nginx.ingress.kubernetes.io/auth-tls-verify-depth` | **Policy CRD**: `IngressMTLS` with `verifyDepth` |
| `nginx.ingress.kubernetes.io/auth-tls-verify-client` | **Policy CRD**: `IngressMTLS` with `verifyClient` |
| `nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream` | VirtualServer: `upstreams[].tls` + `proxy.requestHeaders` |

### Backend/Upstream Configuration

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/backend-protocol` | `nginx.org/ssl-services` (for HTTPS) or `nginx.org/grpc-services` (for gRPC) |
| `nginx.ingress.kubernetes.io/proxy-ssl-secret` | **Policy CRD**: `EgressMTLS` with `tlsSecret` |
| `nginx.ingress.kubernetes.io/proxy-ssl-verify` | **Policy CRD**: `EgressMTLS` with `verifyServer` |
| `nginx.ingress.kubernetes.io/proxy-ssl-verify-depth` | **Policy CRD**: `EgressMTLS` with `verifyDepth` |
| `nginx.ingress.kubernetes.io/proxy-ssl-ciphers` | **Policy CRD**: `EgressMTLS` with `ciphers` |
| `nginx.ingress.kubernetes.io/proxy-ssl-protocols` | **Policy CRD**: `EgressMTLS` with `protocols` |
| `nginx.ingress.kubernetes.io/proxy-ssl-name` | **Policy CRD**: `EgressMTLS` with `sslName` |
| `nginx.ingress.kubernetes.io/proxy-ssl-server-name` | **Policy CRD**: `EgressMTLS` with `serverName` |
| `nginx.ingress.kubernetes.io/service-upstream` | `nginx.org/use-cluster-ip` |
| `nginx.ingress.kubernetes.io/upstream-hash-by` | VirtualServer CRD: `upstreams[].lb-method: "hash"` |
| `nginx.ingress.kubernetes.io/load-balance` | `nginx.org/lb-method` |
| `nginx.ingress.kubernetes.io/proxy-next-upstream` | VirtualServer CRD: `upstreams[].next-upstream` |
| `nginx.ingress.kubernetes.io/proxy-next-upstream-timeout` | VirtualServer CRD: `upstreams[].next-upstream-timeout` |
| `nginx.ingress.kubernetes.io/proxy-next-upstream-tries` | VirtualServer CRD: `upstreams[].next-upstream-tries` |

### Rewrites and Redirects

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/rewrite-target` | `nginx.org/rewrite-target` or VirtualServer CRD: `routes[].action.proxy.rewritePath` |
| `nginx.ingress.kubernetes.io/app-root` | VirtualServer CRD: `routes[].action.redirect` with path `/` |
| `nginx.ingress.kubernetes.io/use-regex` | `nginx.org/path-regex: "case_sensitive"` or `"case_insensitive"` |
| `nginx.ingress.kubernetes.io/permanent-redirect` | VirtualServer CRD: `routes[].action.redirect` with `code: 301` |
| `nginx.ingress.kubernetes.io/permanent-redirect-code` | VirtualServer CRD: `routes[].action.redirect.code` |
| `nginx.ingress.kubernetes.io/temporal-redirect` | VirtualServer CRD: `routes[].action.redirect` with `code: 302` |
| `nginx.ingress.kubernetes.io/from-to-www-redirect` | VirtualServer CRD: custom `routes[]` with redirect action |
| `nginx.ingress.kubernetes.io/proxy-redirect-from` | ConfigMap or server-snippets |
| `nginx.ingress.kubernetes.io/proxy-redirect-to` | ConfigMap or server-snippets |

### Session Affinity / Sticky Sessions

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/affinity` | `nginx.com/sticky-cookie-services` (NGINX Plus) or VirtualServer CRD: `upstreams[].sessionCookie` |
| `nginx.ingress.kubernetes.io/session-cookie-name` | VirtualServer CRD: `upstreams[].sessionCookie.name` |
| `nginx.ingress.kubernetes.io/session-cookie-path` | VirtualServer CRD: `upstreams[].sessionCookie.path` |
| `nginx.ingress.kubernetes.io/session-cookie-domain` | VirtualServer CRD: `upstreams[].sessionCookie.domain` |
| `nginx.ingress.kubernetes.io/session-cookie-expires` | VirtualServer CRD: `upstreams[].sessionCookie.expires` |
| `nginx.ingress.kubernetes.io/session-cookie-max-age` | VirtualServer CRD: `upstreams[].sessionCookie.expires` |
| `nginx.ingress.kubernetes.io/session-cookie-samesite` | VirtualServer CRD: `upstreams[].sessionCookie.samesite` |
| `nginx.ingress.kubernetes.io/session-cookie-secure` | VirtualServer CRD: `upstreams[].sessionCookie.secure` |
| `nginx.ingress.kubernetes.io/affinity-mode` | Not directly supported; use `sessionCookie` for similar behavior |

### Rate Limiting

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/limit-rps` | `nginx.org/limit-req-rate: "Xr/s"` or **Policy CRD**: `RateLimit` |
| `nginx.ingress.kubernetes.io/limit-rpm` | `nginx.org/limit-req-rate: "Xr/m"` or **Policy CRD**: `RateLimit` |
| `nginx.ingress.kubernetes.io/limit-connections` | ConfigMap or snippets (no direct annotation) |
| `nginx.ingress.kubernetes.io/limit-burst-multiplier` | `nginx.org/limit-req-burst` or **Policy CRD**: `RateLimit.burst` |
| `nginx.ingress.kubernetes.io/limit-rate` | ConfigMap: `limit-rate` |
| `nginx.ingress.kubernetes.io/limit-rate-after` | ConfigMap: `limit-rate-after` |
| `nginx.ingress.kubernetes.io/limit-whitelist` | **Policy CRD**: `AccessControl.allow` |

### Authentication

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/auth-type` (basic) | `nginx.org/basic-auth-secret` + `nginx.org/basic-auth-realm` or **Policy CRD**: `BasicAuth` |
| `nginx.ingress.kubernetes.io/auth-secret` | `nginx.org/basic-auth-secret` or **Policy CRD**: `BasicAuth.secret` |
| `nginx.ingress.kubernetes.io/auth-realm` | `nginx.org/basic-auth-realm` or **Policy CRD**: `BasicAuth.realm` |
| `nginx.ingress.kubernetes.io/auth-url` | **Policy CRD**: Use `OIDC` policy or custom location-snippets |
| `nginx.ingress.kubernetes.io/auth-signin` | **Policy CRD**: `OIDC.authEndpoint` |
| `nginx.ingress.kubernetes.io/auth-response-headers` | VirtualServer CRD: `routes[].action.proxy.requestHeaders` |

### CORS

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/enable-cors` | Use `nginx.org/server-snippets` or `nginx.org/location-snippets` |
| `nginx.ingress.kubernetes.io/cors-allow-origin` | Use snippets with `add_header Access-Control-Allow-Origin` |
| `nginx.ingress.kubernetes.io/cors-allow-methods` | Use snippets with `add_header Access-Control-Allow-Methods` |
| `nginx.ingress.kubernetes.io/cors-allow-headers` | Use snippets with `add_header Access-Control-Allow-Headers` |
| `nginx.ingress.kubernetes.io/cors-expose-headers` | Use snippets with `add_header Access-Control-Expose-Headers` |
| `nginx.ingress.kubernetes.io/cors-allow-credentials` | Use snippets with `add_header Access-Control-Allow-Credentials` |
| `nginx.ingress.kubernetes.io/cors-max-age` | Use snippets with `add_header Access-Control-Max-Age` |

### Canary Deployments / Traffic Splitting

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/canary` | **VirtualServer CRD**: `routes[].splits[]` |
| `nginx.ingress.kubernetes.io/canary-weight` | **VirtualServer CRD**: `routes[].splits[].weight` |
| `nginx.ingress.kubernetes.io/canary-by-header` | **VirtualServer CRD**: `routes[].matches[].conditions[].header` |
| `nginx.ingress.kubernetes.io/canary-by-header-value` | **VirtualServer CRD**: `routes[].matches[].conditions[].value` |
| `nginx.ingress.kubernetes.io/canary-by-cookie` | **VirtualServer CRD**: `routes[].matches[].conditions[].cookie` |
| `nginx.ingress.kubernetes.io/canary-weight-total` | **VirtualServer CRD**: Total of `splits[].weight` must equal 100 |

### Access Control

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/whitelist-source-range` | **Policy CRD**: `AccessControl.allow` |
| `nginx.ingress.kubernetes.io/denylist-source-range` | **Policy CRD**: `AccessControl.deny` |
| `nginx.ingress.kubernetes.io/satisfy` | Use `nginx.org/location-snippets` with `satisfy any/all;` |

### Custom Configuration Snippets

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/configuration-snippet` | `nginx.org/location-snippets` |
| `nginx.ingress.kubernetes.io/server-snippet` | `nginx.org/server-snippets` |
| `nginx.ingress.kubernetes.io/stream-snippet` | TransportServer CRD or GlobalConfiguration |

### Headers

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/proxy-hide-headers` | `nginx.org/proxy-hide-headers` |
| `nginx.ingress.kubernetes.io/proxy-pass-headers` | `nginx.org/proxy-pass-headers` |
| `nginx.ingress.kubernetes.io/custom-headers` | VirtualServer CRD: `routes[].action.proxy.responseHeaders.add` |
| `nginx.ingress.kubernetes.io/x-forwarded-prefix` | Use `nginx.org/proxy-set-headers` |
| `nginx.ingress.kubernetes.io/connection-proxy-header` | Use snippets or ConfigMap |

### Health Checks

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| N/A (passive only) | `nginx.com/health-checks` (NGINX Plus) |
| N/A | VirtualServer CRD: `upstreams[].healthCheck` (NGINX Plus) |
| N/A | `nginx.com/health-checks-mandatory` |
| N/A | `nginx.com/slow-start` |

### Upstream Configuration

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| N/A | `nginx.org/max-fails` |
| N/A | `nginx.org/fail-timeout` |
| N/A | `nginx.org/max-conns` |
| N/A | `nginx.org/keepalive` |
| N/A | `nginx.org/upstream-zone-size` |

### WAF / ModSecurity

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/enable-modsecurity` | `appprotect.f5.com/app-protect-enable` or **Policy CRD**: `WAF` |
| `nginx.ingress.kubernetes.io/enable-owasp-core-rules` | **Policy CRD**: `WAF.apPolicy` (reference NGINX App Protect policy) |
| `nginx.ingress.kubernetes.io/modsecurity-snippet` | **Policy CRD**: `WAF` with custom policy |
| `nginx.ingress.kubernetes.io/modsecurity-transaction-id` | App Protect log configuration |

### Miscellaneous

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| `nginx.ingress.kubernetes.io/server-alias` | Create additional VirtualServer resources |
| `nginx.ingress.kubernetes.io/default-backend` | VirtualServer CRD: `routes[].errorPages` |
| `nginx.ingress.kubernetes.io/custom-http-errors` | VirtualServer CRD: `routes[].errorPages[].codes` |
| `nginx.ingress.kubernetes.io/enable-access-log` | ConfigMap: `access-log-off` |
| `nginx.ingress.kubernetes.io/server-tokens` | `nginx.org/server-tokens` |
| `nginx.ingress.kubernetes.io/proxy-http-version` | ConfigMap: `proxy-protocol` |
| `nginx.ingress.kubernetes.io/upstream-vhost` | VirtualServer CRD: `routes[].action.proxy.requestHeaders.set` (Host header) |
| `nginx.ingress.kubernetes.io/enable-opentelemetry` | ConfigMap: enable OpenTelemetry settings |
| `nginx.ingress.kubernetes.io/http2-push-preload` | ConfigMap: `http2-push-preload` |
| `nginx.ingress.kubernetes.io/websocket-services` | `nginx.org/websocket-services` |
| `nginx.ingress.kubernetes.io/mirror-target` | Use location-snippets with `mirror` directive |

### HSTS

| Kubernetes NGINX Ingress Annotation | Official NGINX Annotation/CRD |
| ------------------------------------- | --------------------------- |
| N/A (ConfigMap only) | `nginx.org/hsts` |
| N/A | `nginx.org/hsts-max-age` |
| N/A | `nginx.org/hsts-include-subdomains` |
| N/A | `nginx.org/hsts-behind-proxy` |

---

## CRD-Based Migration Examples

### SSL/TLS Configuration Examples

#### Example: SSL Passthrough with TransportServer CRD

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ssl-passthrough-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
spec:
  rules:
    - host: secure.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: secure-backend
                port:
                  number: 443
```

**Official NGINX TransportServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: TransportServer
metadata:
  name: ssl-passthrough
spec:
  listener:
    name: tls-passthrough
    protocol: TLS_PASSTHROUGH
  host: secure.example.com
  upstreams:
    - name: secure-backend
      service: secure-backend
      port: 443
  action:
    pass: secure-backend
```

> **Note:** Requires GlobalConfiguration with a TLS_PASSTHROUGH listener:

```yaml
apiVersion: k8s.nginx.org/v1
kind: GlobalConfiguration
metadata:
  name: nginx-configuration
  namespace: nginx-ingress
spec:
  listeners:
    - name: tls-passthrough
      port: 443
      protocol: TLS_PASSTHROUGH
```

---

#### Example: Backend/Egress mTLS with EgressMTLS Policy

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: backend-mtls-ingress
  annotations:
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
    nginx.ingress.kubernetes.io/proxy-ssl-secret: "default/backend-client-cert"
    nginx.ingress.kubernetes.io/proxy-ssl-verify: "on"
    nginx.ingress.kubernetes.io/proxy-ssl-verify-depth: "2"
    nginx.ingress.kubernetes.io/proxy-ssl-ciphers: "HIGH:!aNULL:!MD5"
    nginx.ingress.kubernetes.io/proxy-ssl-protocols: "TLSv1.2 TLSv1.3"
    nginx.ingress.kubernetes.io/proxy-ssl-name: "backend.internal"
    nginx.ingress.kubernetes.io/proxy-ssl-server-name: "on"
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: secure-backend
                port:
                  number: 443
```

**Official NGINX EgressMTLS Policy CRD:**

```yaml
# Secret for client certificate (must be kubernetes.io/tls type)
apiVersion: v1
kind: Secret
metadata:
  name: egress-mtls-secret
type: kubernetes.io/tls
data:
  tls.crt: <base64-encoded-client-cert>
  tls.key: <base64-encoded-client-key>
---
# Secret for CA certificate to verify backend (must be nginx.org/ca type)
apiVersion: v1
kind: Secret
metadata:
  name: egress-trusted-ca
type: nginx.org/ca
data:
  ca.crt: <base64-encoded-ca-cert>
---
# EgressMTLS Policy
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: egress-mtls-policy
spec:
  egressMTLS:
    tlsSecret: egress-mtls-secret
    trustedCertSecret: egress-trusted-ca
    verifyServer: true
    verifyDepth: 2
    ciphers: "HIGH:!aNULL:!MD5"
    protocols: "TLSv1.2 TLSv1.3"
    sslName: backend.internal
    serverName: true
    sessionReuse: true
---
# VirtualServer referencing the policy
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: secure-backend
      service: secure-backend
      port: 443
      tls:
        enable: true
  routes:
    - path: /
      policies:
        - name: egress-mtls-policy
      action:
        pass: secure-backend
```

---

### Upstream Configuration Examples

#### Example: Advanced Upstream Settings with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: upstream-config-ingress
  annotations:
    nginx.ingress.kubernetes.io/upstream-hash-by: "$request_uri"
    nginx.ingress.kubernetes.io/proxy-next-upstream: "error timeout http_502 http_503"
    nginx.ingress.kubernetes.io/proxy-next-upstream-timeout: "30"
    nginx.ingress.kubernetes.io/proxy-next-upstream-tries: "3"
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "10"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "60"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

**Official NGINX VirtualServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
      lb-method: "hash $request_uri consistent"
      max-fails: 3
      fail-timeout: 10s
      max-conns: 100
      keepalive: 32
      connect-timeout: 10s
      send-timeout: 60s
      read-timeout: 60s
      next-upstream: "error timeout http_502 http_503"
      next-upstream-timeout: 30s
      next-upstream-tries: 3
      client-max-body-size: 10m
      buffering: true
      buffer-size: 8k
      buffers:
        number: 4
        size: 8k
  routes:
    - path: /
      action:
        pass: my-backend
```

---

#### Example: Active Health Checks with VirtualServer (NGINX Plus)

**Kubernetes NGINX Ingress:**
> No direct equivalent - only passive health checks available

**Official NGINX VirtualServer CRD (NGINX Plus):**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
      healthCheck:
        enable: true
        path: /health
        interval: 5s
        jitter: 2s
        fails: 3
        passes: 2
        port: 8080
        connect-timeout: 5s
        read-timeout: 5s
        send-timeout: 5s
        statusMatch: "200-399"
        headers:
          - name: Host
            value: health.example.com
          - name: X-Health-Check
            value: "true"
        # For mandatory health checks (wait before serving traffic)
        mandatory: true
        persistent: true
  routes:
    - path: /
      action:
        pass: my-backend
```

---

### Rewrite and Redirect Examples

#### Example: Permanent Redirect with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: redirect-ingress
  annotations:
    nginx.ingress.kubernetes.io/permanent-redirect: "https://new-domain.example.com$request_uri"
    nginx.ingress.kubernetes.io/permanent-redirect-code: "308"
spec:
  rules:
    - host: old-domain.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: dummy-service
                port:
                  number: 80
```

**Official NGINX VirtualServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: permanent-redirect
spec:
  host: old-domain.example.com
  routes:
    - path: /
      action:
        redirect:
          url: https://new-domain.example.com${request_uri}
          code: 308
```

---

#### Example: Temporal Redirect with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/temporal-redirect: "https://maintenance.example.com"
  nginx.ingress.kubernetes.io/temporal-redirect-code: "307"
```

**Official NGINX VirtualServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: temporal-redirect
spec:
  host: myapp.example.com
  routes:
    - path: /
      action:
        redirect:
          url: https://maintenance.example.com
          code: 307
```

---

#### Example: App Root Redirect with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/app-root: "/dashboard"
```

**Official NGINX VirtualServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: app-root-redirect
spec:
  host: myapp.example.com
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
  routes:
    # Redirect root path to /dashboard
    - path: /
      matches:
        - conditions:
            - variable: $request_uri
              value: "/"
          action:
            redirect:
              url: ${scheme}://${host}/dashboard
              code: 302
      action:
        pass: my-backend
    # Serve all other paths normally
    - path: /dashboard
      action:
        pass: my-backend
```

---

#### Example: WWW to Non-WWW Redirect (and vice versa)

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/from-to-www-redirect: "true"
```

**Official NGINX VirtualServer CRD (www to non-www):**

```yaml
# Redirect www.example.com to example.com
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: www-redirect
spec:
  host: www.example.com
  routes:
    - path: /
      action:
        redirect:
          url: https://example.com${request_uri}
          code: 301
---
# Main VirtualServer for example.com
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: main-app
spec:
  host: example.com
  tls:
    secret: tls-secret
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
  routes:
    - path: /
      action:
        pass: my-backend
```

---

### Authentication Examples

#### Example: Basic Authentication with Policy CRD

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: basic-auth-ingress
  annotations:
    nginx.ingress.kubernetes.io/auth-type: "basic"
    nginx.ingress.kubernetes.io/auth-secret: "basic-auth-secret"
    nginx.ingress.kubernetes.io/auth-realm: "Authentication Required"
spec:
  rules:
    - host: secure.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

**Official NGINX BasicAuth Policy CRD:**

```yaml
# Create htpasswd secret (type must be nginx.org/htpasswd)
# Generate with: htpasswd -c auth username
apiVersion: v1
kind: Secret
metadata:
  name: basic-auth-secret
type: nginx.org/htpasswd
data:
  htpasswd: <base64-encoded-htpasswd-content>
---
# BasicAuth Policy
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: basic-auth-policy
spec:
  basicAuth:
    secret: basic-auth-secret
    realm: "Authentication Required"
---
# VirtualServer with BasicAuth policy
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: secure-app
spec:
  host: secure.example.com
  policies:
    - name: basic-auth-policy
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
  routes:
    - path: /
      action:
        pass: my-backend
```

---

#### Example: JWT Authentication with Policy CRD (NGINX Plus)

**Kubernetes NGINX Ingress:**
> No direct equivalent - external auth required

**Official NGINX JWT Policy CRD (NGINX Plus):**

```yaml
# Option 1: JWT secret stored in Kubernetes
apiVersion: v1
kind: Secret
metadata:
  name: jwt-secret
type: nginx.org/jwk
data:
  jwk: <base64-encoded-jwk-content>
---
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: jwt-policy-local
spec:
  jwt:
    secret: jwt-secret
    realm: "API"
    token: "$http_authorization"  # Or $cookie_auth_token, $arg_token
---
# Option 2: JWT validation via remote JWKS URI
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: jwt-policy-remote
spec:
  jwt:
    realm: "API"
    jwksURI: "https://idp.example.com/.well-known/jwks.json"
    keyCache: 1h
    token: "$http_authorization"
---
# VirtualServer with JWT policy
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: api
spec:
  host: api.example.com
  policies:
    - name: jwt-policy-remote
  upstreams:
    - name: api-backend
      service: api-service
      port: 80
  routes:
    - path: /api
      action:
        pass: api-backend
    # Public endpoint without JWT
    - path: /health
      action:
        pass: api-backend
```

---

#### Example: OIDC Authentication with Policy CRD (NGINX Plus)

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/auth-url: "https://auth.example.com/oauth2/auth"
  nginx.ingress.kubernetes.io/auth-signin: "https://auth.example.com/oauth2/start?rd=$escaped_request_uri"
  nginx.ingress.kubernetes.io/auth-response-headers: "X-Auth-User, X-Auth-Email"
```

**Official NGINX OIDC Policy CRD (NGINX Plus):**

```yaml
# OIDC client secret
apiVersion: v1
kind: Secret
metadata:
  name: oidc-secret
type: nginx.org/oidc
data:
  client-secret: <base64-encoded-client-secret>
---
# OIDC Policy
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: oidc-policy
spec:
  oidc:
    clientID: "my-client-id"
    clientSecret: oidc-secret
    authEndpoint: "https://idp.example.com/authorize"
    tokenEndpoint: "https://idp.example.com/oauth/token"
    jwksURI: "https://idp.example.com/.well-known/jwks.json"
    scope: "openid+profile+email"
    redirectURI: "/_codexch"
    endSessionEndpoint: "https://idp.example.com/logout"
    postLogoutRedirectURI: "/_logout"
    # Optional: enable PKCE (for public clients)
    # pkceEnable: true
---
# VirtualServer with OIDC policy
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: oidc-app
spec:
  host: app.example.com
  tls:
    secret: tls-secret
  policies:
    - name: oidc-policy
  upstreams:
    - name: app-backend
      service: app-service
      port: 80
  routes:
    - path: /
      action:
        pass: app-backend
```

---

#### Example: API Key Authentication with Policy CRD

**Kubernetes NGINX Ingress:**
> No direct equivalent

**Official NGINX APIKey Policy CRD:**

```yaml
# API key secret (hashed with SHA-256)
# Keys are stored as: name: sha256-hash
apiVersion: v1
kind: Secret
metadata:
  name: api-key-secret
type: nginx.org/apikey
data:
  # key "client1" = sha256("my-secret-api-key-1")
  client1: <base64-of-sha256-hash>
  # key "client2" = sha256("my-secret-api-key-2")
  client2: <base64-of-sha256-hash>
---
# API Key Policy
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: api-key-policy
spec:
  apiKey:
    suppliedIn:
      header:
        - X-API-Key
        - Authorization
      query:
        - api_key
    clientSecret: api-key-secret
---
# VirtualServer with API Key policy
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: api
spec:
  host: api.example.com
  policies:
    - name: api-key-policy
  upstreams:
    - name: api-backend
      service: api-service
      port: 80
  routes:
    - path: /v1
      action:
        pass: api-backend
```

---

### Rate Limiting Examples

#### Example: Basic Rate Limiting with Policy CRD

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/limit-rps: "10"
    nginx.ingress.kubernetes.io/limit-burst-multiplier: "5"
```

**Official NGINX Ingress (Policy CRD):**

```yaml
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: rate-limit-policy
spec:
  rateLimit:
    rate: 10r/s
    burst: 50
    key: ${binary_remote_addr}
    zoneSize: 10M
---
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  policies:
    - name: rate-limit-policy
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
  routes:
    - path: /
      action:
        pass: my-backend
```

#### Example: Canary Deployment with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
# Main Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: main-ingress
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            backend:
              service:
                name: main-service
                port:
                  number: 80
---
# Canary Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: canary-ingress
  annotations:
    nginx.ingress.kubernetes.io/canary: "true"
    nginx.ingress.kubernetes.io/canary-weight: "20"
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            backend:
              service:
                name: canary-service
                port:
                  number: 80
```

**Official NGINX Ingress (VirtualServer CRD):**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: main
      service: main-service
      port: 80
    - name: canary
      service: canary-service
      port: 80
  routes:
    - path: /
      splits:
        - weight: 80
          action:
            pass: main
        - weight: 20
          action:
            pass: canary
```

#### Example: Canary by Header with VirtualServer

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/canary: "true"
  nginx.ingress.kubernetes.io/canary-by-header: "X-Canary"
  nginx.ingress.kubernetes.io/canary-by-header-value: "always"
```

**Official NGINX VirtualServer:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: main
      service: main-service
      port: 80
    - name: canary
      service: canary-service
      port: 80
  routes:
    - path: /
      matches:
        - conditions:
            - header: X-Canary
              value: always
          action:
            pass: canary
      action:
        pass: main
```

#### Example: mTLS Client Authentication

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/auth-tls-secret: "namespace/ca-secret"
  nginx.ingress.kubernetes.io/auth-tls-verify-client: "on"
  nginx.ingress.kubernetes.io/auth-tls-verify-depth: "2"
```

**Official NGINX Ingress (Policy CRD):**

```yaml
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: ingress-mtls-policy
spec:
  ingressMTLS:
    clientCertSecret: ca-secret
    verifyClient: "on"
    verifyDepth: 2
---
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  tls:
    secret: tls-secret
  policies:
    - name: ingress-mtls-policy
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
  routes:
    - path: /
      action:
        pass: my-backend
```

#### Example: Session Affinity / Sticky Sessions

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/affinity: "cookie"
  nginx.ingress.kubernetes.io/session-cookie-name: "SERVERID"
  nginx.ingress.kubernetes.io/session-cookie-expires: "172800"
  nginx.ingress.kubernetes.io/session-cookie-path: "/"
  nginx.ingress.kubernetes.io/session-cookie-secure: "true"
```

**Official NGINX VirtualServer (NGINX Plus):**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
      sessionCookie:
        enable: true
        name: SERVERID
        expires: 48h
        path: /
        secure: true
  routes:
    - path: /
      action:
        pass: my-backend
```

#### Example: URL Rewriting

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/rewrite-target: /$2
  nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  rules:
    - http:
        paths:
          - path: /api(/|$)(.*)
            pathType: Prefix
```

**Official NGINX VirtualServer:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: api-backend
      service: api-service
      port: 80
  routes:
    - path: ~ ^/api(/|$)(.*)
      action:
        proxy:
          upstream: api-backend
          rewritePath: /$2
```

---

### Rate Limiting Examples (Continued)

#### Example: Advanced Rate Limiting with Conditions (NGINX Plus)

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/limit-rps: "100"
  nginx.ingress.kubernetes.io/limit-whitelist: "10.0.0.0/8"
```

**Official NGINX RateLimit Policy with Conditions (NGINX Plus):**

```yaml
# Rate limit policy with JWT-based conditions
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: rate-limit-by-tier
spec:
  rateLimit:
    rate: 10r/s
    burst: 20
    key: ${binary_remote_addr}
    zoneSize: 10M
    logLevel: notice
    rejectCode: 429
    # Condition: Apply different limits based on JWT claim
    condition:
      jwt:
        claim: tier
        match: premium
---
# Premium users get higher limits
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: rate-limit-premium
spec:
  rateLimit:
    rate: 100r/s
    burst: 200
    key: ${binary_remote_addr}
    zoneSize: 10M
    condition:
      jwt:
        claim: tier
        match: premium
---
# Default rate limit for other users
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: rate-limit-default
spec:
  rateLimit:
    rate: 10r/s
    burst: 20
    key: ${binary_remote_addr}
    zoneSize: 10M
    condition:
      default: true
---
# VirtualServer with multiple rate limit policies
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: api
spec:
  host: api.example.com
  policies:
    - name: rate-limit-premium
    - name: rate-limit-default
  upstreams:
    - name: api-backend
      service: api-service
      port: 80
  routes:
    - path: /api
      action:
        pass: api-backend
```

---

#### Example: Rate Limiting with Dry Run and Scaling

**Official NGINX RateLimit Policy with Scale and Dry Run:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: rate-limit-scaled
spec:
  rateLimit:
    rate: 100r/s
    burst: 200
    key: ${binary_remote_addr}
    zoneSize: 10M
    # Scale rate limit across pods (rate / pod count)
    scale: true
    # Test rate limiting without rejecting (useful for tuning)
    dryRun: false
    noDelay: false
    delay: 50
    logLevel: warn
    rejectCode: 429
```

---

### Access Control Examples

#### Example: Access Control (IP Whitelist/Denylist)

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/whitelist-source-range: "10.0.0.0/8,172.16.0.0/12"
```

**Official NGINX AccessControl Policy CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: allow-internal-policy
spec:
  accessControl:
    allow:
      - 10.0.0.0/8
      - 172.16.0.0/12
      - 192.168.0.0/16
---
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: internal-app
spec:
  host: internal.example.com
  policies:
    - name: allow-internal-policy
  upstreams:
    - name: internal-backend
      service: internal-service
      port: 80
  routes:
    - path: /
      action:
        pass: internal-backend
```

---

#### Example: IP Denylist with AccessControl Policy

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/denylist-source-range: "203.0.113.0/24,198.51.100.0/24"
```

**Official NGINX AccessControl Policy CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: deny-bad-actors
spec:
  accessControl:
    deny:
      - 203.0.113.0/24
      - 198.51.100.0/24
      - 192.0.2.100
```

> **Note:** AccessControl policies support either `allow` OR `deny`, not both simultaneously.

---

### Header Manipulation Examples

#### Example: Custom Response Headers with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/custom-headers: "default/custom-headers-configmap"
```

**Official NGINX VirtualServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
  routes:
    - path: /
      action:
        proxy:
          upstream: my-backend
          responseHeaders:
            # Add headers to client response
            add:
              - name: X-Frame-Options
                value: "DENY"
              - name: X-Content-Type-Options
                value: "nosniff"
              - name: X-XSS-Protection
                value: "1; mode=block"
              - name: Strict-Transport-Security
                value: "max-age=31536000; includeSubDomains"
                always: true  # Add regardless of response code
            # Hide headers from upstream
            hide:
              - X-Powered-By
              - Server
            # Pass specific hidden headers
            pass:
              - X-Custom-Header
```

---

#### Example: Request Header Manipulation with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/upstream-vhost: "backend.internal"
  nginx.ingress.kubernetes.io/x-forwarded-prefix: "/api"
```

**Official NGINX VirtualServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
  routes:
    - path: /api
      action:
        proxy:
          upstream: my-backend
          requestHeaders:
            # Override or add request headers
            set:
              - name: Host
                value: "backend.internal"
              - name: X-Forwarded-Prefix
                value: "/api"
              - name: X-Real-IP
                value: "${remote_addr}"
              - name: X-Request-ID
                value: "${request_id}"
            # Pass original headers (default true)
            pass: true
```

---

### Error Handling Examples

#### Example: Custom HTTP Error Pages with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: custom-errors-ingress
  annotations:
    nginx.ingress.kubernetes.io/custom-http-errors: "404,500,502,503"
    nginx.ingress.kubernetes.io/default-backend: "custom-error-pages"
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

**Official NGINX VirtualServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-app
spec:
  host: myapp.example.com
  upstreams:
    - name: my-backend
      service: my-service
      port: 80
    - name: error-backend
      service: custom-error-pages
      port: 80
  routes:
    - path: /
      action:
        pass: my-backend
      errorPages:
        # Redirect to custom error page service
        - codes: [404]
          redirect:
            url: ${scheme}://${host}/errors/404.html
            code: 302
        # Return inline error response
        - codes: [500, 502, 503]
          return:
            code: 503
            type: text/html
            body: |
              <html>
                <head><title>Service Unavailable</title></head>
                <body>
                  <h1>Service Temporarily Unavailable</h1>
                  <p>Please try again later.</p>
                </body>
              </html>
            headers:
              - name: Retry-After
                value: "30"
        # Proxy to error backend
        - codes: [504]
          redirect:
            url: "http://error-backend/timeout.html"
            code: 302
```

---

### WAF / Security Examples

#### Example: WAF with NGINX App Protect Policy CRD

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/enable-modsecurity: "true"
  nginx.ingress.kubernetes.io/enable-owasp-core-rules: "true"
  nginx.ingress.kubernetes.io/modsecurity-snippet: |
    SecRuleEngine On
    SecRequestBodyAccess On
```

**Official NGINX WAF Policy CRD (requires NGINX App Protect):**

```yaml
# App Protect Policy (defines WAF rules)
apiVersion: appprotect.f5.com/v1beta1
kind: APPolicy
metadata:
  name: waf-policy
spec:
  policy:
    name: strict-policy
    template:
      name: POLICY_TEMPLATE_NGINX_BASE
    applicationLanguage: utf-8
    enforcementMode: blocking
    signature-sets:
      - name: All Signatures
        block: true
        alarm: true
    blocking-settings:
      violations:
        - name: VIOL_ATTACK_SIGNATURE
          alarm: true
          block: true
---
# App Protect Log Configuration
apiVersion: appprotect.f5.com/v1beta1
kind: APLogConf
metadata:
  name: waf-log-config
spec:
  filter:
    request_type: all
  content:
    format: default
    max_request_size: any
    max_message_size: 10k
---
# WAF Policy referencing App Protect resources
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: waf-policy
spec:
  waf:
    enable: true
    apPolicy: "default/waf-policy"
    securityLogs:
      - enable: true
        apLogConf: "default/waf-log-config"
        logDest: "syslog:server=syslog-svc.logging.svc.cluster.local:514"
---
# VirtualServer with WAF policy
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: secure-app
spec:
  host: secure.example.com
  policies:
    - name: waf-policy
  upstreams:
    - name: app-backend
      service: app-service
      port: 80
  routes:
    - path: /
      action:
        pass: app-backend
```

---

### Caching Examples

#### Example: Proxy Caching with Policy CRD

**Kubernetes NGINX Ingress:**
> No direct equivalent - requires custom snippets

**Official NGINX Cache Policy CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: cache-policy
spec:
  cache:
    cacheZoneName: my-cache
    cacheZoneSize: 100m
    maxSize: 1g
    inactive: 60m
    time: 10m
    allowedCodes:
      - 200
      - 301
      - 302
    allowedMethods:
      - GET
      - HEAD
    cacheKey: "${scheme}${host}${request_uri}"
    cacheRevalidate: true
    cacheBackgroundUpdate: true
    cacheUseStale:
      - error
      - timeout
      - updating
      - http_500
      - http_502
      - http_503
    conditions:
      bypass:
        - "$cookie_nocache"
        - "$arg_nocache"
      noCache:
        - "$http_pragma"
    lock:
      enable: true
      timeout: 5s
      age: 5s
---
# VirtualServer with caching
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: cached-app
spec:
  host: static.example.com
  policies:
    - name: cache-policy
  upstreams:
    - name: static-backend
      service: static-content
      port: 80
  routes:
    - path: /
      action:
        pass: static-backend
```

---

### Traffic Splitting Examples (Continued)

#### Example: A/B Testing with Multiple Conditions

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/canary: "true"
  nginx.ingress.kubernetes.io/canary-by-header: "X-Test-Group"
  nginx.ingress.kubernetes.io/canary-by-header-pattern: "group-[a-z]"
```

**Official NGINX VirtualServer with Complex Matching:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: ab-test
spec:
  host: app.example.com
  upstreams:
    - name: version-a
      service: app-v1
      port: 80
    - name: version-b
      service: app-v2
      port: 80
    - name: version-c
      service: app-v3
      port: 80
  routes:
    - path: /
      matches:
        # Match header X-Test-Group: group-a
        - conditions:
            - header: X-Test-Group
              value: "group-a"
          action:
            pass: version-a
        # Match header X-Test-Group: group-b
        - conditions:
            - header: X-Test-Group
              value: "group-b"
          action:
            pass: version-b
        # Match cookie ab_test=variant_c
        - conditions:
            - cookie: ab_test
              value: "variant_c"
          action:
            pass: version-c
        # Match query parameter ?version=beta
        - conditions:
            - argument: version
              value: "beta"
          action:
            pass: version-b
      # Default action with traffic split
      splits:
        - weight: 70
          action:
            pass: version-a
        - weight: 30
          action:
            pass: version-b
```

---

#### Example: Canary with Header Pattern Matching

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/canary: "true"
  nginx.ingress.kubernetes.io/canary-by-header-pattern: ".*-beta$"
```

**Official NGINX VirtualServer with Variable Condition:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: canary-pattern
spec:
  host: app.example.com
  upstreams:
    - name: stable
      service: app-stable
      port: 80
    - name: beta
      service: app-beta
      port: 80
  routes:
    - path: /
      matches:
        # Use NGINX variable matching for pattern
        - conditions:
            - variable: $http_x_release
              value: "~.*-beta$"
          action:
            pass: beta
      action:
        pass: stable
```

---

### VirtualServerRoute Examples

#### Example: Delegating Routes to Different Teams

**Official NGINX VirtualServer with VirtualServerRoute:**

```yaml
# Main VirtualServer (owned by platform team)
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: main-app
  namespace: platform
spec:
  host: app.example.com
  tls:
    secret: tls-secret
  upstreams:
    - name: default-backend
      service: default-service
      port: 80
  routes:
    # Delegate /api to api-team namespace
    - path: /api
      route: api-team/api-routes
    # Delegate /web to web-team namespace
    - path: /web
      route: web-team/web-routes
    # Handle root path locally
    - path: /
      action:
        pass: default-backend
---
# VirtualServerRoute for API team
apiVersion: k8s.nginx.org/v1
kind: VirtualServerRoute
metadata:
  name: api-routes
  namespace: api-team
spec:
  host: app.example.com
  upstreams:
    - name: api-v1
      service: api-v1-service
      port: 80
    - name: api-v2
      service: api-v2-service
      port: 80
  subroutes:
    - path: /api/v1
      action:
        pass: api-v1
    - path: /api/v2
      action:
        pass: api-v2
    - path: /api/health
      action:
        return:
          code: 200
          type: application/json
          body: '{"status": "healthy"}'
---
# VirtualServerRoute for Web team
apiVersion: k8s.nginx.org/v1
kind: VirtualServerRoute
metadata:
  name: web-routes
  namespace: web-team
spec:
  host: app.example.com
  upstreams:
    - name: web-frontend
      service: frontend-service
      port: 80
  subroutes:
    - path: /web
      action:
        proxy:
          upstream: web-frontend
          rewritePath: /
```

---

### CORS Configuration Example

#### Example: CORS with Server Snippets

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/enable-cors: "true"
  nginx.ingress.kubernetes.io/cors-allow-origin: "https://frontend.example.com"
  nginx.ingress.kubernetes.io/cors-allow-methods: "GET, POST, PUT, DELETE, OPTIONS"
  nginx.ingress.kubernetes.io/cors-allow-headers: "Authorization, Content-Type"
  nginx.ingress.kubernetes.io/cors-allow-credentials: "true"
  nginx.ingress.kubernetes.io/cors-max-age: "86400"
```

**Official NGINX VirtualServer with Snippets:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: cors-app
spec:
  host: api.example.com
  server-snippets: |
    # CORS headers
    set $cors_origin "";
    set $cors_credentials "";

    if ($http_origin ~* "^https://(frontend\.example\.com|app\.example\.com)$") {
        set $cors_origin $http_origin;
        set $cors_credentials "true";
    }

    add_header Access-Control-Allow-Origin $cors_origin always;
    add_header Access-Control-Allow-Credentials $cors_credentials always;
    add_header Access-Control-Allow-Methods "GET, POST, PUT, DELETE, OPTIONS" always;
    add_header Access-Control-Allow-Headers "Authorization, Content-Type, X-Requested-With" always;
    add_header Access-Control-Max-Age 86400 always;

    # Handle preflight requests
    if ($request_method = OPTIONS) {
        return 204;
    }
  upstreams:
    - name: api-backend
      service: api-service
      port: 80
  routes:
    - path: /
      action:
        pass: api-backend
```

---

### gRPC Configuration Example

#### Example: gRPC Backend with VirtualServer

**Kubernetes NGINX Ingress (annotation-based):**

```yaml
annotations:
  nginx.ingress.kubernetes.io/backend-protocol: "GRPC"
  nginx.ingress.kubernetes.io/ssl-redirect: "true"
```

**Official NGINX VirtualServer CRD:**

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: grpc-app
spec:
  host: grpc.example.com
  tls:
    secret: tls-secret
  upstreams:
    - name: grpc-backend
      service: grpc-service
      port: 50051
      type: grpc  # Enable gRPC protocol
      tls:
        enable: true  # Use GRPCS if backend requires TLS
      # gRPC-specific health checks (NGINX Plus)
      healthCheck:
        enable: true
        grpcService: grpc.health.v1.Health
        grpcStatus: 0  # SERVING status
        interval: 5s
        fails: 3
        passes: 2
  routes:
    - path: /
      action:
        pass: grpc-backend
```

---

## Features Requiring CRDs (No Annotation Equivalent)

The following advanced features in Official NGINX Ingress Controller are **only available via CRDs**:

### VirtualServer CRD Features

| Feature | VirtualServer Field | Description |
| --------- | --------------------- | ------------- |
| Traffic Splitting | `routes[].splits[]` | Weighted traffic distribution across upstreams |
| Content-Based Routing | `routes[].matches[]` | Route based on headers, cookies, arguments, variables |
| Advanced Health Checks | `upstreams[].healthCheck` | Active health checks with custom endpoints (NGINX Plus) |
| Session Cookies | `upstreams[].sessionCookie` | Cookie-based session persistence (NGINX Plus) |
| Custom Error Responses | `routes[].errorPages[]` | Custom responses for specific error codes |
| Path Rewriting | `routes[].action.proxy.rewritePath` | Rewrite URI with regex capture groups |
| Request Header Modification | `routes[].action.proxy.requestHeaders` | Add/modify/remove request headers |
| Response Header Modification | `routes[].action.proxy.responseHeaders` | Add/hide/ignore response headers |
| Inline Responses | `routes[].action.return` | Return static content without backend |
| Route Delegation | `routes[].route` | Delegate routes to VirtualServerRoute in other namespaces |
| Custom Upstream Settings | `upstreams[].*` | Per-upstream timeouts, buffers, connection limits |
| Backup Upstreams | `upstreams[].backup` | Fallback service when primary is unavailable |
| Upstream Subselector | `upstreams[].subselector` | Select pod subset using labels |
| HTTP Snippets | `http-snippets` | Custom NGINX config in http context |
| Server Snippets | `server-snippets` | Custom NGINX config in server context |
| Location Snippets | `routes[].location-snippets` | Custom NGINX config in location context |

### Policy CRD Features

| Feature | Policy Type | Description |
| --------- | ------------ | ------------- |
| JWT Authentication | `jwt` | Validate JWTs against secrets or JWKS URIs (NGINX Plus) |
| OIDC Authentication | `oidc` | OpenID Connect integration (NGINX Plus) |
| API Key Authentication | `apiKey` | Validate API keys in headers or query params |
| Basic Authentication | `basicAuth` | HTTP Basic authentication with htpasswd |
| Rate Limiting | `rateLimit` | Advanced rate limiting with conditions and scaling |
| Access Control | `accessControl` | IP-based allow/deny lists |
| Client mTLS | `ingressMTLS` | Client certificate verification |
| Backend mTLS | `egressMTLS` | Backend certificate authentication |
| Proxy Caching | `cache` | Response caching configuration |
| WAF | `waf` | NGINX App Protect WAF integration |

### TransportServer CRD Features

| Feature | TransportServer Field | Description |
| --------- | ---------------------- | ------------- |
| TCP Load Balancing | `listener.protocol: TCP` | Layer 4 TCP load balancing |
| UDP Load Balancing | `listener.protocol: UDP` | Layer 4 UDP load balancing |
| TLS Passthrough | `listener.protocol: TLS_PASSTHROUGH` | Pass encrypted traffic directly to backend |
| TCP Health Checks | `upstreams[].healthCheck` | TCP-level health checking |
| Session Persistence | `sessionParameters` | Persistent connections for stateful protocols |

### GlobalConfiguration CRD Features

| Feature | GlobalConfiguration Field | Description |
| --------- | --------------------------- | ------------- |
| Custom Listeners | `listeners[]` | Define custom HTTP/HTTPS/TCP/UDP listeners |
| Custom Ports | `listeners[].port` | Listen on non-standard ports |
| TLS Passthrough Listener | `listeners[].protocol: TLS_PASSTHROUGH` | Enable SSL passthrough capability |

---

## TransportServer Examples (TCP/UDP)

### Example: TCP Load Balancing

**Kubernetes NGINX Ingress:**

```yaml
annotations:
  nginx.ingress.kubernetes.io/stream-snippet: |
    server {
        listen 3306;
        proxy_pass mysql-backend;
    }
```

**Official NGINX TransportServer CRD:**

```yaml
# GlobalConfiguration to define the listener
apiVersion: k8s.nginx.org/v1
kind: GlobalConfiguration
metadata:
  name: nginx-configuration
  namespace: nginx-ingress
spec:
  listeners:
    - name: mysql-tcp
      port: 3306
      protocol: TCP
---
# TransportServer for MySQL
apiVersion: k8s.nginx.org/v1
kind: TransportServer
metadata:
  name: mysql-transport
spec:
  listener:
    name: mysql-tcp
    protocol: TCP
  upstreams:
    - name: mysql-backend
      service: mysql-service
      port: 3306
      maxFails: 3
      maxConns: 100
      failTimeout: 10s
      healthCheck:
        enable: true
        interval: 10s
        timeout: 5s
        passes: 2
        fails: 3
  upstreamParameters:
    udpRequests: 0
    udpResponses: 0
  action:
    pass: mysql-backend
```

---

#### Example: UDP Load Balancing (DNS)

**Official NGINX TransportServer CRD:**

```yaml
# GlobalConfiguration for UDP listener
apiVersion: k8s.nginx.org/v1
kind: GlobalConfiguration
metadata:
  name: nginx-configuration
  namespace: nginx-ingress
spec:
  listeners:
    - name: dns-udp
      port: 53
      protocol: UDP
---
# TransportServer for DNS
apiVersion: k8s.nginx.org/v1
kind: TransportServer
metadata:
  name: dns-transport
spec:
  listener:
    name: dns-udp
    protocol: UDP
  upstreams:
    - name: dns-backend
      service: coredns
      port: 53
  upstreamParameters:
    udpRequests: 1
    udpResponses: 1
  sessionParameters:
    timeout: 60s
  action:
    pass: dns-backend
```

---

## Installing CRDs

Before using VirtualServer, VirtualServerRoute, Policy, TransportServer, or GlobalConfiguration resources, you must install the CRDs:

```bash
# Install all NGINX Ingress Controller CRDs
kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v3.4.0/deploy/crds.yaml

# Or install individual CRDs
kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v3.4.0/deploy/crds/k8s.nginx.org_virtualservers.yaml
kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v3.4.0/deploy/crds/k8s.nginx.org_virtualserverroutes.yaml
kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v3.4.0/deploy/crds/k8s.nginx.org_policies.yaml
kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v3.4.0/deploy/crds/k8s.nginx.org_transportservers.yaml
kubectl apply -f https://raw.githubusercontent.com/nginx/kubernetes-ingress/v3.4.0/deploy/crds/k8s.nginx.org_globalconfigurations.yaml
```

---

## Quick Reference: Annotation to CRD Mapping

| When You See This K8s NGINX Annotation | Use This Official NGINX Resource |
| ---------------------------------------- | ------------------------------ |
| `canary*` annotations | VirtualServer with `splits[]` or `matches[]` |
| `auth-tls-*` annotations | Policy with `ingressMTLS` |
| `proxy-ssl-*` annotations | Policy with `egressMTLS` |
| `limit-rps`, `limit-rpm` | Policy with `rateLimit` |
| `whitelist-source-range` | Policy with `accessControl.allow` |
| `denylist-source-range` | Policy with `accessControl.deny` |
| `auth-type: basic` | Policy with `basicAuth` |
| `auth-url` (external auth) | Policy with `oidc` or snippets |
| `ssl-passthrough` | TransportServer with `TLS_PASSTHROUGH` |
| `stream-snippet` | TransportServer or GlobalConfiguration |
| `custom-http-errors` | VirtualServer with `errorPages[]` |
| `rewrite-target` with regex | VirtualServer with `proxy.rewritePath` |
| `session-cookie-*` | VirtualServer with `sessionCookie` |
| `enable-modsecurity` | Policy with `waf` |

---

## Migration Checklist

1. **Identify annotation prefix**: Change `nginx.ingress.kubernetes.io/` to `nginx.org/` or `nginx.com/`
2. **Review deprecated features**: Some features require CRDs instead of annotations
3. **Install CRDs**: Apply VirtualServer, VirtualServerRoute, Policy, and TransportServer CRDs
4. **Test in staging**: Validate configuration before production migration
5. **Update monitoring**: Adjust dashboards and alerts for new controller metrics

## Additional Resources

- [Official NGINX Ingress Controller Documentation](https://docs.nginx.com/nginx-ingress-controller/)
- [VirtualServer and VirtualServerRoute Resources](https://docs.nginx.com/nginx-ingress-controller/configuration/virtualserver-and-virtualserverroute-resources/)
- [Policy Resource](https://docs.nginx.com/nginx-ingress-controller/configuration/policy-resource/)
- [Annotations Reference](https://docs.nginx.com/nginx-ingress-controller/configuration/ingress-resources/advanced-configuration-with-annotations/)
