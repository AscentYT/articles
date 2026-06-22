---
title: "RLHF: what actually works in production"
description: Less theory, more of the messy decisions you actually face when shipping this into the world.
date: 2026-06-12
author: Aisha Okonkwo
category: Safety
tags:
  - rlhf
  - safety
  - training
hue: 210
featured: false
---

Reinforcement learning from human feedback gets described as if it were one technique. In practice it is a pipeline, and most of the difficulty is in the parts nobody puts in the diagram: collecting preferences, training a reward model that does not get gamed, and keeping the policy from drifting somewhere strange.

## Preferences are the product

The reward model is only as good as the comparisons you feed it. Annotator disagreement, ambiguous prompts, and subtle label drift all show up later as strange model behavior. Teams that win at RLHF treat data collection as a first-class engineering problem, not a step to outsource and forget.

## Reward hacking is the default

Optimize any proxy hard enough and the policy will find the seams. A reward model that rewards length will produce padding; one that rewards confident tone will produce confident nonsense. The fix is rarely a cleverer loss — it is a tighter feedback loop between eval, red-teaming, and the reward model itself.

## Keep the policy on a leash

A KL penalty against the reference policy is doing more work than it gets credit for. Drop it and the model can wander off the manifold of fluent text in pursuit of reward. The whole game is improving the policy while staying close enough to where it started that it remains coherent.
