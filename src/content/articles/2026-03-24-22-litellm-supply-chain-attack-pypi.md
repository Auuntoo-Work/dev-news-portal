---
title: "LiteLLM Supply Chain Attack: Compromised PyPI Packages Inject Credential-Stealing Malware into AI Infrastructure"
description: "Versions 1.82.7 and 1.82.8 of LiteLLM — the popular open-source LLM proxy with 97 million monthly PyPI downloads — were compromised with credential-stealing malware via a poisoned CI/CD pipeline. The incident highlights the growing risk of supply chain attacks targeting AI tooling."
pubDate: 2026-03-24T22:00:00Z
tags: ["security", "supply-chain", "ai", "python", "pypi", "devops"]
author: "AI Editor"
category: "DevOps"
---

## What Happened

On March 24, two malicious versions of the LiteLLM Python package — versions 1.82.7 and 1.82.8 — were published to PyPI containing credential-stealing malware. The compromised code was not present in LiteLLM's official GitHub repository. Both versions have since been removed from PyPI, and version 1.82.6 is confirmed as the last clean release.

LiteLLM is an open-source proxy layer that provides a unified API for calling over 100 LLM providers — OpenAI, Anthropic, Azure, Bedrock, and others — through a single interface. It sees roughly 97 million monthly downloads on PyPI and is used in production by thousands of companies running AI workloads.

## How the Attack Worked

The attack has been attributed to **TeamPCP**, the same threat actor behind the recent compromise of Aqua Security's Trivy container scanner. The attack chain exploited LiteLLM's CI/CD pipeline, which ran Trivy as part of its build process without pinning to a specific version. The compromised Trivy action exfiltrated the project's `PYPI_PUBLISH` token from the GitHub Actions runner environment, giving the attacker the ability to publish new PyPI releases.

The payload is a three-stage attack:

- **Credential Harvesting** — Sweeps the host for SSH keys, cloud credentials, Kubernetes configs, cryptocurrency wallets, `.env` files, and API keys. Harvested secrets are encrypted and exfiltrated to a lookalike domain controlled by the attacker.
- **Kubernetes Lateral Movement** — Deploys privileged pods to every node in accessible clusters, expanding the attack surface across infrastructure.
- **Persistent Backdoor** — Installs a systemd service that polls an external command-and-control server for additional binaries.

Version 1.82.8 escalated the attack further by adding a malicious `.pth` file (`litellm_init.pth`) at the wheel root. Python `.pth` files execute automatically on every Python process startup — not just when LiteLLM is imported. The launcher spawns a background child process via `subprocess.Popen`, making the payload harder to detect.

```python
# Simplified representation of the .pth execution chain
# litellm_init.pth triggers on ANY Python process startup
import subprocess
subprocess.Popen(["python", "-c", base64_decoded_payload],
                 stdout=subprocess.DEVNULL,
                 stderr=subprocess.DEVNULL)
```

## Why This Attack Matters

This is not a typical typosquatting attack or a compromised maintainer account. It's a **cascading supply chain compromise** — a poisoned security tool (Trivy) was used as the vector to compromise downstream projects. LiteLLM was one of the highest-value targets because it sits at the center of AI infrastructure, often running with access to API keys for every LLM provider an organization uses.

The implications are significant:

- **API key exposure** — LiteLLM proxy servers typically hold keys for OpenAI, Anthropic, Azure, and other providers. A single compromised instance could leak credentials worth thousands of dollars in API spend.
- **Kubernetes blast radius** — Many LiteLLM deployments run inside Kubernetes clusters alongside other AI infrastructure. The lateral movement toolkit could propagate across an entire AI platform.
- **Silent execution** — The `.pth` file technique means the malware runs on every Python process, not just when LiteLLM is imported, making it extremely difficult to trace.

## What You Should Do Now

If you use LiteLLM in any environment, take these steps immediately:

- **Pin to version 1.82.6** or earlier in your `requirements.txt` or `pyproject.toml`
- **Audit your systems** for the presence of `litellm_init.pth` in your Python site-packages
- **Rotate all credentials** on any machine where versions 1.82.7 or 1.82.8 were installed — including LLM API keys, cloud credentials, SSH keys, and Kubernetes configs
- **Check for the systemd backdoor** by looking for `sysmon.service` in your systemd units
- **Review CI/CD pipelines** for unpinned dependencies pulled from external registries

## The Bigger Picture

This incident underscores a systemic problem in the AI ecosystem. As AI tooling becomes critical infrastructure, it inherits all the supply chain risks of the broader open-source ecosystem — but with higher stakes. An LLM proxy server is, by design, a centralized vault of API credentials. Compromising one package can expose an organization's entire AI stack.

The Trivy-to-LiteLLM attack chain also demonstrates a maturing threat model: attackers are no longer just targeting end-user packages. They're compromising the security and build tools that other packages depend on, creating cascading blast radiuses that are difficult to contain.

For teams running AI infrastructure in production, the lesson is clear: pin your dependencies, verify your build tools, and treat your LLM proxy servers with the same security posture as your secrets management systems. Because that's exactly what they are.
