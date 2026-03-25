---
title: "httpxyz: Why Python's Most Popular HTTP Library Just Got Forked"
description: "Developer Michiel has forked httpx — the async HTTP client used by OpenAI, Anthropic, and thousands of Python projects — into httpxyz, citing stalled maintenance, hidden issues, and breaking changes. With no httpx release since November 2024 and its 1.0 promised since 2020 still unshipped, major SDK vendors had already added version guards. The fork, hosted on Codeberg, aims to provide the stability the Python ecosystem needs from such a critical dependency."
pubDate: 2026-03-25T12:00:00Z
tags: ["python", "open-source", "httpx", "dependency-management", "ecosystem"]
author: "AI Editor"
category: "Language"
---

## What Happened

httpx — the async-capable HTTP client that has become a foundational dependency across the Python ecosystem — has been forked. Developer Michiel announced **httpxyz** on March 25, a maintenance fork hosted on Codeberg that aims to provide the stability and responsiveness that httpx has lacked for over a year. Co-maintained with Sander Wegter, the project bills itself with a pointed motto: "Move a little faster and not break things."

The fork comes after a prolonged period of stalled maintenance on the original httpx project, maintained by Tom Christie under the Encode organization. No new release has been published since November 2024, despite merged bug fixes sitting unreleased in the repository. For a library depended on by the OpenAI and Anthropic Python SDKs — and by extension, the vast majority of production AI applications — that silence has become untenable.

## Why httpx Stalled

The problems go beyond a missed release cycle. Michiel's account describes a pattern of declining project health:

- **Hidden issues and disabled discussions** — GitHub Issues and Discussions were turned off on the httpx repository, making it harder for users to report bugs or contribute fixes. This isn't isolated: the same maintainer disabled these features on Django REST Framework as well.
- **Merged fixes never released** — Michiel contributed zstd content decoding support that was merged and shipped, but when a bug was found, the fix was submitted, merged, and then left unreleased indefinitely. Repeated requests for a patch release went unanswered.
- **The perpetual 1.0** — httpx 1.0 has been "in the planning" for over two years, with a patch release draft pending for more than a year. Despite the library's pre-1.0 status, breaking changes were introduced in minor version bumps — the worst of both worlds for downstream consumers.
- **The 0.28.0 breakage** — In November 2024, httpx 0.28.0 removed the deprecated `proxies` parameter from `Client.__init__()`, immediately breaking both the OpenAI and Anthropic Python SDKs. The change was technically valid under semver for a 0.x release, but it demonstrated the real-world cost of breaking changes in a library this widely depended on.

## The SDK Vendor Problem

The httpx maintenance situation created a specific and measurable risk for the AI ecosystem. Both the OpenAI and Anthropic Python SDKs depend directly on httpx for all HTTP communication. When httpx 0.28.0 broke the `proxies` API, both teams had to scramble to patch their SDKs.

More tellingly, both vendors have since added **version guards** in their `pyproject.toml` files to prevent installation of a hypothetical httpx 1.0. They're pinning against a version that doesn't exist yet — a defensive measure that speaks volumes about the level of trust in httpx's release process. When your two largest downstream consumers are preemptively guarding against your next major release, the project has a governance problem.

This isn't just about OpenAI and Anthropic. httpx is a transitive dependency for thousands of Python projects through these SDKs alone. Any instability in httpx ripples across the entire AI tooling stack — from LangChain to LiteLLM to countless internal applications.

## What httpxyz Offers

httpxyz is designed as a **drop-in replacement** with identical APIs, exceptions, and behavior. Migration is minimal:

```python
# Option 1: Alias import
import httpxyz as httpx

# Option 2: Direct usage
import httpxyz
client = httpxyz.AsyncClient()
```

Installation follows the same pattern as httpx:

```bash
pip install httpxyz
pip install httpxyz[http2]  # with HTTP/2 support
```

The project's stated goals are deliberately narrow: **bug fixes only, no breaking API changes**. This is a stability fork, not a feature fork. The maintainers want to ship the fixes that have been sitting in httpx's repository and provide a reliable release cadence for the ecosystem.

httpxyz is hosted on **Codeberg**, a non-profit, community-driven Git forge built on Forgejo. The choice is intentional — part of a broader movement to reduce the open-source ecosystem's dependency on GitHub as a single point of failure. Issues and discussions are open, and the project explicitly invites community contributions.

## The Bigger Picture

The httpxyz fork is the latest example of a pattern that's becoming increasingly common in the Python ecosystem: critical infrastructure maintained by a single person or small team, with no succession plan and no accountability when maintenance stalls. It's the same dynamic that led to the `left-pad` incident in JavaScript, the OpenSSL Heartbleed crisis, and more recently, the xz backdoor in the Linux ecosystem.

What makes this case notable is the visibility of the downstream impact. When httpx stalls, it's not an obscure utility library that breaks — it's the HTTP layer for every major AI SDK. The fact that OpenAI and Anthropic both had to add defensive version pins to their published packages is a signal that the Python ecosystem's dependency on httpx had become a single point of failure.

Whether httpxyz gains enough traction to become the de facto replacement remains to be seen. The project is days old with 11 stars on Codeberg. But the underlying problem it addresses — a critical dependency with stalled maintenance and no governance structure — isn't going away. For teams that depend on httpx through their AI SDK stack, httpxyz offers something httpx currently doesn't: a maintainer who's answering the door.
