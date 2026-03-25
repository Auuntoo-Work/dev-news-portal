---
title: "GitHub Copilot Can Now Write Code Directly in Your Pull Requests"
description: "GitHub's Copilot coding agent now lets developers mention @copilot in any pull request comment to request code changes — from fixing failing CI tests to addressing reviewer feedback. The agent runs in its own cloud environment, validates changes against existing tests and linters, and pushes commits directly to the PR branch."
pubDate: 2026-03-25T20:00:00Z
tags: ["github-copilot", "ai-coding", "code-review", "pull-requests", "developer-tools", "automation"]
author: "AI Editor"
category: "AI"
---

## What Happened

On March 24, GitHub announced that its Copilot coding agent can now make changes directly to existing pull requests. Developers can mention `@copilot` in any PR comment — review threads, inline comments, or the main conversation — and ask it to write code, fix failing tests, or address reviewer feedback. The agent pushes commits directly to the PR branch, making Copilot a collaborative participant in the code review workflow rather than just a code generation tool.

Previously, Copilot's agent mode could only respond to requests by opening a new pull request on a separate branch. That created friction: developers had to context-switch between the original PR and the Copilot-generated one, manually reconcile changes, and manage additional branches. The new behavior eliminates that overhead entirely.

## How It Works

When you tag `@copilot` in a PR comment with a request, the agent spins up a secure cloud-based development environment. It clones the repository, checks out the PR branch, analyzes the codebase, and makes the requested changes. Before pushing anything, it validates its work against the project's existing test suite and linter configuration.

The interaction model is straightforward:

- **Fix failing CI** — `@copilot Fix the failing tests` triggers the agent to analyze test failures and push a fix
- **Address review comments** — `@copilot Address this comment` lets the agent respond to specific reviewer feedback with code changes
- **Add coverage** — `@copilot Add a unit test covering the case when the model argument is missing` generates targeted test cases
- **General changes** — Any code modification request that can be described in natural language

The agent operates iteratively. It can recognize errors in its own output, self-correct, and even infer additional tasks that weren't explicitly specified but are necessary for the primary request to work. If a requested change requires updating an import statement or modifying a type definition elsewhere, the agent handles that automatically.

```
# Example PR comment interaction
@copilot Fix the failing type check in ci and add
a test for the new validateInput function
```

The agent processes this in its cloud sandbox, runs the project's type checker and test suite to verify the fix, and pushes one or more commits to the PR branch — all without requiring the developer to leave the review interface.

## Who Gets Access

The feature is available on **all paid Copilot plans** — Individual, Business, and Enterprise. However, there's an important administrative requirement: for Copilot Business and Enterprise users, an organization administrator must explicitly enable the coding agent before team members can use it.

Developers who prefer the previous behavior can still request `@copilot open a PR` to have the agent create a separate pull request instead of committing directly to the current branch.

## Why This Matters

This update represents a fundamental shift in how AI assistants participate in the development lifecycle. Until now, AI coding tools have primarily operated in two modes: autocomplete in the editor and autonomous PR generation from issues. The PR comment interface creates a third mode — **AI as a code review participant** — that sits at the exact point where developer collaboration already happens.

The implications are significant for team workflows:

- **Faster review cycles** — Reviewers can request changes and have them implemented immediately, rather than waiting for the author to context-switch back to the branch
- **Lower friction for small fixes** — Typos, missing tests, linter violations, and minor refactors can be resolved without the PR author touching their local environment
- **Reduced CI iteration time** — Failing checks can be addressed by the agent directly, cutting down on the push-wait-fix-push cycle that slows many PRs

The competitive landscape is also shifting. This capability puts GitHub Copilot in direct competition with tools like Claude Code and Cursor that offer agentic coding workflows. The key differentiator is integration depth — Copilot operates natively within the GitHub PR interface, where millions of developers already conduct code review.

## What to Watch For

The feature is powerful but introduces questions that teams should consider before enabling it broadly:

- **Commit hygiene** — Agent-generated commits land directly on PR branches. Teams will need to decide whether these require the same review scrutiny as human-authored commits.
- **Trust boundaries** — The agent runs in a cloud sandbox with access to the repository. Organizations with strict security requirements should evaluate what code and secrets the agent can access during execution.
- **Review workflow changes** — When a reviewer can ask an AI to implement their feedback, the dynamics of code review shift. The line between "requesting changes" and "making changes" blurs.

## The Bigger Picture

GitHub is steadily transforming Copilot from a code completion tool into an end-to-end development agent. The trajectory is clear: from inline suggestions, to chat-based editing, to autonomous PR creation from issues, and now to real-time participation in pull request reviews.

For development teams, this update is worth evaluating immediately — not because it replaces human judgment in code review, but because it handles the mechanical work that slows reviews down. Fixing a failing lint check or adding a missing null check shouldn't require a round trip through a developer's local environment. If an AI agent can handle that in seconds from a PR comment, the review cycle gets meaningfully faster.

The question is no longer whether AI will participate in code review. It's how teams will adapt their workflows to make that participation effective.
