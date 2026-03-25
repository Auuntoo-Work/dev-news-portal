---
title: "Hypura Lets You Run LLMs Too Big for Your Mac's Memory — By Treating Your SSD as a Smart Storage Tier"
description: "Hypura is a Rust-based inference scheduler for Apple Silicon that intelligently places model tensors across GPU, RAM, and NVMe storage tiers — letting a 32 GB Mac run a 40 GB model that would otherwise crash."
pubDate: 2026-03-25T12:00:00Z
tags: ["ai", "llm", "apple-silicon", "rust", "inference", "open-source"]
author: "AI Editor"
category: "AI"
---

## The Memory Wall

Running large language models locally on Apple Silicon has a hard ceiling: unified memory. A Mac with 32 GB of RAM cannot naively load a 40 GB model. The OS will swap-thrash until the OOM killer intervenes, and your inference session ends in a crash. Quantization helps — but it only goes so far before quality degrades. And buying a 192 GB M4 Ultra is not everyone's idea of a budget solution.

Hypura, a new open-source Rust project, takes a different approach. Instead of shrinking the model to fit in memory, it **schedules tensor placement across three storage tiers** — GPU, RAM, and NVMe — based on access patterns, bandwidth costs, and hardware capabilities. The result: models that previously crashed on consumer Macs now run successfully, and models that already fit in memory see zero overhead.

## How It Works

Hypura's core insight is that not all tensors are accessed equally. Attention layers, norms, and embeddings are small but touched on every token — they belong on the GPU. Feed-forward network weights are large but accessed less frequently. And in Mixture-of-Experts architectures like Mixtral, only 2 of 8 experts fire per token, meaning 75% of expert weights are idle at any given moment.

Hypura exploits this structure through three inference modes, selected automatically based on model size, architecture, and available hardware:

- **Full-resident** — The model fits entirely in GPU and RAM. No NVMe I/O. Full Metal acceleration. Zero overhead compared to vanilla llama.cpp.
- **Expert-streaming** — For MoE models like Mixtral. Only non-expert tensors (~1 GB) stay pinned to the GPU. Expert weights stream from NVMe on demand through a pool buffer, with router interception identifying which experts are needed before the forward pass begins.
- **Dense FFN-streaming** — For dense models too large for GPU memory, like Llama 70B. Attention and norms (~8 GB) stay on GPU while FFN tensors (~32 GB) stream from NVMe through a dynamically-sized pool buffer.

The NVMe I/O path uses read-only `pread()` calls with `F_NOCACHE`, streaming tensor weights from the GGUF file into memory pools where computation happens. The SSD is treated as cold storage, not working memory — meaning negligible write wear.

## The Numbers

Benchmarks on an M1 Max with 32 GB of unified memory and ~5.1 GB/s NVMe throughput tell the story:

- **Qwen 2.5 14B** — 21 tok/s (full-resident, zero overhead)
- **Mixtral 8x7B** (31 GB) — 2.2 tok/s (expert-streaming mode)
- **Llama 70B** (40 GB) — 0.3 tok/s (dense FFN-streaming)

The critical detail: **vanilla llama.cpp crashes on both Mixtral 8x7B and Llama 70B** on this hardware. Hypura doesn't just run them faster — it makes them runnable at all.

The expert-streaming mode is particularly clever. A neuron cache tracks which expert slices have been loaded, and co-activation tracking predicts which experts will fire next for speculative prefetch. After warmup, the cache achieves a **99.5% hit rate**, effectively eliminating most NVMe I/O for ongoing inference.

## Built on llama.cpp, Not Against It

Hypura isn't a from-scratch inference engine. It wraps llama.cpp through FFI bindings (via the `hypura-sys` crate), adding its scheduling and placement logic on top. This means it inherits llama.cpp's model support, GGUF format compatibility, and Metal GPU acceleration while adding the storage-tier awareness that llama.cpp lacks.

The project also ships an Ollama-compatible HTTP server, making it a drop-in replacement for existing toolchains:

```bash
hypura serve ./mixtral-8x7b.gguf
# POST /api/chat works with any Ollama client
```

Other commands include `hypura profile` for hardware detection, `hypura inspect` to preview a placement plan without loading the model, and `hypura run` for single-prompt or interactive inference.

## Why This Matters

The local LLM landscape on Apple Silicon has been defined by a simple rule: your model must fit in memory, or you quantize until it does. Hypura breaks that constraint by recognizing that modern NVMe drives on Macs deliver 5-7 GB/s of sequential read throughput — enough to stream tensor weights on demand if you're smart about what stays hot and what stays cold.

This is especially relevant as model sizes continue to grow. The gap between what consumer hardware can afford in memory and what frontier models require in parameters keeps widening. Hypura doesn't close that gap entirely — 0.3 tok/s on Llama 70B isn't fast — but it turns "impossible" into "usable for experimentation," which is a meaningful shift for researchers and developers working on constrained hardware.

The project is MIT-licensed and available on GitHub. It requires Rust 1.75+ and CMake to build, and works on any Apple Silicon Mac. For developers already running llama.cpp or Ollama locally, Hypura offers a path to models they couldn't touch before — without buying new hardware.
