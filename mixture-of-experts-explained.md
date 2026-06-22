---
title: Understanding Mixture of Experts from first principles
description: A clear-eyed look at the mechanics, the trade-offs, and the parts most write-ups quietly skip over.
date: 2026-05-22
author: Aisha Okonkwo
category: Large Models
tags:
  - llm
  - moe
  - training
hue: 255
featured: false
---

Mixture of experts answers a tempting question: what if a model could have far more parameters without paying for all of them on every token? The trick is to route each token to a small subset of specialized sub-networks, so capacity grows while per-token compute stays roughly flat.

## Routing is the whole game

A small gating network decides which experts each token visits. Get routing right and experts specialize in useful ways; get it wrong and you train a lot of dead weight. Most of the research effort in MoE is really effort spent making the router learn a good, balanced assignment.

## Load balancing or bust

Left alone, routers collapse — sending most tokens to a few favorite experts while the rest starve. Auxiliary load-balancing losses and capacity limits exist to fight this, trading a little routing freedom for experts that all actually get trained.

## Cheap to run, awkward to serve

MoE buys you quality at a fixed compute budget, but the parameters still have to live somewhere. Memory footprint and the communication cost of shuffling tokens to the right expert make serving its own engineering problem — one reason dense models still win when memory, not compute, is the constraint.
