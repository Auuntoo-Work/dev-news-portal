---
title: "Package Managers Embrace Dependency Cooldowns to Fight Supply Chain Attacks"
description: "In an unprecedented wave of coordinated adoption, npm, pnpm, Yarn, Bun, Deno, uv, and pip have all shipped dependency cooldown features — letting developers delay installing freshly published package versions for a configurable grace period. The push follows the LiteLLM supply chain attack and a growing consensus that the publish-to-install pipeline is fundamentally too fast."
pubDate: 2026-03-25T12:00:00Z
tags: ["supply-chain-security", "package-managers", "npm", "pip", "uv", "pnpm", "dependency-management", "open-source-security"]
author: "AI Editor"
category: "DevOps"
---

## The Problem: Publishing Is Instant, Detection Is Not

When a malicious actor publishes a compromised package to npm, PyPI, or any other registry, it becomes installable worldwide in seconds. If Dependabot, Renovate, or a CI pipeline runs in that window, the malicious code lands in projects without human review. Every major supply chain attack exploits this fundamental property: **publishing and distribution are the same act**, and there is no grace period between them.

The LiteLLM supply chain attack on March 24 — where compromised PyPI packages stole credentials from thousands of AI infrastructure deployments — made this painfully concrete. But the response from the package manager ecosystem had been building for months. Andrew Nesbitt's March 4 blog post, "Package Managers Need to Cool Down," catalyzed a movement that was already underway across multiple ecosystems.

The idea is simple: don't install package versions that were published less than a configurable number of days ago. If a malicious package is typically detected and removed within 24–72 hours, even a 7-day cooldown provides a comfortable safety margin.

## Who Shipped What

Seven package managers across three language ecosystems have now implemented or announced cooldown features. The timeline is remarkable for its speed and coordination:

- **pnpm 10.16** (September 2025) — Shipped `minimumReleaseAge` in `.npmrc`, covering both direct and transitive dependencies. Includes `minimumReleaseAgeExclude` for packages you trust enough to skip.
- **Yarn 4.10.0** (September 2025) — Added `npmMinimalAgeGate` in `.yarnrc.yml`, measured in minutes, with `npmPreapprovedPackages` for exemptions.
- **Bun 1.3** (October 2025) — Added `minimumReleaseAge` via `bunfig.toml`.
- **uv 0.9.17** (December 2025) — Extended the existing `--exclude-newer` flag with relative duration support (e.g., `7d`) and added per-package overrides via `exclude-newer-package`.
- **pip 26.0** (January 2026) — Shipped `--uploaded-prior-to`, though currently limited to absolute timestamps. Relative duration support is under discussion.
- **npm 11.10.0** (February 2026) — Shipped `min-release-age` in `.npmrc`, completing the JavaScript ecosystem trifecta.
- **Deno** — Added `--minimum-dependency-age` for `deno update` and `deno outdated`.

Cargo has an RFC in progress, with registry-side infrastructure for cooldowns stabilized in Cargo 1.94, slated for release in March 2026.

## How to Enable Cooldowns Today

The configuration is straightforward across all supported tools. Here's what it looks like in the major ecosystems:

**npm / pnpm** (`.npmrc`):
```ini
min-release-age=7d
```

**Yarn** (`.yarnrc.yml`):
```yaml
npmMinimalAgeGate: 10080  # minutes (7 days)
```

**Bun** (`bunfig.toml`):
```toml
[install]
minimumReleaseAge = "7d"
```

**uv** (`pyproject.toml`):
```toml
[tool.uv]
exclude-newer = "7d"
```

The key distinction across implementations is **absolute timestamps vs. relative durations**. An absolute timestamp (like `2026-03-18T00:00:00Z`) pins resolution to a moment in time — useful for reproducibility. A relative duration (like `7d`) creates a sliding window that always excludes recently published packages regardless of when the build runs — useful for security. Most implementations now support both modes, with pip being the notable exception still limited to absolute timestamps.

## What Cooldowns Don't Solve

Cooldowns are not a silver bullet. They operate on a simple assumption: that malicious packages will be detected and yanked before the cooldown window expires. This holds for most attacks — the median time-to-detection for malicious npm packages is under 48 hours — but it won't catch a sophisticated attacker who maintains a compromised package for weeks or months before activating a payload.

Cooldowns also create friction for legitimate use cases:

- **Zero-day security patches** — If a critical vulnerability fix is published, a 7-day cooldown means you can't install it immediately. Most implementations address this with exemption lists.
- **Rapid iteration** — Teams publishing and consuming internal packages in the same organization may find cooldowns disruptive. Scoped exemptions (by package name or publisher) help here.
- **Greenfield projects** — Starting a new project means every dependency is "new" from the tool's perspective. Initial setup may require temporarily disabling the cooldown.

## Why This Coordination Matters

Five package managers shipping the same class of feature within six months is unprecedented. Package managers are typically competitors — they differentiate on speed, disk usage, and developer experience. Security features rarely drive adoption. But the wave of supply chain attacks in 2025 and early 2026 — from the Shai-Hulud npm worm affecting 180+ packages to the LiteLLM compromise — created enough pressure to align the ecosystem around a common defense.

The feature is also notable for what it reveals about the limits of registry-side solutions. PyPI, npm, and other registries have invested heavily in malware detection, but detection will always lag behind publication. Cooldowns shift part of the defense to the client side, giving the ecosystem time to catch up.

## What You Should Do

If you maintain any project with external dependencies — which is effectively every project — enable dependency cooldowns now. A 7-day window is a reasonable default. Add exemptions for packages you trust or need to update urgently. And review your CI/CD pipelines: if Dependabot or Renovate auto-merges dependency updates, cooldowns should be the minimum safeguard between a malicious publish and your production environment.

The publish-to-install pipeline has been running without a speed limit for decades. It's about time someone added a cooldown.
