# CIRVA: Hybrid Tiered AI Architecture

This document describes the high-level architecture of CIRVA, a privacy-first, context-aware AI communication system.

## Architectural Overview

To resolve the trade-off between user privacy and advanced AI capabilities, CIRVA utilizes a hybrid local-first design. The system segments operations into two latency-isolated tiers and segregates storage.

```
                    +---------------------------------------+
                    |           React Native App            |
                    +-------------------+---------------+---+
                                        |               |
                                        v               v
                         +--------------+---+   +-------+---------------+
                         |   Tier 1 (Local) |   |   Tier 2 (Cloud)      |
                         |   ONNX Runtime   |   |   Groq Serverless     |
                         |   (MiniLM SLM)   |   |   (Llama-3.1-8B)      |
                         +--------------+---+   +-------+---------------+
                                        |               |
                                        v               v
                         +--------------+---+   +-------+---------------+
                         | Local Database   |   | Telemetry Guardrail   |
                         | (SQLite Cache)   |   | (Supabase Analytics)  |
                         +------------------+   +-----------------------+
```

---

## The Two Tiers

### 1. Tier 1: Local ONNX Semantic Classifier (Edge)
* **Goal**: Real-time classification of typing inputs, notifications, and quick actions.
* **Implementation**: Run a quantized **MiniLM** transformer model locally using **ONNX Runtime (React Native)**.
* **Latency**: Sub-20ms execution on modern hardware.
* **Privacy**: 100% offline, zero network requests, zero data exposure.
* **Fallback Trigger**: If the local classifier's confidence score falls below a set threshold (e.g. `0.70`), the task is promoted to Tier 2.

### 2. Tier 2: Groq LLM Fallback (Cloud Burst)
* **Goal**: Handle complex contextual reasoning, multi-turn summaries, and ambiguous user intent.
* **Implementation**: An encrypted, low-latency burst API call to **Llama-3.1-8B-Instant** or **Llama-3.3-70B** hosted on **Groq**.
* **Latency**: ~100-150ms time-to-first-token.
* **Privacy**: Transient processing only. Raw chat messages are anonymized and stripped of personally identifiable information (PII) before transmission.

---

## Data Segmentation & Storage

To prevent central servers from reading user communications, the storage design is strictly split:

1. **Encrypted SQLite Cache (Local)**: Raw text, user profiles, chat messages, and decryption keys are kept locally on the device's hardware partition.
2. **Supabase Telemetry (Cloud)**: Central analytics only receive non-identifying telemetry events (`user_events`) to track performance, accuracy, and latency, without storing any actual conversation logs.
