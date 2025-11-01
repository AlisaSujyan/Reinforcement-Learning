## Mountain Car Task — Semi-Gradient n-Step SARSA


**Mountain Car Task:**
Implementation of the episodic semi-gradient *n-step SARSA* algorithm with **tile-coding function approximation** for solving the continuous **Mountain Car control problem**.
---

## Project Overview

This project demonstrates how a reinforcement learning agent can solve the Mountain Car task — a continuous control problem in which an underpowered car must learn to reach the top of a hill.
The problem requires *temporarily moving away from the goal* to build momentum, illustrating challenges in control tasks with **delayed reward structures**.

The project reproduces Figures **10.1–10.4** from the Sutton & Barto textbook, evaluating:

* The cost-to-go function learned over episodes,
* Learning speed with different step sizes (α), and
* Performance comparison between 1-step and multi-step SARSA methods.

---

## Problem Setup

The environment consists of a car moving along a 1D track between two hills.
At each time step, the agent selects one of three actions:

1. Full throttle reverse (−1)
2. Zero throttle (0)
3. Full throttle forward (+1)

The dynamics are given by:

$$
\dot{x}*{t+1} = \text{bound}[\dot{x}*t + 0.001 A_t - 0.0025 \cos(3x_t)]
$$

$$
x*{t+1} = \text{bound}[x_t + \dot{x}*{t+1}]
$$

with the bounds:

$$
-1.2 \le x_t \le 0.5, \quad -0.07 \le \dot{x}_t \le 0.07
$$

Episodes terminate when the car reaches the goal ($x = 0.5$).
The reward is constant:

$$
R_t = -1 \quad \text{for all time steps until termination.}
$$

Each episode begins from a random position:

$$
x_0 \in [-0.6, -0.4), \quad \dot{x}_0 = 0
$$

The continuous state space $(x_t, \dot{x}_t)$ is encoded via **tile coding** with:

* 8 overlapping tilings,
* Asymmetric offsets,
* Binary feature vector $\mathbf{x}(s, a)$ used for linear function approximation:
  
$$
\hat{q}(s, a, \mathbf{w}) = \mathbf{w}^\top \mathbf{x}(s, a)
$$

---

## Learning Algorithm

The **semi-gradient n-step SARSA** algorithm is used to estimate $q_\pi(s,a)$ under function approximation.

The n-step return:

$$
G_{t:t+n} = R_{t+1} + R_{t+2} + \dots + R_{t+n} + \hat{q}(S_{t+n}, A_{t+n}, \mathbf{w})
$$

The weight update rule:

$$
\mathbf{w} \leftarrow \mathbf{w} + \alpha , [G_{t:t+n} - \hat{q}(S_t, A_t, \mathbf{w})] , \nabla \hat{q}(S_t, A_t, \mathbf{w})
$$

**Exploration policy:**
An epsilon-greedy strategy with $\varepsilon = 0$, relying on **optimistic initialization** to drive exploration.

**Value Function Representation:**
Linear combination over active tiles, each with its associated weight.
Weights are updated proportionally to the TD error for all active tiles.

---

## Experiments

Several experiments reproduce the textbook’s illustrative figures:

|  Figure  | Experiment Description                                                                                   |
| :------: | :------------------------------------------------------------------------------------------------------- |
| **10.1** | Cost-to-go function visualization during early, mid, and late learning (episodes 1, 100, 9000).          |
| **10.2** | Learning curves comparing step sizes $\alpha \in {0.1, 0.2, 0.5}$ (averaged over 10 runs, 500 episodes). |
| **10.3** | Comparison of 1-step and 8-step SARSA (n = 1 vs. n = 8) under best-performing step sizes.                |
| **10.4** | Study of the combined effect of $\alpha$ and $n$ on early learning performance.                          |

All results are averaged over multiple runs for statistical stability.
Time steps per episode are plotted on a log scale.

---

## 6. Results

**[Figure 10.1:]()**
Shows the evolution of the learned cost-to-go function across episodes.
Early in learning, the agent oscillates in the valley due to overly optimistic initial values; later, it converges toward a policy that successfully reaches the goal.

**[Figure 10.2:]()**
Learning curves for different step sizes show that moderate $\alpha$ values balance stability and learning speed. Too large values lead to divergence.

**[Figure 10.3:]()**
Multi-step SARSA ($n=8$) learns faster and achieves better asymptotic performance than the 1-step variant, confirming the advantage of intermediate bootstrapping levels.

**[Figure 10.4:]()**
A systematic evaluation reveals that performance peaks at moderate $n$ (around 4–8) and moderate $\alpha$, consistent with the theoretical trade-off between bias and variance.

---

## Key Insights

* Optimistic initialization alone can induce sufficient exploration, even with $\varepsilon = 0$.
* Tile-coding enables effective approximation of the action-value function in continuous spaces.
* Intermediate bootstrapping levels ($n>1$ but small) significantly improve sample efficiency.
* Excessively large $\alpha$ or $n$ destabilize learning, confirming sensitivity to hyperparameters.
* The Mountain Car task vividly illustrates *the need for moving away from immediate goals* to achieve long-term success.

---

## Conclusion

This project demonstrates the successful application of **semi-gradient n-step SARSA** with **tile-coding function approximation** on a challenging continuous control task.

* Learning improves with moderate bootstrapping,
* Properly tuned step size and feature representation are essential for convergence.

---
