---
title: "6.8 Summary"
parent: 6. Bayes Nets
nav_order: 8
layout: page
---

## 6.8 Summary

To summarize, Bayes' Nets is a powerful representation of joint probability distributions. Its topological structure encodes independence and conditional independence relationships, and we can use it to model arbitrary distributions to perform inference and sampling.

In this note, we covered two approaches to probabilistic inference: exact inference and probabilistic inference (sampling). In exact inference, we are guaranteed the exact correct probability, but the amount of computation may be prohibitive.

The exact inference algorithms covered were:
- Inference By Enumeration
- Variable Elimination

We can turn to sampling to approximate solutions while using less compute.

The sampling algorithms covered were:
- Prior Sampling
- Rejection Sampling
- Likelihood Weighting
- Gibbs Sampling