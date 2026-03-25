---
title: "ProofShot Gives AI Coding Agents Eyes to Visually Verify the UI They Build"
description: "ProofShot is a new open-source CLI tool that plugs into any AI coding agent and adds a visual verification workflow — testing in a real browser, recording video proof, collecting errors, and bundling everything into a standalone HTML report. It hit 128 points and 88 comments on Hacker News as developers debate whether vibe coding finally has an accountability layer."
pubDate: 2026-03-25T12:00:00Z
tags: ["ai-agents", "developer-tools", "ui-testing", "visual-verification", "coding-assistants", "open-source", "cli", "vibe-coding"]
author: "AI Editor"
category: "AI"
---

## The Gap in AI-Assisted Development

AI coding agents can write functional code. Claude Code, Cursor, Codex, and Gemini CLI can scaffold components, wire up state management, and fix broken builds — all from a terminal prompt. But none of them can look at what they built and tell you whether it actually looks right.

That's the problem ProofShot solves. Created by developer AmElmo, ProofShot is an open-source CLI tool that gives any AI coding agent visual verification capabilities — launching a real browser, navigating the UI, capturing screenshots, recording video, collecting console and server errors, and bundling everything into a self-contained HTML report. The project hit **128 points and 88 comments** on Hacker News within hours of its Show HN launch, sparking a sharp debate about whether existing tools already handle this and whether vibe coding needs its own verification layer.

## How It Works

ProofShot operates in three steps. First, you start a session that launches your dev server and a headless browser:

```bash
proofshot start --run "npm run dev" --port 3000 --description "Verify login flow"
```

Then the AI agent interacts with the page through agent-browser — a browser automation layer built by Vercel Labs — navigating, clicking, filling forms, and capturing screenshots at key moments. Finally, stopping the session generates the evidence bundle:

```bash
proofshot stop
```

Each session produces a timestamped folder containing a **WebM video recording**, an **interactive HTML viewer** with timeline scrubbing, a **Markdown summary** with extracted errors and screenshots, step-by-step PNG captures, and synchronized console and server logs. The HTML viewer is fully self-contained — no server needed, just open the file.

The tool is built in **TypeScript**, ships as an npm package, and installs globally with `npm install -g proofshot`. Running `proofshot install` auto-detects which AI agents are installed and deploys skill files to each one, so the agent knows how to invoke the verification workflow without manual configuration.

## Agent-Agnostic by Design

ProofShot supports **Claude Code, Cursor, Codex, Gemini CLI, and Windsurf** out of the box, deploying rules or skill files to each agent's configuration directory. But the design is deliberately generic — any agent that can run shell commands can use ProofShot.

The skill files teach agents when and how to trigger verification. After generating or modifying UI code, the agent starts a ProofShot session, walks through the relevant flows in the browser, and stops the session. The resulting report gives the human reviewer video evidence of what the agent built, without needing to spin up the dev server and click through manually.

For teams using pull requests as their review surface, `proofshot pr` uploads the artifacts directly to a GitHub PR — posting formatted comments with embedded screenshots and video. When `ffmpeg` is available, it converts WebM to MP4 for broader browser compatibility.

## Error Detection Across Languages

Beyond visual verification, ProofShot includes automatic error detection across **10+ programming languages** — JavaScript, Python, Ruby, Go, Java, Rust, PHP, and C#/.NET among them. It pattern-matches against server logs and browser console output, extracting and categorizing errors into the summary report.

This matters because AI agents frequently generate code that compiles and runs but throws runtime warnings or non-fatal errors that cascade into visual bugs. By surfacing these alongside the video evidence, ProofShot gives reviewers the full picture — not just what the UI looks like, but what went wrong underneath.

The tool also includes `proofshot diff` for **visual regression testing** — comparing screenshots against baselines to catch unintended changes across sessions.

## The Community Debate

The Hacker News discussion split along a familiar line. Supporters praised the practical value: one commenter called screenshots on PRs "incredibly helpful as a reviewer" and suggested running ProofShot against every open pull request. Another highlighted how it validates the vibe coding workflow where developers prompt an agent and review the output.

Skeptics argued the problem is already solved. **Playwright MCP**, **Chrome DevTools MCP**, and Claude Code's native browser integration were all cited as overlapping alternatives. One commenter flatly called ProofShot "not necessary" given existing capabilities.

The creator's response was direct: ProofShot isn't a browser automation tool. It's a **session management and evidence bundling layer** on top of browser automation. The distinction matters — Playwright MCP lets an agent interact with a browser, but it doesn't record the session, collect errors across languages, generate a standalone report, or post that report to a PR. ProofShot wraps all of that into a single workflow.

## Limitations

ProofShot is at **version 1.3.2** with 31 commits and 305 GitHub stars — early and moving fast. The current limitations are real:

- **Desktop and mobile only through headless Chrome** — No native mobile device testing or responsive viewport simulation beyond what the browser supports
- **Video format** — WebM output requires ffmpeg for MP4 conversion, which not every CI environment has
- **No cloud dashboard** — All artifacts are local by default, which is a feature for privacy but a limitation for distributed teams

Desktop app and mobile device support were the top community requests, though the maintainer indicated these would require community contributions.

## Why This Matters

The rise of vibe coding — prompting an AI agent to build UI and reviewing the result — is creating a verification gap. Agents can pass type checks, linting, and unit tests while producing output that looks completely wrong. Screenshot testing frameworks exist, but they require setup and maintenance that defeats the speed advantage of agent-driven development.

ProofShot's bet is that the verification step should be as automated as the generation step. Whether it becomes the standard tool for this or gets absorbed into the agents themselves, the pattern it establishes — record everything, bundle the evidence, let humans review — is likely here to stay.

ProofShot is MIT-licensed and available now via `npm install -g proofshot`.
