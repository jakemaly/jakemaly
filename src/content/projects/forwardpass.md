---
title: "FORWARDPASS"
description: "Local AI inference server running in my bedroom — an RTX 3090 serving Qwen3.6 27B and Gemma 4 31B via llama.cpp, accessible over Tailscale and harnessed through a custom Pi setup for agentic coding."
tech:
  - llama.cpp
  - CUDA
  - Qwen3.6 27B
  - Gemma 4 31B QAT
  - Speculative Decoding
  - KV Cache Quantization
  - Linux
  - Tailscale
  - Pi
githubUrl: "https://github.com/jakemaly/forwardpass"
date: "2025-05-21"
featured: true
---

ForwardPass is an ongoing project documenting the learning, design, and evolution of a local inference machine hosted in my bedroom. It started as an experiment in running large language models on consumer hardware and has grown into a full local AI server serving models over the network.

The core hardware is an NVIDIA RTX 3090 (24GB VRAM) paired with an Intel i7-9700k. The 3090 was specifically chosen for its 24GB of VRAM — a massive leap from the previous RTX 2060's 8GB — enabling models that would otherwise require datacenter-grade GPUs. The machine dual-boots Linux for optimal CUDA performance and is served via Tailscale for SSH access from anywhere.

### Quantization & KV Cache

The project explores practical quantization strategies to fit large models on consumer hardware. By switching from FP32 to Q4 (INT4) quantization, a 27B-parameter model shrinks from ~108GB to ~16GB — fitting comfortably on the 3090's 24GB. The same approach extends to KV cache quantization (`-ctk q4_0`), enabling effective context windows up to 262k tokens without overflowing VRAM.

### Speculative Decoding & MTP

To address slow autoregressive token generation, the setup leverages Multi-Token Prediction (MTP) — a form of speculative decoding where the model itself contains a built-in draft head that proposes multiple tokens in parallel. The base model then verifies them in a single forward pass, yielding significantly faster inference without sacrificing quality. Both Qwen3.6 27B MTP and Gemma 4 31B QAT MTP have been benchmarked extensively.

### Benchmarks

Native throughput was measured with `llama-bench` across varying context depths (pp512/tg128 at d2000, d4000, d8000). Agentic capability was evaluated via a 5-instance batch from SWE-bench Verified using the Pi coding harness. Key findings: Gemma 4 31B QAT MTP showed superior context management stability with zero OOMs across all instances, while Qwen3.6 27B MTP delivered slightly higher token/sec throughput but hit VRAM limits on larger contexts.

### What It's Used For

The server powers a custom Pi coding agent setup for local-first AI-assisted development, eliminating cloud API dependencies and keeping all inference on-premise. The repository includes write-ups on the design process, configurations, sample code, and benchmark results — serving as both a functional server and a learning journal.
