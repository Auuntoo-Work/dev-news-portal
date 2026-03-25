---
title: "Mozilla AI Launches cq: A Stack Overflow Built for AI Coding Agents"
description: "Mozilla AI has open-sourced cq, a shared knowledge commons where AI coding agents can query past learnings, contribute discoveries, and avoid repeating the same mistakes in isolation. With Stack Overflow question volume down dramatically since 2014, cq fills the gap with agent-to-agent knowledge sharing via MCP servers, IDE plugins, and multi-agent trust mechanisms."
pubDate: 2026-03-25T12:00:00Z
tags: ["ai-agents", "mozilla", "open-source", "mcp", "knowledge-sharing", "developer-tools", "claude-code"]
author: "AI Editor"
category: "AI"
---

## The Problem With Isolated Agents

AI coding agents are getting remarkably good at solving problems. They're also remarkably good at solving the same problems over and over again, independently, burning tokens and time on failures that another agent already figured out last week.

Every developer using Claude Code, Copilot, or similar tools has experienced this: an agent hits an obscure dependency conflict, spends 15 minutes reasoning through it, eventually finds a workaround — and then next Tuesday, on a different machine or in a different repo, the exact same agent hits the exact same problem and starts from scratch. There's no shared memory. No collective learning. Each agent session is an island.

Mozilla AI thinks that's a solvable problem. Their new open-source project **cq** — named after both "colloquy" (a formal conversation) and the radio general call "CQ" (seeking any station) — is a shared knowledge commons designed specifically for AI agents to query, contribute to, and validate collectively.

## How cq Works

The core loop is straightforward. Before tackling an unfamiliar task, an agent queries the cq commons for existing solutions. If another agent has already encountered and resolved the same issue — a tricky build error, an API migration gotcha, a dependency version conflict — the querying agent gets that knowledge immediately instead of rediscovering it through trial and error.

When an agent discovers a novel approach or workaround, it proposes that knowledge back to the system. Other agents can then validate the contribution by confirming it works in their own contexts, or flag it as outdated when environments change.

Installation for Claude Code is a two-line operation:

```bash
claude plugin marketplace add mozilla-ai/cq
claude plugin install cq
```

For OpenCode and other MCP-compatible clients, cq runs as a standard MCP server — clone the repo, run `make install-opencode`, and the agent gains access to the shared knowledge store.

## The Architecture

cq uses a three-tier design that prioritizes local-first operation:

- **Local MCP Server** — A Python/FastMCP server backed by a SQLite database at `~/.cq/local.db`. This handles all queries and contributions locally with zero configuration required.
- **IDE Integration** — Behavioral instructions and post-error auto-queries are wired into Claude Code and OpenCode, so agents automatically check the commons when they hit errors.
- **Team API** — An optional FastAPI server (deployable via Docker) that synchronizes knowledge across an organization. When configured, local discoveries propagate to the team, and team knowledge is available locally.

The local-first approach means cq works out of the box without any network dependency. A solo developer gets value from their own agent's accumulated knowledge across sessions. Teams that deploy the shared API get the network effect of every agent on the team learning from every other agent's experiences.

## Trust Without Authority

The hardest problem in agent-to-agent knowledge sharing isn't storage or retrieval — it's trust. How do you know a piece of knowledge contributed by one agent is actually correct?

cq's approach relies on **multi-agent confirmation** rather than centralized authority. Knowledge gains credibility when multiple agents, working in different codebases and contexts, independently confirm that a solution works. A workaround that succeeds across three separate repositories carries more weight than one that's only been tested once.

The project's roadmap includes confidence scoring, reputation signals, and mechanisms to automatically deprecate knowledge that starts failing as dependencies and APIs evolve. These features are still in development — cq is currently at version 0.4.0 and self-identifies as an "exploratory" proof of concept.

## The Stack Overflow Context

The timing of cq isn't coincidental. Stack Overflow's monthly question volume has fallen from over 200,000 at its 2014 peak to **3,862 in December 2025** — a decline that accelerated sharply after ChatGPT's launch in late 2022. The platform that defined a generation of developer knowledge sharing is no longer where most problem-solving happens.

But the knowledge that Stack Overflow accumulated didn't disappear — it's baked into the training data of the models that replaced it. The gap isn't in historical knowledge. It's in **new knowledge**: the workarounds, migration paths, and configuration fixes that emerge daily as libraries ship breaking changes, APIs deprecate endpoints, and toolchains evolve.

That's the gap cq targets. Not a replacement for Stack Overflow's archive, but a living, agent-maintained knowledge base that stays current because the agents contributing to it are actively working in production codebases.

## What It Can't Do Yet

cq is early. The project has 446 GitHub stars and 10 releases, with the codebase split roughly 74% Python and 21% TypeScript. There are real limitations:

- **IDE support is narrow** — Only Claude Code and OpenCode are supported today. VS Code with Copilot, Cursor, and other popular agent-powered editors aren't integrated yet.
- **Trust mechanisms are nascent** — The multi-agent confirmation system is designed but not fully implemented. Today's knowledge contributions are more of an honor system.
- **No cross-language knowledge graph** — Knowledge is stored as flat entries, not as a structured graph that understands relationships between packages, versions, and platforms.
- **Scaling is unproven** — The SQLite-backed architecture works for individual developers and small teams, but organizational-scale deployment with thousands of agents is untested territory.

Mozilla AI is explicit about the project's status: this is a proof of concept exploring whether collective agent intelligence is viable, not a production-ready platform.

## Why This Matters

The trend line is clear. AI coding agents are becoming the primary interface between developers and their codebases. As that happens, the inefficiency of every agent starting from zero on every problem becomes increasingly costly — not just in compute, but in developer time spent watching agents rediscover known solutions.

cq's bet is that agent-to-agent knowledge sharing will become as fundamental to AI-assisted development as Stack Overflow was to traditional development. The project hit 208 points and 92 comments on Hacker News, suggesting the developer community recognizes the problem even if the solution is still taking shape.

Whether cq itself becomes the standard or simply proves the concept for others to build on, the direction is compelling. Agents that learn from each other will outperform agents that don't. Mozilla AI is building the infrastructure to make that possible.

cq is available now on GitHub under the Apache 2.0 license at `mozilla-ai/cq`.
