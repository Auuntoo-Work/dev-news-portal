---
title: "JetBrains Central: A New Control Plane for AI Coding Agents"
description: "JetBrains has launched Central, an open system designed to be the control and execution plane for agent-driven software production — giving enterprises governance, cost visibility, and orchestration across AI coding agents from any vendor."
pubDate: 2026-03-25T12:00:00Z
tags: ["jetbrains", "ai-agents", "developer-tools", "agentic-development", "ide", "devops", "ai-governance"]
author: "AI Editor"
category: "AI"
---

## The Problem Central Solves

AI coding agents are multiplying fast. Teams are running Claude Code for backend refactors, Copilot for PR reviews, Gemini CLI for quick scripts, and custom agents for internal tooling. Each one operates in its own silo — its own context, its own cost profile, its own audit trail (or lack of one). For individual developers, that's manageable. For enterprises managing hundreds of developers and dozens of agent-powered workflows, it's a governance nightmare.

JetBrains Central, announced March 24, is designed to be the unified control and execution plane for all of it. Rather than building another AI coding agent, JetBrains is building the layer that sits above them — the system that provisions, monitors, governs, and optimizes agent-driven work across an entire organization.

## What Central Actually Does

Central connects tools, agents, and infrastructure into a single production system. Its core capabilities break down into three areas:

- **Governance and Policy Enforcement** — Identity and access management, auditability, cost attribution, and security controls. Administrators can define who can run which agents, on what code, with what permissions, and track exactly what happened.
- **Agent Execution Infrastructure** — Cloud runtimes and compute provisioning that give agents reliable, sandboxed environments to operate in. This is the execution layer that makes agents production-ready rather than laptop-dependent.
- **Semantic Context Layer** — A shared understanding of codebases, architecture, runtime behavior, and organizational knowledge. Instead of each agent starting from scratch with a prompt, Central provides system-level context that enables smarter task routing and better results.

The key design decision is openness. Central supports agents from JetBrains (like Junie) and external ecosystems alike — Claude Agent, Codex, Gemini CLI, or custom-built solutions. Developers initiate workflows from whatever environment they prefer: JetBrains IDEs, third-party editors, CLI tools, or web interfaces.

## Why Interoperability Matters

Most AI coding tools today are tightly coupled to a specific editor or vendor. Cursor runs in Cursor. Copilot runs in VS Code and GitHub. Claude Code runs in the terminal. Central's bet is that enterprises won't standardize on a single agent — they'll use many, and they need a management layer that works across all of them.

This is where JetBrains' existing ecosystem becomes a strategic advantage. The company already ships the Agent Communication Protocol (ACP), an open standard for agent-to-IDE communication. The ACP Agent Registry, launched in January 2026, lets developers discover and connect AI coding agents directly within JetBrains IDEs. Central extends this interoperability from the IDE level to the organizational level.

Team integration is also built in. Agents communicate through existing collaboration tools — Slack, Atlassian products, Linear — keeping workflows embedded in the systems teams already use rather than requiring yet another dashboard.

## The Numbers Behind the Bet

JetBrains' AI Pulse Survey from January 2026, covering 11,000 developers worldwide, provides the context for why Central exists now:

- **90%** of surveyed developers use AI at work
- **22%** currently use AI coding agents specifically
- **66%** of companies plan to adopt agents within 12 months
- Only **13%** use AI across the entire software development lifecycle

That gap between current adoption (22%) and planned adoption (66%) is exactly the problem Central targets. When agent usage triples across an organization, ad-hoc management breaks down. Someone needs to answer questions like: How much are we spending on agent compute? Which agents have access to our proprietary code? What did the agent change, and can we audit it?

## JetBrains Air and the Broader Stack

Central doesn't exist in isolation. JetBrains also recently previewed Air, a lightweight IDE built on the abandoned Fleet codebase, designed specifically for agentic development workflows. Air provides a dedicated workspace for organizing tasks, running agent-assisted workflows, and reviewing results.

The planned Air Team product will coordinate multi-step workflows between humans and agents while maintaining alignment across systems. Together, Central and Air form a two-layer architecture: Air is where individual developers interact with agents, and Central is where organizations manage them.

## Availability and What to Watch

Central's Early Access Program launches in Q2 2026 with a limited group of design partners. JetBrains has also signaled that updated AI pricing for organizations is coming, enabling flexible scaling of AI usage with cost alignment tied to development priorities.

As Hadi Hariri, SVP of Operations at JetBrains, put it: the industry is "increasingly leaning into agents and AI-driven workflows, creating a need for better visibility into costs and governance."

The interesting question isn't whether enterprises need agent governance — they clearly do. It's whether the governance layer will come from the tool vendors themselves (GitHub, JetBrains), from cloud providers (AWS, Azure), or from a new category of standalone platforms. JetBrains is betting that the company developers already trust for their IDE is the right one to trust with the control plane above it.

For teams already juggling multiple AI agents, Central is worth watching closely when EAP opens. The era of ungoverned agent sprawl has an expiration date.
