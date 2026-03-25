---
title: "Structured Concurrency Proposal Aims to Fix JavaScript's Async Cleanup Problem"
description: "A new proposal by TC39 contributor bakkot introduces structured concurrency primitives to JavaScript by enhancing AbortController and AbortSignal. The proposal adds disposable controllers, guaranteed cleanup callbacks, and an AsyncAbortController whose abort() returns a Promise that settles only after all cleanup completes — bringing patterns from Python and Java to the web platform."
pubDate: 2026-03-25T12:00:00Z
tags: ["javascript", "tc39", "async", "structured-concurrency", "abortcontroller", "web-standards"]
author: "AI Editor"
category: "Language"
---

## The Async Cleanup Gap

JavaScript has had `AbortController` and `AbortSignal` since 2017, giving developers a standard way to cancel fetch requests and other async operations. But cancellation is only half the problem. What happens *after* you abort — cleanup of open connections, rollback of partial writes, teardown of child tasks — remains entirely manual and fundamentally unreliable.

A new proposal from TC39 contributor **bakkot** aims to close that gap by bringing **structured concurrency** to JavaScript. The approach doesn't introduce new syntax. Instead, it extends the existing `AbortController` and `AbortSignal` APIs with disposable semantics, guaranteed cleanup callbacks, and a new `AsyncAbortController` that waits for all cleanup to finish before proceeding. If adopted, it would be the most significant change to JavaScript's async story since `async`/`await`.

## What Structured Concurrency Actually Means

The term comes from a 2018 blog post by Nathaniel J. Smith (creator of Python's Trio library) and has since been adopted by Java's Project Loom, Kotlin's coroutine scopes, and Swift's task groups. The core idea has three rules:

- **Child tasks are bound to a lexical scope** — they can't outlive the block that created them.
- **The scope doesn't exit until all children complete or are cancelled** — including any async cleanup they need to perform.
- **An error in one child cancels the others** — preventing orphaned tasks from running against stale assumptions.

JavaScript's current `AbortController` pattern violates all three. Signals are passed manually and can be ignored. `catch` blocks execute before other tasks are cancelled. And there's no mechanism to wait for async cleanup to complete — `controller.abort()` is fire-and-forget.

## The Proposal: Three Layers

The proposal builds in three incremental layers, each useful on its own but composing into full structured concurrency when combined.

### addAbortCallback()

An addition to `AbortSignal` (already proposed as a WHATWG DOM PR) that registers cleanup callbacks with stronger guarantees than `addEventListener('abort', ...)`. The key difference: callbacks registered via `addAbortCallback` are guaranteed to run exactly when `controller.abort()` is called, and they return a token with `Symbol.dispose` support for automatic removal:

```javascript
using token = signal.addAbortCallback(() => {
  connection.close();
});
// token is automatically disposed when the scope exits,
// preventing the callback from leaking
```

This eliminates the most common `AbortSignal` footgun: forgetting to remove event listeners, which keeps signal references alive and prevents garbage collection.

### DisposableAbortController

A variant of `AbortController` that implements `Symbol.dispose`, enabling use with the `using` declaration from TC39's **Explicit Resource Management** proposal (now shipping in V8, SpiderMonkey, and JavaScriptCore):

```javascript
{
  using controller = new AbortController.Disposable();
  const { signal } = controller;

  fetch('/api/users', { signal });
  fetch('/api/orders', { signal });
}
// controller.abort() is called automatically when the block exits
```

This binds task lifetime to lexical scope — the first rule of structured concurrency. Tasks launched with the signal are cancelled when the block exits, whether normally or via an exception.

### AsyncAbortController

The final layer adds async cleanup. `AsyncAbortController` implements `Symbol.asyncDispose` and changes the semantics of `abort()`: instead of returning `undefined`, it returns a **Promise that settles only after all registered cleanup callbacks have completed** — including async ones:

```javascript
{
  await using controller = new AbortController.AsyncDisposable();
  const { signal } = controller;

  signal.addAbortCallback(async () => {
    await db.rollback();
    await cache.invalidate();
  });

  await doWork(signal);
}
// Block waits for db.rollback() and cache.invalidate() to finish
// before execution continues past the closing brace
```

This is the piece that makes the pattern genuinely structured. Without it, async cleanup races against whatever code runs after the abort — the exact bug that structured concurrency is designed to prevent.

## Why Now

Two things make this proposal viable today that weren't true two years ago.

First, **Explicit Resource Management** (`using` and `await using`) has shipped across all major engines. The proposal leans heavily on this infrastructure — without it, there's no way to bind controller lifetime to lexical scope without new syntax.

Second, the rise of **AI agent frameworks and background task orchestration** in web applications has made the cleanup problem acute. An AI agent that spawns multiple tool calls, each with their own fetch requests and database connections, needs reliable cancellation and cleanup when the user navigates away or the parent task times out. Today, developers build this with fragile manual plumbing. Structured concurrency would make it a one-liner.

## Current Status and What's Missing

The proposal is currently in the **exploratory stage** — it's a GitHub repository with a detailed explainer, not yet a formal TC39 proposal with a stage number. The `addAbortCallback` piece is furthest along, with an open PR against the WHATWG DOM spec. The `DisposableAbortController` and `AsyncAbortController` pieces depend on community consensus around the API shape.

The main open question is error handling. If multiple cleanup callbacks throw, which error propagates? The current sketch uses `AggregateError`, but the semantics of multiple async failures during abort are genuinely hard to get right — as Java's virtual threads and Python's task groups have discovered.

## Why This Matters

JavaScript is the last major language with first-class async support that lacks structured concurrency primitives. Python has `asyncio.TaskGroup`, Java has `StructuredTaskScope`, Kotlin has `coroutineScope`, and Swift has task groups. JavaScript developers have been building the same patterns by hand, with `try`/`finally` blocks and manual signal threading, for nearly a decade.

This proposal doesn't ask for new syntax or new runtime concepts. It extends APIs that already exist, using language features that already shipped. That pragmatism — building on `AbortController` rather than replacing it — is what gives it a realistic path to adoption. Whether it reaches TC39's formal proposal process depends on the WHATWG's reception of `addAbortCallback`, but the direction is clear: JavaScript's async model is getting the cleanup guarantees it's been missing since Promises landed in ES2015.
