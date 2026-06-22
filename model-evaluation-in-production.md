---
title: "Model Evaluation: what actually works in production"
description: Less theory, more of the messy decisions you actually face when shipping this into the world.
date: 2026-05-28
author: Marcus Reed
category: Evaluation
tags:
  - eval
  - serving
hue: 215
featured: false
---

Evaluation is where good intentions go to die. Everyone agrees it matters; almost everyone underinvests until a regression ships. The teams that move fast are not the ones that skip evals — they are the ones whose evals are fast enough to trust on every change.

## Offline and online disagree, on purpose

An offline benchmark measures a frozen slice of the world; online metrics measure live behavior under shifting traffic. When they disagree, that is signal, not noise. The gap usually points at distribution shift or a metric that does not capture what users actually care about.

## Build the eval before the feature

A capability you cannot measure is a capability you cannot improve. Writing the eval first forces you to define success concretely, and it gives you a baseline the moment the feature exists. Retrofitting an eval after launch almost always means grading to the answer you already shipped.

## Humans in the loop, sparingly

Human judgment is the gold standard and the bottleneck. The trick is to spend it where automated metrics are weakest — nuance, safety, tone — and to automate the rest ruthlessly. A small, well-curated human eval beats a large, noisy one every time.
