---
title: "OPKSSH Goes Open Source: SSH Authentication Gets Single Sign-On via OpenID Connect"
description: "The OpenPubkey project has open-sourced OPKSSH, a tool that replaces static SSH keys with short-lived, OIDC-backed credentials. Developers can now run 'opkssh login' to authenticate via Google, Microsoft, or GitLab SSO and get an SSH key that expires in 24 hours — eliminating the need for manual key rotation, authorized_keys management, and long-lived credentials sitting on developer laptops."
pubDate: 2026-03-25T12:00:00Z
tags: ["ssh", "authentication", "openid-connect", "sso", "security", "devops", "open-source", "identity"]
author: "AI Editor"
category: "DevOps"
---

## What OPKSSH Does

OPKSSH — OpenPubkey SSH — is a command-line tool that brings OpenID Connect single sign-on to SSH. Instead of generating a static SSH key pair, adding the public key to `~/.ssh/authorized_keys` on every server, and hoping someone remembers to rotate it, developers run a single command:

```bash
opkssh login
```

This opens a browser-based OIDC authentication flow with a supported identity provider — Google, Microsoft/Azure, GitLab, hello.dev, or Authelia. After authentication, OPKSSH generates an ephemeral SSH key pair and writes it to `~/.ssh/id_ecdsa`. The key expires after 24 hours by default. When it expires, you run `opkssh login` again.

The tool was originally developed by BastionZero, which was acquired by Cloudflare. Cloudflare has now gifted the OPKSSH codebase to the OpenPubkey project under the Linux Foundation. While OpenPubkey itself has been open source since 2023, OPKSSH remained closed-source until this release.

## How It Works Under the Hood

OPKSSH doesn't replace SSH or require a custom SSH server. It works with standard OpenSSH by leveraging two existing mechanisms:

- **PK Tokens in SSH public keys** — When you run `opkssh login`, the tool generates an SSH public key that embeds a PK Token. This token contains a standard OpenID Connect ID Token, binding your identity (e.g., `alice@example.com`) to the ephemeral key.
- **AuthorizedKeysCommand** — Instead of checking `authorized_keys` files, the SSH server is configured to call OPKSSH as a verification program. When a connection comes in, `sshd` passes the public key to `opkssh verify`, which validates the embedded PK Token against the identity provider's public keys.

The server-side configuration is straightforward. Administrators install OPKSSH on the server and configure `sshd_config` to use it:

```
AuthorizedKeysCommand /usr/local/bin/opkssh verify %u %k %t
AuthorizedKeysCommandUser opkssh
```

Two policy files control access:

- `/etc/opk/providers` — An allowlist of OpenID Providers and Client IDs the server trusts
- `/etc/opk/auth_id` — Maps identities to Linux user accounts, defining who can SSH as which user

This means access control shifts from "which public keys are in authorized_keys" to "which email addresses or group memberships are allowed" — a model that maps directly to how organizations already manage identity.

## Why This Matters Now

Static SSH keys have been a known security liability for decades. They don't expire unless you manually configure expiration. They accumulate on servers as engineers join and leave teams. They sit unencrypted on developer laptops. And when a machine is compromised, every server that trusts that key is exposed.

The timing of OPKSSH's open-source release is particularly relevant. Just days ago, the LiteLLM supply chain attack demonstrated how compromised credentials — including SSH keys — can be harvested and used for lateral movement across infrastructure. OPKSSH directly addresses this class of risk:

- **No long-lived credentials** — Keys expire after 24 hours by default, with configurable policies (12h, 24h, 48h, 1 week, or tied to OIDC token lifetime)
- **No authorized_keys management** — Access is controlled through identity provider groups and email addresses, not file-level key distribution
- **No key rotation ceremonies** — Ephemeral keys are generated on demand, eliminating the operational burden of scheduled rotations
- **Centralized revocation** — Disable a user's identity provider account and SSH access is immediately revoked across all servers

## Getting Started

OPKSSH is written in Go and available on GitHub under the `openpubkey/opkssh` repository. Installation on both client and server is a single binary. The project provides packages for major Linux distributions and macOS.

For a quick proof of concept, the client-side setup takes about 30 seconds:

```bash
# Install opkssh
brew install opkssh  # macOS
# or download from GitHub releases

# Authenticate and generate ephemeral SSH key
opkssh login

# SSH as normal — the ephemeral key is used automatically
ssh user@server.example.com
```

Server-side setup requires installing the binary, running `opkssh setup`, and configuring the provider and identity policy files. The project's documentation includes step-by-step guides for each supported identity provider.

## The Broader Shift

OPKSSH represents a larger trend in infrastructure security: the move from static secrets to ephemeral, identity-based credentials. Tools like HashiCorp Vault have been doing this for database credentials and API keys for years. Cloud providers have pushed workload identity for service-to-service authentication. But SSH — one of the oldest and most ubiquitous protocols in infrastructure — has largely been left behind.

With OPKSSH now open source and backed by the Linux Foundation through OpenPubkey, teams have a credible path to eliminate static SSH keys entirely. For organizations already running OIDC-based identity infrastructure, the integration cost is minimal and the security improvement is significant. The era of `ssh-keygen` followed by copying public keys into authorized_keys files may finally be coming to an end.
