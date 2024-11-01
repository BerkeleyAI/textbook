---
title: "6.1 Probability Rundown"
parent: 6. Bayes Nets
nav_order: 1
layout: page
header-includes:
    \pagenumbering{gobble}
---

# 6.1 Probability Rundown

We're assuming that you've learned the foundations of probability in CS70, so these notes will assume a basic understanding of standard concepts in probability like PDFs, conditional probabilities, independence, and conditional independence. Here we provide a brief summary of probability rules we will be using.

A **random variable** represents an event whose outcome is unknown. A **probability distribution** is an assignment of weights to outcomes. Probability distributions must satisfy the following conditions:

$$0 \leq P(\omega) \leq 1$$

$$\sum_{\omega}P(\omega) = 1$$

For instance, if $$A$$ is a binary variable (can only take on two values), then $$P(A = 0) = p$$ and $$P(A = 1) = 1 - p$$ for some $$p \in [0,1]$$.

We will use the convention that capital letters refer to random variables and lowercase letters refer to some specific outcome of that random variable.

We use the notation $$P(A, B, C)$$ to denote the **joint distribution** of the variables $$A, B, C$$. In joint distributions, ordering does not matter, i.e., $$P(A, B, C) = P(C, B, A)$$.

We can expand a joint distribution using the **chain rule**, also sometimes referred to as the product rule. 

$$P(A, B) = P(A | B) P(B) = P(B | A) P(A)$$

$$P(A_1, A_2, \dots, A_k) = P(A_1) P(A_2 | A_1) \dots P(A_k | A_1, \dots, A_{k-1})$$

The **marginal distribution** of $$A, B$$ can be obtained by summing out all possible values that variable $$C$$ can take as $$P(A, B) = \sum_{c}P(A, B, C = c)$$. The marginal distribution of $$A$$ can also be obtained as $$P(A) = \sum_{b} \sum_{c}P(A, B = b, C = c)$$. We will also sometimes refer to the process of marginalization as "summing out."

When we do operations on probability distributions, sometimes we get distributions that do not necessarily sum to 1. To fix this, we **normalize**: take the sum of all entries in the distribution and divide each entry by that sum.

<p>
</p>
**Conditional probabilities** assign probabilities to events conditioned on some known facts. For instance, $$P(A|B = b)$$ gives the probability distribution of $$A$$ given that we know the value of $$B$$ equals $$b$$. Conditional probabilities are defined as:

$$P(A|B) = \frac{P(A, B)}{P(B)}.$$

Combining the above definition of conditional probability and the chain rule, we get **Bayes' Rule**:

$$P(A | B) = \frac{P(B | A) P(A)}{P(B)}$$

To write that random variables $$A$$ and $$B$$ are **mutually independent**, we write $$A \perp\!\!\!\perp B$$. This is equivalent to $$B \perp\!\!\!\perp A$$.

<p>
</p>
When $$A$$ and $$B$$ are mutually independent, $$P(A, B) = P(A) P(B)$$. An example you can think of is two independent coin flips. You may be familiar with mutual independence as just "independence" in other courses. We can derive from the above equation and the chain rule that $$P(A | B) = P(A)$$ and $$P(B | A) = P(B)$$.

<p>
</p>
To write that random variables $$A$$ and $$B$$ are **conditionally independent** given another random variable $$C$$, we write $$A \perp\!\!\!\perp B | C$$. This is also equivalent to $$B \perp\!\!\!\perp A | C$$.

<p>
</p>
If $$A$$ and $$B$$ are conditionally independent given $$C$$, then $$P(A, B | C) = P(A | C) P(B | C)$$. This means that if we have knowledge about the value of $$C$$, then $$B$$ and $$A$$ do not affect each other. Equivalent to the above definition of conditional independence are the relations $$P(A | B, C) = P(A | C)$$ and $$P(B | A, C) = P(B | C)$$. Notice how these three equations are equivalent to the three equations for mutual independence, just with an added conditional on $$C$$!

