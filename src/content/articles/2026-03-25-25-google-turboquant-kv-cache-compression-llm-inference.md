---
title: "Google's TurboQuant Achieves 6x LLM KV-Cache Compression with Zero Accuracy Loss"
description: "Google Research unveils TurboQuant, a vector quantization algorithm presented at ICLR 2026 that compresses LLM key-value caches to 3 bits without training or fine-tuning — delivering 6x memory reduction and up to 8x inference speedup on H100 GPUs."
pubDate: 2026-03-25T12:00:00Z
tags: ["ai", "llm", "quantization", "google-research", "inference", "performance", "machine-learning", "iclr-2026"]
author: "AI Editor"
category: "AI"
---

## The KV-Cache Bottleneck

Every time a large language model generates a token, it stores key-value pairs from its attention layers in memory. This KV-cache grows linearly with sequence length and model size, and for long-context workloads it can consume more GPU memory than the model weights themselves. At 128K context windows, a single inference session on a 70B parameter model can burn through tens of gigabytes of VRAM just to hold the cache.

Previous approaches to compressing this cache — standard quantization, eviction policies, sliding windows — all came with trade-offs. Quantize too aggressively and accuracy degrades. Evict tokens and long-range reasoning suffers. The fundamental problem: existing quantization methods introduce bias into attention score calculations, and that bias compounds across layers and tokens.

TurboQuant, a new algorithm from Google Research presented at ICLR 2026, eliminates this trade-off. It compresses KV-caches down to 3 bits per element with **zero accuracy loss** across standard benchmarks — no training, no fine-tuning, no model modification required.

## How TurboQuant Works

TurboQuant is a two-stage compression pipeline that combines two complementary techniques: PolarQuant for primary compression and QJL for residual correction.

**PolarQuant** reimagines how vectors are quantized by converting them from Cartesian to polar coordinates. Instead of quantizing along standard X-Y-Z axes — where the rectangular grid wastes bits on empty corners of the value space — PolarQuant maps vectors onto their radius (magnitude) and angle (direction). This polar representation maps naturally onto a fixed circular grid, eliminating the memory overhead that traditional quantization methods must carry to handle variable-shaped distributions.

The key insight: most of the information in attention key-value vectors lives in their direction, not their magnitude. PolarQuant captures this primary structure using most of its bit budget, achieving strong compression on its own.

**QJL (Quantized Johnson-Lindenstrauss)** handles the residual error that PolarQuant leaves behind. It applies a random projection based on the Johnson-Lindenstrauss lemma — a mathematical guarantee that high-dimensional data can be projected into lower dimensions while preserving distances between points. QJL takes this further by reducing each projected value to a single sign bit (+1 or -1), adding just 1 bit of overhead per dimension.

The critical property: QJL's residual correction is **unbiased**, meaning the errors it introduces cancel out in expectation rather than accumulating. This is what previous quantization methods got wrong — their compression artifacts introduced systematic bias into attention scores, causing accuracy to degrade as context length increased.

## The Numbers

Google benchmarked TurboQuant on Gemma and Llama-3.1-8B-Instruct (Mistral architecture) across five standard evaluation suites: LongBench, Needle In A Haystack, ZeroSCROLLS, RULER, and L-Eval.

The results are striking:

- **6x KV-cache memory reduction** at 3-bit precision with perfect downstream accuracy across all benchmarks
- **Near-lossless performance** on needle-in-haystack long-context tasks — the hardest test for cache compression, since it requires precise retrieval from arbitrary positions in the context
- **Up to 8x inference speedup** over 32-bit unquantized keys on NVIDIA H100 GPUs at 4-bit precision

The speedup comes from two sources: smaller cache means fewer memory reads per attention computation, and the quantized representations enable more efficient vectorized operations on modern GPU hardware. At 4-bit precision, TurboQuant's throughput gains are large enough to meaningfully reduce per-token latency in production serving scenarios.

## Beyond LLMs: Vector Search

TurboQuant's compression approach generalizes beyond KV-caches. Google also evaluated it on vector similarity search — the core operation behind retrieval-augmented generation, recommendation systems, and semantic search.

On the GloVe dataset (200 dimensions), TurboQuant achieved superior recall-at-k ratios compared to established baselines including Product Quantization (PQ) and RabbiQ. This matters because vector databases face the same fundamental tension as KV-caches: you want to store as many vectors as possible in memory for fast retrieval, but compression typically hurts recall quality.

TurboQuant's unbiased compression sidesteps this trade-off, maintaining search quality at significantly reduced memory footprints.

## Why This Matters for Developers

The practical appeal of TurboQuant is its deployment simplicity. It requires **no training, no fine-tuning, and no model modification**. It operates as a post-hoc compression step applied to the KV-cache during inference. This means it can be integrated into existing serving infrastructure — vLLM, TensorRT-LLM, or custom inference stacks — without retraining or modifying model weights.

For teams running LLM inference at scale, a 6x reduction in KV-cache memory directly translates to either serving longer contexts on the same hardware, fitting more concurrent requests per GPU, or both. On H100 clusters where GPU hours cost real money, the 8x throughput improvement at 4-bit precision represents a significant reduction in serving costs.

The technique is also complementary to existing optimizations. Model weight quantization (GPTQ, AWQ) compresses the static weights; TurboQuant compresses the dynamic cache that grows at runtime. You can apply both.

## The Research Behind It

TurboQuant builds on two prior papers from the same Google Research team led by Amir Zandieh and Vahab Mirrokni: PolarQuant (presented at AISTATS 2026) and the original QJL algorithm. The combined TurboQuant paper was published at ICLR 2026 and is available on arXiv.

The broader research direction — achieving extreme compression without accuracy loss through mathematically principled methods rather than learned approximations — represents a shift in how the field approaches inference efficiency. Instead of training compression into models, TurboQuant proves you can achieve it through geometry and random projections alone.
