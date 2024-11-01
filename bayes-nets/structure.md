---
title: '6.4 Structure of Bayes Nets'
parent: 6. Bayes Nets
nav_order: 4
layout: page
header-includes:
    \pagenumbering{gobble}
---

# 6.4 Structure of Bayes Nets

In this class, we will refer to two rules for Bayes Net independences that can be inferred by looking at the graphical structure of the Bayes Net:

- **Each node is conditionally independent of all its ancestor nodes (non-descendants) in the graph, given all of its parents.**

  ![Parents](../assets/images/parents.png)

- **Each node is conditionally independent of all other variables given its Markov blanket.** A variable’s Markov blanket consists of parents, children, and children’s other parents.

  ![Markov Blanket](../assets/images/blanket.png)

Using these tools, we can return to the assertion in the previous section: that we can get the joint distribution of all variables by joining the CPTs of the Bayes Net.

$$P(X_1, X_2, \dots, X_n) = \prod_{i=1}^n P(X_i | \text{parents}(X_i))$$

This relation between the joint distribution and the CPTs of the Bayes net works because of the conditional independence relationships given by the graph. We will prove this using an example.

<p></p>
Let's revisit the previous example. We have the CPTs $$P(B)$$ , $$P(E)$$ , $$P(A |B,E)$$ , $$P(J | A)$$ and $$P(M | A)$$ , and the following graph:

![Basic Bayes Net Examples](../assets/images/basic_bayes_nets.png)

For this Bayes net, we are trying to prove the following relation:

$$P(B, E, A, J, M) = P(B)P(E)P(A | B, E)P(J | A)P(M | A)$$

We can expand the joint distribution another way: using the chain rule. If we expand the joint distribution with topological ordering (parents before children), we get the following equation:

$$P(B, E, A, J, M) = P(B)P(E | B)P(A | B, E)P(J | B, E, A)P(M | B, E, A, J)$$

<p></p>
Notice that in the first equation every variable is represented in a CPT $$P(var | Parents(var))$$ , while in the second equation, every variable is represented in a CPT $$P(var | Parents(var), Ancestors(var))$$ .

We rely on the first conditional independence relation above, that **each node is conditionally independent of all its ancestor nodes in the graph, given all of its parents**[^1].

<p></p>
Therefore, in a Bayes net, $$P(var | Parents(var), Ancestors(var)) = P(var | Parents(var))$$ , so the two equations are equal. The conditional independences in a Bayes Net allow for multiple smaller conditional probability tables to represent the entire joint probability distribution.

[^1]: Elsewhere, the assumption may be defined as "a node is conditionally independent of its _non-descendants_ given its parents." We always want to make the minimum assumption possible and prove what we need, so we will use the ancestors assumption.
