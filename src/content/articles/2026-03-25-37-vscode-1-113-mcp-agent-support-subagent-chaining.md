---
title: "VS Code 1.113 Ships MCP Agent Support and Subagent Chaining"
description: "Visual Studio Code 1.113 brings native Model Context Protocol server bridging to Copilot CLI and Claude agents, enables nested subagent invocations for multi-step automations, adds session forking for parallel exploration, and introduces a unified Chat Customizations editor with configurable reasoning effort controls."
pubDate: 2026-03-25T12:00:00Z
tags: ["vscode", "mcp", "ai-agents", "developer-tools", "copilot", "ide", "agentic-development", "model-context-protocol"]
author: "AI Editor"
category: "AI"
---

## What Shipped

Visual Studio Code 1.113, released March 25 as the third weekly release under VS Code's new rapid cadence, makes the world's most popular code editor a full-fledged agent orchestration platform. The headline feature: MCP servers registered in VS Code are now automatically bridged to Copilot CLI and Claude agents, extending tool access that was previously confined to local editor sessions. Alongside MCP bridging, the release ships nested subagent invocations, session forking, a unified Chat Customizations editor, and configurable reasoning effort controls — a dense set of capabilities that collectively redefine how developers interact with AI agents inside their IDE.

This is the first major editor release to offer end-to-end MCP-powered agent orchestration out of the box, and it arrives just one day after JetBrains announced Central, its own control plane for AI coding agents. The timing underscores how quickly the IDE market is converging on agent infrastructure as the next competitive battleground.

## MCP Server Bridging

The Model Context Protocol has been gaining momentum as a standard for connecting AI agents to external tools and data sources. Until now, MCP servers configured in VS Code only worked with the editor's built-in chat agents. Version 1.113 changes that by bridging those same servers to Copilot CLI and Claude agent sessions.

In practice, this means any MCP server you've registered — whether it connects to a database, a documentation system, an API, or a custom internal tool — is now accessible from the terminal through Copilot CLI or Claude agents without additional configuration. The agent running in your terminal inherits the same tool ecosystem as the agent running in your editor.

The release also adds MCP server sandboxing on macOS and Linux. Local MCP servers can now run in restricted processes with limited file system and network access by adding `"sandboxEnabled": true` to the server's entry in `mcp.json`. This addresses a real security concern: as developers connect more external tools to their agents, the blast radius of a compromised or misbehaving server needs to be contained.

## Nested Subagents

Previous VS Code releases introduced subagents — specialized agents that handle discrete subtasks within a larger workflow. Version 1.113 removes the recursion restriction, allowing subagents to invoke other subagents for complex multi-step automations.

The feature is controlled by a new setting, `chat.subagents.allowInvocationsFromSubagents`, which defaults to off. When enabled, a top-level agent can delegate a task to a subagent, which can in turn spin up its own subagent for a sub-subtask. This enables workflows like:

- **Multi-file refactoring** — A planning agent analyzes the codebase, delegates individual file changes to specialized refactoring subagents, each of which can invoke a testing subagent to validate its changes
- **Research-then-implement** — A research subagent gathers context from documentation and code, passes findings to an implementation subagent, which delegates test generation to a third
- **Cross-language coordination** — A frontend subagent and backend subagent each work on their respective codebases while a coordinator agent ensures API contract consistency

The default-off setting is a deliberate guardrail. Unrestricted agent recursion can burn through API tokens quickly and produce unpredictable behavior. Teams should enable it selectively for workflows where the multi-step delegation provides clear value.

## Session Forking

Session forking, now available as an experimental feature in Copilot CLI and Claude agent sessions, lets developers create copies of existing chat conversations at any point in their history. This addresses a common frustration with linear chat interfaces: exploring an alternative approach means either losing your current context or starting over from scratch.

With forking, a developer can reach a decision point — say, choosing between two architectural approaches — and branch the conversation. Each fork maintains full context from the original session up to the fork point, enabling genuine parallel exploration without context loss. The Agent Debug Log panel has also been updated to work with CLI and Claude agent sessions, providing chronological event logs that make it easier to trace what happened in each forked branch.

## Chat Customizations and Reasoning Effort

The new Chat Customizations editor centralizes what was previously scattered across multiple configuration surfaces. It provides a tabbed interface for managing instructions, prompt files, agents, and skills in one place, with embedded syntax highlighting for prompt content.

Alongside the customizations editor, reasoning models now expose a Thinking Effort submenu directly in the model picker. Developers can toggle between Low, Medium, and High reasoning effort without navigating to settings — a small UX improvement that matters when you're iterating quickly and want to balance response quality against latency and cost.

## Why This Release Matters

VS Code commands roughly 70% of the IDE market. When it ships a feature, that feature becomes the de facto standard for how millions of developers work. MCP server bridging across CLI and editor agents doesn't just add a capability — it establishes the expectation that AI agents should have seamless access to the same tools regardless of where they run.

The nested subagent feature is equally significant. Agent orchestration frameworks like LangGraph and CrewAI have offered multi-agent coordination for months, but they require developers to build and manage that orchestration themselves. VS Code 1.113 brings multi-agent chaining directly into the IDE, lowering the barrier from "build your own orchestration layer" to "flip a setting."

Combined with MCP sandboxing, session forking, and the unified customizations editor, this release positions VS Code not just as a code editor with AI features, but as the primary interface for agent-driven development workflows. For teams evaluating their agent infrastructure, VS Code 1.113 is worth a close look — the weekly release cadence means the next iteration is never far behind.
