---
title: Why Embeddings keep surprising us
description: We trace the idea from a napkin sketch to a system that holds up under real production traffic.
date: 2026-05-31
author: Dr. Lena Park
category: NLP
tags:
  - embeddings
  - nlp
  - retrieval
hue: 245
featured: false
---

Embeddings are the most useful idea in modern machine learning that almost nobody fully understands. Map things — words, images, users — into a vector space where distance means similarity, and a startling range of problems become nearest-neighbor lookups.

## Geometry is the feature

The magic is that structure in the data becomes structure in the space. Related concepts cluster; analogies become directions. None of this is engineered explicitly; it falls out of training the model to predict context. When embeddings surprise us, it is usually because the geometry encoded something we did not think to ask for.

## Similarity is a choice

Cosine, dot product, Euclidean — these are not interchangeable, and the right one depends on how the embeddings were trained. Mismatched similarity metrics are a silent source of bad retrieval. Know what objective produced your vectors before you decide how to compare them.

## Drift is the long-term enemy

Embeddings encode a snapshot of the world the model was trained on. As language, products, and users move, the space slowly stops matching reality. The systems that stay good are the ones that treat re-embedding and index refreshes as routine maintenance, not emergencies.
