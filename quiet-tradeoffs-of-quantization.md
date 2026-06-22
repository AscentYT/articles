---
title: The quiet trade-offs of Quantization
description: A clear-eyed look at the mechanics, the trade-offs, and the parts most write-ups quietly skip over.
date: 2026-06-03
author: Priya Nair
category: Infrastructure
tags:
  - serving
  - infra
  - eval
hue: 200
featured: false
---

Quantization is the cheapest large win in inference: store and compute with fewer bits, and the model gets smaller and faster almost for free. Almost. The trade-offs are real, just quiet enough that teams discover them in production instead of in the design doc.

## Where the bits go

Most of the memory in a served model is weights, so moving from 16-bit to 8-bit roughly halves the footprint and speeds up memory-bound decoding. Push to 4-bit and the wins continue, but so does the sensitivity. The question is never "how few bits" in the abstract — it is "how few bits where."

## Outliers ruin everything

A handful of large-magnitude activations dominate the error budget. Naive quantization spends its precision on a range it rarely uses, and quality falls off a cliff. The modern techniques — per-channel scales, keeping a few dimensions in higher precision — are all variations on protecting the outliers.

## Measure on your distribution

A model that loses half a point on a public benchmark can lose far more on your traffic, or far less. Quantization error is distribution-dependent, so the only number that matters is the one measured on inputs that look like production. Trust your eval, not the marketing table.
