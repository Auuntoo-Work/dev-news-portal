---
title: "Kubernetes Ingress-NGINX Controller Officially Archived: The End of an Era for K8s Networking"
description: "The Kubernetes project has officially archived ingress-nginx — the most widely-used ingress controller in the ecosystem — effective March 24, 2026. With no further releases, bugfixes, or security patches, teams running Kubernetes must now migrate to Gateway API implementations or face unpatched infrastructure."
pubDate: 2026-03-25T16:00:00Z
tags: ["kubernetes", "ingress-nginx", "gateway-api", "cloud-native", "infrastructure", "migration"]
author: "AI Editor"
category: "DevOps"
---

## The Repository Goes Read-Only

On March 24, 2026, the Kubernetes project officially archived the `kubernetes/ingress-nginx` repository on GitHub. The repo is now read-only. There will be no further releases, no bugfixes, and no security patches. Existing installation artifacts — Helm charts, container images, manifests — remain available, but they are frozen in time.

This wasn't a surprise. SIG Network announced the retirement in November 2025, giving teams a four-month runway to plan their migration. But for a component that powers roughly **half of all cloud-native environments** — according to internal Datadog research — the reality of an archived repository still hits hard.

Ingress-NGINX has been the default ingress controller for most Kubernetes deployments since the early days of the project. It handled TLS termination, path-based routing, rate limiting, and a sprawling set of NGINX-specific annotations that teams wired deep into their infrastructure. For many organizations, it's the single component that sits between the internet and every service in their cluster.

## Why It Was Retired

The retirement wasn't driven by technical obsolescence — it was driven by **maintainer burnout**. Despite its massive adoption by companies of all sizes, the ingress-nginx project never received the contributors it needed. The Kubernetes Steering Committee and Security Response Committee issued a joint statement in January 2026 making the situation clear: the project's maintenance burden had become unsustainable with its existing contributor base.

Security was a growing concern. Ingress-nginx had accumulated several critical CVEs over the past two years, and the small maintainer team was struggling to keep up with both feature requests and vulnerability remediation. Rather than let the project drift into an unmaintained-but-widely-deployed state — where unpatched CVEs would silently affect millions of clusters — the Kubernetes project made the decision to archive it cleanly.

The broader technical context also played a role. The Kubernetes **Gateway API** reached GA status and has been positioned as the official successor to the legacy Ingress API. Gateway API provides a more expressive, role-oriented model that splits the concerns of infrastructure provisioning (Gateway) and traffic routing (HTTPRoute) into separate resources — something the original Ingress spec was never designed to do.

## The Migration Path

The timing of the archival coincides with a significant milestone: the Kubernetes project released **Ingress2Gateway 1.0** on March 20, 2026 — just four days before the archive date. This is the official migration tool, and the 1.0 release represents a major upgrade in coverage.

Ingress2Gateway now supports over **30 common ingress-nginx annotations**, including CORS configuration, backend TLS, regex path matching, URL rewriting, and custom headers. Each supported annotation is backed by controller-level integration tests that verify behavioral equivalence between the original ingress-nginx configuration and the generated Gateway API resources.

The recommended migration approach is a **parallel deployment**:

- Deploy a Gateway API controller alongside your existing ingress-nginx installation
- Each controller gets a separate external IP address
- Run `ingress2gateway` to convert your Ingress resources to Gateway and HTTPRoute objects
- Validate the new configuration against production traffic patterns without affecting live services
- Cut DNS over to the new controller once validated
- Decommission ingress-nginx

```yaml
# Example: An Ingress resource and its Gateway API equivalent

# Before (Ingress)
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80

# After (Gateway API)
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: my-app
spec:
  parentRefs:
  - name: my-gateway
  hostnames:
  - app.example.com
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /api
    filters:
    - type: URLRewrite
      urlRewrite:
        path:
          type: ReplacePrefixMatch
          replacePrefixMatch: /
    backendRefs:
    - name: api-service
      port: 80
```

## Choosing a Gateway API Implementation

Gateway API is a specification, not a product. You need to choose a controller that implements it. The current landscape includes several production-ready options:

- **Envoy Gateway** — The reference implementation backed by the Envoy project. Strong community momentum and CNCF backing.
- **NGINX Gateway Fabric** — F5's Gateway API implementation using NGINX as the data plane. The closest migration path for teams already familiar with NGINX behavior.
- **Cilium Gateway API** — Built on eBPF, offering kernel-level packet processing. Attractive for teams already running Cilium as their CNI.
- **Istio** — Supports Gateway API natively as of recent versions, suitable for teams that want a full service mesh.
- **KGateway (formerly Gloo Gateway)** — Solo.io's Envoy-based implementation with extended policy support.

Each has different operational characteristics. Teams should evaluate based on their existing infrastructure, performance requirements, and the specific annotations they rely on from ingress-nginx.

## What Happens If You Do Nothing

Your existing ingress-nginx deployments will continue to function. The controller doesn't have a kill switch, and the container images aren't being deleted. But you are now running unpatched infrastructure. Any CVE discovered in ingress-nginx from this point forward will not be fixed by the upstream project. Given that ingress controllers sit at the network edge — directly exposed to the internet — this is not a risk most security teams will be comfortable carrying for long.

The clock is ticking. The migration tooling is mature, the alternative implementations are production-ready, and the Kubernetes ecosystem has made its direction clear. The Ingress API served the community well for years, but Gateway API is the future of Kubernetes networking. The archival of ingress-nginx makes that transition non-optional.
