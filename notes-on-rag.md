---
title: Notes on Retrieval-Augmented Generation, without the hype
description: What the benchmarks tell you, what they hide, and how to read the difference between them.
date: 2026-06-09
author: Yuki Tanaka
category: Retrieval
tags:
  - rag
  - retrieval
  - nlp
hue: 250
featured: false
---

Retrieval-augmented generation is the most over-promised and under-specified pattern in applied AI. The premise is sound: give the model the right context at inference time instead of baking everything into weights. The disappointment usually comes from treating retrieval as solved when it is the hard part.

## Retrieval is the bottleneck

If the right passage is not in the top-k, no amount of prompt engineering will save the answer. Chunking strategy, embedding quality, and reranking matter more than the generator you bolt on top. Measure recall at the retrieval stage before you blame the model for hallucinating.

## Context is not free

Stuffing more passages into the prompt has diminishing and sometimes negative returns. Models attend unevenly across long contexts, and irrelevant passages actively distract. A tight, well-ranked handful usually beats a generous dump.

## Evaluate the system, not the parts

It is tempting to evaluate retrieval and generation separately because it is easier. But users experience the composition. End-to-end evals on realistic queries catch the failures — relevant-but-misread, retrieved-but-ignored — that component metrics quietly miss.
