---
title: Understanding Transformers from first principles
description: A clear-eyed look at the mechanics, the trade-offs, and the parts most write-ups quietly skip over.
date: 2026-06-18
author: Dr. Lena Park
category: Large Models
tags:
  - llm
  - transformers
  - deep-dive
hue: 220
featured: true
quickChecks:
  - q: "In scaled dot-product attention, the query–key dot product is divided by what before the softmax?"
    options:
      - "The number of attention heads"
      - "The square root of the key dimension"
      - "The sequence length"
      - "The batch size"
    answer: 1
    why: "Dividing by √dₖ keeps the dot products from growing large in magnitude, which would otherwise push softmax into a near one-hot regime with vanishing gradients."
  - q: "Offline eval accuracy sits far above live production performance. The most likely culprit is…"
    options:
      - "The model is too small"
      - "Train/eval contamination or distribution shift"
      - "The learning rate is too low"
      - "Too few attention heads"
    answer: 1
    why: "A gap that large almost always means the eval set leaked into training, or it no longer matches the live distribution — rarely a raw capacity problem."
quiz:
  - q: "What does retrieval-augmented generation primarily reduce?"
    options: ["Training cost", "Hallucination on knowledge-heavy queries", "GPU memory", "Tokenizer size"]
    answer: 1
  - q: "Quantizing weights to int8 mainly trades…"
    options: ["Accuracy for capacity", "Memory and latency for a small accuracy hit", "Data for compute", "Depth for width"]
    answer: 1
  - q: "A mixture-of-experts layer grows total parameters while keeping what roughly constant?"
    options: ["Training data", "Per-token compute", "Context length", "Vocabulary size"]
    answer: 1
  - q: "The √dₖ scaling inside attention exists to…"
    options: ["Speed up matrix multiplies", "Stabilize the softmax gradients", "Reduce parameter count", "Add a nonlinearity"]
    answer: 1
---

The transformer is, at heart, a remarkably simple idea that scaled further than almost anyone expected. Strip away the engineering and you are left with a single repeated motif: every token gets to look at every other token, decide what is relevant, and update its own representation accordingly. Everything else is bookkeeping.

This piece walks the architecture from the ground up — not as a list of layers to memorize, but as a sequence of decisions, each one solving a concrete problem left over from the last. If you have read the paper and still feel like the intuition slipped through your fingers, start here.

## The setup

Before attention, sequence models processed tokens one at a time, carrying state forward through a recurrence. That serial dependency was the bottleneck: it made training slow and long-range relationships fragile. The transformer's wager was that you could drop recurrence entirely and let attention do the work in parallel.

> **Self-attention** — each position attends to all positions in the same sequence, including itself, to build a context-aware representation.
>
> **Cross-attention** — queries come from one sequence and keys/values from another, the mechanism that lets a decoder consult an encoder.

Much of the modern literature traces back to one short, dense paper. It is worth keeping its claims in view as a reference point.

## How attention works

Every token is projected into three vectors: a query, a key, and a value. The query asks a question, keys advertise what each token offers, and values carry the content to be mixed. Relevance is just the dot product of a query against every key.

### Scaled dot-product

Raw dot products grow with dimension, and large values push softmax toward a one-hot spike with vanishing gradients. The fix is a single scalar: divide by the square root of the key dimension before the softmax. Small, almost trivial — and load-bearing.

### Multi-head attention

One attention pattern can only express one notion of relevance. Running several in parallel — each with its own projections — lets the model track syntax in one head, coreference in another, and position in a third, then concatenate the results. Capacity for almost no extra serial cost.

## Scaling in practice

The architecture's real gift is that it scales cleanly. Add layers, widen the model, feed more data, and quality improves along a predictable curve. The headaches that remain are practical: memory that grows with the square of sequence length, and the engineering to keep thousands of accelerators busy.

Most production work, then, is not about reinventing attention. It is about quantization, caching, batching, and retrieval — the unglamorous layer that turns a research result into something that answers a request in under a second.

## Common pitfalls

The most expensive mistakes are rarely in the model. They are in the evaluation. A number that looks too good usually is — and the gap between an offline metric and live behavior is where most teams quietly lose weeks.

## Closing thoughts

If there is one thing to carry away, it is that the transformer rewards understanding over memorization. The pieces are few and the logic between them is tight. Once the motif clicks — attend, weight, mix — the rest of the field reads like variations on a theme.
