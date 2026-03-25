---
title: "Google Gemini Can Now Natively Embed Video — Developers Are Building Sub-Second Video Search"
description: "Google's Gemini Embedding 2 is the first foundation model to natively embed video, audio, images, and text into a unified vector space. Early adopters are already building real-time video search applications that return results in under a second, opening up new possibilities for media-heavy applications."
pubDate: 2026-03-24T15:00:00Z
tags: ["ai", "gemini", "google", "video", "embeddings", "search", "machine-learning"]
author: "AI Editor"
category: "AI"
---

## A New Modality for Embeddings

Google released Gemini Embedding 2 on March 10, and it represents a genuine first: a production embedding model that natively processes video, audio, images, text, and PDFs into a single vector space. Previous approaches to video search required extracting individual frames, running them through an image model, and stitching the results together. Gemini Embedding 2 skips all of that — you send it a video clip, and it returns a vector that captures visual content, on-screen text, and audio simultaneously.

The model is available now in public preview through the Gemini API and Vertex AI under the ID `gemini-embedding-2-preview`. It's already integrated with LangChain, LlamaIndex, Haystack, Weaviate, Qdrant, ChromaDB, and Google's own Vector Search.

## How It Works

Gemini Embedding 2 accepts video input up to 120 seconds in MP4 or MOV format, supporting H.264, H.265, AV1, and VP9 codecs. The model samples up to 32 frames from the input and processes them alongside any audio track to produce a single embedding vector. Output dimensionality is flexible — developers can choose between 128 and 3,072 dimensions, with 768, 1,536, and 3,072 as the recommended configurations.

The API follows the same pattern as text embeddings:

```python
from google import genai
from google.genai import types

client = genai.Client()

result = client.models.embed_content(
    model="gemini-embedding-2-preview",
    contents=[
        types.Content(
            parts=[
                types.Part.from_uri(
                    file_uri="gs://bucket/clip.mp4",
                    mime_type="video/mp4"
                )
            ]
        )
    ]
)
```

The key architectural innovation is **Matryoshka Representation Learning (MRL)**. This technique lets a single model produce embeddings at multiple granularities. A retrieval system can perform a fast, coarse search across millions of items using 768-dimensional sub-vectors, then re-rank the top results using the full 3,072-dimensional embeddings. This two-stage approach makes large-scale video search practical without sacrificing precision.

## Sub-Second Video Search in Practice

The capability caught developer attention immediately. A project shared on Hacker News — pulling 226 points and 65 comments — demonstrated a CLI tool that indexes hours of security camera footage into ChromaDB, then searches it with natural language queries and automatically trims the matching clip.

The system works by downsampling video to 5 frames per second and creating overlapping 5-second chunks to avoid missing events at segment boundaries. Each chunk gets embedded natively through Gemini — no frame captioning, no transcription, no intermediate text conversion. The resulting vectors go into ChromaDB for semantic search.

The performance numbers are compelling:

- **Indexing cost** — Approximately $2.50 per hour of footage
- **Search latency** — Sub-second results across indexed video libraries
- **Optimization** — Still-frame detection reduces costs significantly for static footage like security cameras

One nuance the developer highlighted: query specificity matters. Searching for "car cuts me off" returned generic results, but "car with bike rack cuts me off at night" found the exact clip. The embeddings capture rich semantic detail, but users need to leverage that depth for best results.

## What Makes This Different

Previous multimodal embedding approaches — CLIP, ImageBind, and others — either didn't support video natively or required complex preprocessing pipelines. Gemini Embedding 2 is the first model from a major provider that accepts raw video as a first-class input modality alongside text, images, and audio.

This matters for several reasons:

- **Unified vector space** — A text query, an image, and a video clip all live in the same embedding space. You can search video with text, find similar videos to an image, or match audio to visual content using the same index.
- **No pipeline complexity** — Frame extraction, OCR, speech-to-text, and caption generation pipelines are no longer prerequisites for video search. The model handles all of these signals internally.
- **Cross-modal retrieval** — Because all modalities share a vector space, you can build applications that naturally bridge media types without separate indexes or fusion layers.

The model also supports over 100 languages for text input, and handles audio natively without requiring intermediate transcription — a significant advantage for multilingual video content.

## Privacy and the Surveillance Question

The Hacker News discussion surfaced an important tension. The same technology that enables powerful video search also lowers the barrier for mass surveillance. Commenters raised concerns about combining cheap video embedding with existing camera networks and platforms like Fusus, which aggregate feeds from private security cameras for law enforcement.

The project's creator acknowledged this and advocated for open-weight local models that could process footage entirely on-device, eliminating both API costs and data exfiltration risks. As of now, Gemini Embedding 2 requires sending video to Google's API — a non-starter for many security-sensitive use cases.

## What Comes Next

Gemini Embedding 2 is still in preview, and the 120-second video limit constrains certain use cases. But the direction is clear: embeddings are going multimodal by default, and video is no longer a second-class citizen in the retrieval stack. For developers building media search, content moderation, or video analytics applications, native video embeddings eliminate an entire layer of infrastructure complexity. The question now is how quickly the rest of the ecosystem — vector databases, retrieval frameworks, and application layers — adapts to treat video as just another searchable modality.
