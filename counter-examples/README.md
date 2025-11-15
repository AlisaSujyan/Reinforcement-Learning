## 1. Baird’s Counterexample — Divergence of Semi-Gradient Bootstrapping

**Baird’s Counterexample:**
A classical reinforcement learning example demonstrating that **semi-gradient TD(0)** and **semi-gradient DP** can diverge under **off-policy learning**, even with linear function approximation and a seemingly well-conditioned feature set.

---

## Project Overview

Baird’s counterexample is a 7-state, 2-action episodic MDP specifically constructed to expose instability in off-policy TD learning.
The example shows that even when:

* rewards are zero,
* features are linearly independent,
* the value function is exactly representable, and
* the target policy is deterministic,

the combination of *off-policy bootstrapping* and *function approximation* can cause the weights to **diverge to infinity**, regardless of step-size.

The project reproduces **Figure 11.2** from Sutton & Barto by implementing:

1. **Semi-gradient Off-policy TD(0)**
2. **Semi-gradient DP (expected update)**

and plotting the evolution of the weight vector.

---

## Problem Setup

The MDP consists of **7 states**:

* **States 0–5** → upper states
* **State 6** → lower state

There are **2 actions**:

* **dashed** → transitions to one of the 6 upper states (uniformly)
* **solid** → transitions to the lower state

### Policies

* **Behavior policy**
  $$
  b(\text{solid}) = \frac{1}{7}, \quad b(\text{dashed}) = \frac{6}{7}
  $$
  Produces a *uniform next-state distribution*.

* **Target policy**
  $$
  \pi(\text{solid}) = 1
  $$
  Concentrates the on-policy distribution on the **lower** state.

### Rewards and Discount

* Reward on all transitions:
  $$
  R(s,a) = 0
  $$
* Discount rate:
  $$
  \gamma = 0.99
  $$

### Feature Representation

Each state is represented by an **8-dimensional** linear feature vector.

For upper states (i = 0, \ldots, 5):
$$
\mathbf{x}(s=i) = (0,\ldots,2,\ldots,0,\ 1)
$$

For the lower state:
$$
\mathbf{x}(s=6) = (0,\ldots,0,\ 1,\ 2)
$$

The true value function is:
$$
v_\pi(s) = 0
$$

and is exactly representable by:
$$
\mathbf{w} = \mathbf{0}
$$

Even though the features are linearly independent and exact representation is possible, semi-gradient TD diverges.

---

## Learning Algorithm

### Semi-gradient Off-policy TD(0)

Using importance sampling ratio:
$$
\rho =
\begin{cases}
0, & a=\text{dashed} \
\frac{1}{b(\text{solid})} = 7, & a=\text{solid}
\end{cases}
$$

TD error:
$$
\delta = R + \gamma \mathbf{w}^\top \mathbf{x}(S') - \mathbf{w}^\top \mathbf{x}(S)
$$

Update:
$$
\mathbf{w} \leftarrow \mathbf{w} + \alpha \rho , \delta , \mathbf{x}(S)
$$

---

### Semi-gradient DP (Expected Update)

Expected next-state value:
$$
\mathbb{E}_\pi [V(S')] = \gamma \mathbf{w}^\top \mathbf{x}(\text{lower})
$$

Bellman error:
$$
\delta(s) = \gamma \mathbf{w}^\top \mathbf{x}(\text{lower}) - \mathbf{w}^\top \mathbf{x}(s)
$$

Update over all states:
$$
\mathbf{w} \leftarrow \mathbf{w} + \frac{\alpha}{|S|} \sum_{s} \delta(s),\mathbf{x}(s)
$$

Even with **full sweeps**, no randomness, and DP-style expectation updates, the weights diverge.

---

## Experiments

Two experiments were run for **1,000 steps/sweeps**:

| Experiment                         | Description                                                      |
| ---------------------------------- | ---------------------------------------------------------------- |
| **Semi-gradient Off-policy TD(0)** | Update using behavior policy transitions and importance sampling |
| **Semi-gradient DP**               | Update using the full DP expectation sweep                       |

Initial weights are:
$$
\mathbf{w} = (1,1,1,1,1,1,10,1)
$$

The weight vector is recorded at every iteration and plotted component-wise.

---

## Results

**[Figure 11.2:]()**
Both experiments show unstable learning:

* All weight components diverge without bound.
* Divergence occurs **for any positive step size**.
* Divergence happens **even when the true value function is representable**.
* Semi-gradient DP diverges despite full-sweep updates and absence of sampling noise.

This reproduces the canonical instability result from the textbook.

---

## Key Insights

* **Off-policy bootstrapping** + **function approximation** is inherently unstable.
* Importance sampling ratios amplify TD updates when the behavior policy differs sharply from the target policy.
* Semi-gradient methods do not minimize any true objective under off-policy sampling.
* The feature vectors form a linearly independent set, yet instability still occurs — demonstrating that linear FA alone is not enough.
* Even DP with exact expectations diverges if updates are not performed using the **on-policy distribution**.

---

## Conclusion

Baird’s counterexample highlights a critical limitation of classic TD learning:

Semi-gradient TD(0) and semi-gradient DP can diverge under off-policy sampling, even in the simplest possible settings.

This example motivates the need for:

* **Gradient-TD methods** (GTD, GTD2, TDC)
* **Emphatic TD**
* **Careful handling of off-policy distributions**

The project provides a faithful reproduction of the original instability plots, offering a clear demonstration of why standard TD cannot be safely used off-policy with linear function approximation.
