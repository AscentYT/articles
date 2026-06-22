---
title: Inside Inference Optimization
description: A field guide for engineers who would rather understand why than memorize how.
date: 2026-05-25
author: Yuki Tanaka
category: Serving
tags:
  - serving
  - infra
hue: 235
featured: false
---

Training gets the headlines; inference pays the bills. Every request a model serves is a recurring cost, and the difference between a naive and a tuned serving stack is often an order of magnitude in dollars. Knowing where the time goes is most of the battle.

## Decoding is memory-bound

Autoregressive generation produces one token at a time, and each step reads the entire model from memory. That makes decoding bandwidth-limited, not compute-limited, which flips your intuition: bigger batches and smaller weights help far more than faster math.

## The KV cache is your budget

Every token you generate adds to the key-value cache, and that cache competes with everything else for memory. Managing it well — paged attention, eviction, smart batching — is what lets you serve long contexts and many users on the same hardware.

## Batch like you mean it

A single request leaves the accelerator mostly idle. Continuous batching, which slots new requests into the gaps left by finished ones, keeps utilization high without making any one user wait for a fixed window. It is the least glamorous and most impactful lever in the stack.
