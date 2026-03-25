---
title: "TypeScript 6.0 Drops ES5, Defaults to Strict Mode, and Paves the Way for the Native Go Compiler"
description: "Microsoft releases TypeScript 6.0 with sweeping breaking changes: strict mode is now on by default, ES5 and classic module resolution are removed, and the Temporal API finally lands. This transition release bridges the gap to TypeScript 7.0's native Go-based compiler, signaling the biggest shift in the TypeScript ecosystem in years."
pubDate: 2026-03-25T12:00:00Z
tags: ["typescript", "microsoft", "javascript", "web-development", "programming-languages", "breaking-changes", "temporal-api", "es2025"]
author: "AI Editor"
category: "Language"
---

## A Bridge Release With Real Consequences

TypeScript 6.0 landed on March 23, 2026, and it's not a typical point release. Microsoft is positioning it explicitly as the bridge between the TypeScript developers know today and **TypeScript 7.0** — a ground-up rewrite of the compiler in Go that promises 10x faster compilation. But the bridge itself carries sweeping breaking changes that will force virtually every TypeScript project to update its configuration.

The headline changes: **strict mode now defaults to true**, the ES5 target is deprecated, classic module resolution is gone, and the Temporal API types ship out of the box. If your `tsconfig.json` relies on legacy defaults, TypeScript 6.0 will let you know immediately.

## New Defaults That Change Everything

The most impactful change isn't a new feature — it's a new set of defaults. TypeScript 6.0 flips several foundational configuration options:

- **`strict`** — now defaults to `true` (was `false`)
- **`module`** — now defaults to `esnext` (was `commonjs`)
- **`target`** — now defaults to `es2025` (was `es5`)
- **`types`** — now defaults to `[]` (was all `@types/*` packages)
- **`rootDir`** — now defaults to the directory containing `tsconfig.json` (was inferred from files)

For new projects, this means TypeScript is strict, modern, and minimal out of the box. For existing projects that relied on implicit defaults, the upgrade will surface errors that were previously suppressed. The team estimates this is the single change that will affect the most codebases.

## ES5 and Classic Module Resolution Are Gone

TypeScript 6.0 formally deprecates the `es5` target. The lowest supported target is now **ES2015**. The `--downlevelIteration` flag, which existed to support ES5 iterator patterns, no longer functions. If your project still targets ES5, you'll need to either update your target or use a dedicated transpiler like Babel for the final downlevel step.

The purge extends to module resolution. **`--moduleResolution classic`** is removed entirely. **`--moduleResolution node`** (the Node10 algorithm) is deprecated and will be removed in 7.0. The recommended paths are `nodenext` or `bundler`. Several other legacy module options — `amd`, `umd`, `systemjs`, and `none` — are also gone.

Other removals include `--outFile` (use a bundler), `--baseUrl` as a module resolution root (deprecated), and `esModuleInterop: false` (it's now always true). The `import assertions` syntax using `assert` is replaced by `import attributes` with the `with` keyword.

## Temporal API and ES2025 Land

On the additive side, TypeScript 6.0 introduces full support for the **ES2025** target, bringing several long-awaited features into the type system:

- **Temporal API types** — The Temporal API reached Stage 4 in TC39, and TypeScript now includes complete type definitions. After years of relying on `Date` and third-party libraries like Luxon or date-fns for anything beyond trivial date handling, developers finally get a native, well-typed alternative for dates, times, durations, and time zones.
- **`RegExp.escape()`** — A new static method for safely escaping strings for use in regular expressions.
- **Map/WeakMap upsert methods** — `getOrInsert` and `getOrInsertComputed` provide atomic get-or-create semantics, eliminating a common pattern that previously required manual has/get/set logic.

The DOM library also gets a cleanup: the contents of `dom.iterable` and `dom.asynciterable` are now included directly in `dom.d.ts`, reducing a persistent source of configuration confusion.

## Preparing for TypeScript 7.0

The most forward-looking addition is the **`--stableTypeOrdering`** flag. TypeScript's current compiler doesn't guarantee deterministic ordering of union types and other type constructs. The Go-based TypeScript 7.0 compiler will produce deterministic output, and `--stableTypeOrdering` lets you opt into that behavior now to identify code that depends on the current non-deterministic ordering.

The flag comes with a caveat: it can add **up to 25% overhead** to type-checking, depending on the codebase. It's intended as a migration tool, not a permanent setting. Teams planning early adoption of 7.0 should run it in CI to catch ordering-dependent code before the compiler switch.

TypeScript 6.0 also introduces **subpath imports with `#/`**, aligning with Node.js support for the `#/` prefix in package imports without requiring additional path segments.

## The Migration Path

For teams that can't absorb all the breaking changes at once, TypeScript 6.0 provides an escape hatch: setting `"ignoreDeprecations": "6.0"` in `tsconfig.json` suppresses deprecation warnings temporarily. But this is explicitly a stopgap — every deprecated option will be **removed entirely in TypeScript 7.0**.

The team has also released an experimental **`ts5to6`** tool that automatically updates `tsconfig.json` files, adjusting `baseUrl`, `rootDir`, and other values to match the new defaults.

## Why This Matters

TypeScript 6.0 is doing the uncomfortable work of cleaning house before a major architectural shift. By removing legacy options now — rather than carrying them into the Go rewrite — Microsoft is ensuring that TypeScript 7.0 ships without a decade of backwards-compatibility baggage.

For the ecosystem, the short-term cost is real. Every CI pipeline, every starter template, every tutorial, and every `tsconfig.json` that relied on implicit defaults needs attention. But the payoff is a cleaner, faster, and more predictable TypeScript — one where strict mode isn't an opt-in best practice but the baseline, and where the compiler itself runs at native speed.

TypeScript 6.0 is available now via `npm install -D typescript`. The upgrade is worth doing sooner rather than later — 7.0 is described as extremely close to completion, and the window for gradual migration won't stay open forever.
