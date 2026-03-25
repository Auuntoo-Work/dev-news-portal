---
title: "Video.js v10 Beta Drops with 88% Smaller Bundles and a Ground-Up Rewrite"
description: "After 16 years, the most widely-used open source web video player ships its biggest release ever. Video.js v10 is a ground-up rewrite that merges four separate open source players into a single modern framework, slashing default bundle sizes by 88%."
pubDate: 2026-03-25T18:00:00Z
tags: ["video.js", "javascript", "typescript", "react", "open-source", "web-video", "performance", "bundle-size"]
author: "AI Editor"
category: "Web"
---

## Four Players Become One

Video.js has been the default open source video player on the web since 2010. It powers tens of billions of video plays monthly across millions of sites. But after 16 years, the jQuery-era architecture had accumulated serious weight — and serious limitations.

Video.js v10, released as a beta on March 10, 2026, isn't an incremental update. It's a complete ground-up rewrite that merges **four separate open source video players** — Video.js, Plyr, Vidstack, and Media Chrome — into a single unified framework. Combined, those projects represent over 75,000 GitHub stars and some of the most widely-deployed media code on the web.

The rewrite was led by engineers from Mux, the video infrastructure company that has stewarded Video.js since acquiring it, alongside Sam Potts (creator of Plyr, 29,000 GitHub stars) and contributors from the Vidstack and Media Chrome projects. It's a rare consolidation in the open source world — competing projects voluntarily merging rather than fragmenting further.

## The Bundle Size Story

The headline number is real. Video.js v10's default HTML video player ships at **97.4 kB minified (25.1 kB gzipped)** — an 88% reduction from v8. But the improvements go deeper than a single benchmark:

- **React video player** — 62.0 kB minified, 18.0 kB gzipped
- **Background video (React)** — 10.7 kB minified, 3.5 kB gzipped
- **Simple HLS with SPF engine** — 144.6 kB minified, 38.7 kB gzipped (only 19% the size of v8 with ABR)
- **SPF engine alone** — 38.5 kB minified, 12.1 kB gzipped

The size reduction comes from a fundamental architectural shift. In v8, everything was bundled together — controls, streaming engines, accessibility features, audio handling — whether you used it or not. In v10, the player is composed of independent, tree-shakeable modules. If you don't need audio controls, that code doesn't ship. If you don't need adaptive bitrate streaming, you can drop the ABR engine entirely.

## A Composable Architecture

The old Video.js was a monolithic controller. State management, UI rendering, and media playback were tightly coupled in ways that made customization painful and bundle optimization impossible.

v10 splits these concerns into three independent layers: **State**, **UI**, and **Media**. Each communicates through API contracts rather than internal references, and each is independently swappable. Want to use your own state management? Replace the state layer. Need a custom UI? Build on top of the unstyled primitives — inspired by libraries like Base UI and Radix — without fighting the player's opinions about how controls should look.

The streaming layer introduces **SPF (Streaming Processor Framework)**, a modular system for building and composing streaming engines. Rather than shipping a single monolithic HLS/DASH implementation, SPF lets developers compose exactly the streaming capabilities they need.

## First-Class React and TypeScript

v10 ships with a dedicated `@videojs/react` package that treats React as a first-class target rather than an afterthought wrapper. A minimal React player with a play button comes in at under 5 kB gzipped.

The entire codebase is TypeScript-first, with full type coverage across the public API. Combined with Tailwind support for styling, the developer experience aligns with how modern frontend teams actually work — not how they worked in 2010.

## New Skins by the Creator of Plyr

Sam Potts, whose Plyr player was known for its clean, minimal design, created the new default skins for v10. The beta ships with two options: a **default skin** featuring a frosted glass aesthetic with refined controls and smooth animations, and a **minimal skin** designed as a clean starting point for custom designs. Error dialogs are styled to match each skin, and both prioritize thoughtful interaction design over visual clutter.

## Three Presets Out of the Box

Recognizing that most video use cases fall into a handful of patterns, v10 ships with three presets:

- **Default video** — Standard website playback with full controls
- **Default audio** — Podcast-style player optimized for audio content
- **Background video** — Layout-only embed with no controls or audio, designed for hero sections and visual backgrounds

Each preset is a starting configuration, not a constraint. Every component within a preset can be swapped, removed, or extended.

## What's Missing — and What's Coming

This is a beta, and the team is transparent about current limitations. APIs are not yet stable. Settings menus, advertising support, and some advanced features are planned for later in 2026. The general availability release is targeted for mid-2026.

The project has also leaned into AI-readiness in an unusual way: all documentation ships in Markdown format alongside an `llms.txt` file, and the repository includes a growing set of AI-oriented tooling — a nod to the reality that many developers now interact with documentation through AI assistants rather than reading docs directly.

## Why This Matters

Video.js v10 matters because web video infrastructure is one of those invisible layers that affects nearly every user on the internet. An 88% bundle reduction across millions of sites translates to real-world improvements in page load times, Core Web Vitals scores, and user experience — especially on mobile and in emerging markets where bandwidth is constrained.

The consolidation of four major players into one framework also simplifies a fragmented ecosystem. Instead of choosing between Video.js for features, Plyr for design, Vidstack for modern architecture, or Media Chrome for composability, developers now get all four in a single package.

The v10 beta is available now via npm. If you're running Video.js in production, the migration path is worth exploring — but given the API instability, most teams should wait for GA before committing to the upgrade.
