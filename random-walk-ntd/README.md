# n-Step Temporal Difference (TD) Learning — Random Walk Example

## Project Overview
This project implements n-step Temporal Difference (TD) learning for the random walk problem described in Sutton & Barto’s Reinforcement Learning: An Introduction (Chapter 7).

The goal is to compare how different values of n affect the learning process.

* 1-step TD (TD(0)) updates only the most recent state.\
* n-step TD generalizes this by updating multiple preceding states.
* Extreme cases:
  * n = 1 → pure TD.
  * n → ∞ → Monte Carlo (MC) method.
* Empirical studies show that intermediate values of n can often perform better than either extreme.

## Problem Description
We simulate a random walk environment with 19 states:
* Non-terminal states: 1–19
* Terminal states:
  * Left (index 0) with reward -1
  * Right (index 20) with reward +1

Each episode begins in the center state (index 10) and proceeds left or right with equal probability until reaching a terminal state. Rewards are given only at termination; all intermediate transitions have reward 0.

The goal is to estimate the value function 𝑉(𝑠), i.e., the expected return from each non-terminal state.

* The true values can be derived analytically from the Bellman equations.

### Learning Algorithm: n-step Temporal Difference

The algorithm generalizes TD(0) and Monte Carlo:
* TD(0) (n = 1): Updates only the most recent state using the next state’s value estimate.
* Monte Carlo (n → ∞): Updates states based only on full-episode returns.
* n-step TD: Updates the last n visited states toward the return over n steps (or until termination).

#### Update rule
    V(S_t) ← V(S_t) + α [ G_t^(n) - V(S_t) ]
where 𝐺_𝑡^(𝑛) is the n-step return:

    G_t^(n) = R_{t+1} + γ R_{t+2} + ... + γ^(n-1) R_{t+n} + γ^n V(S_{t+n})

### Experiments
1. Effect of Different n-step Values:

   * Run episodes with varying 𝑛 (e.g., 1, 2, 3, 5, 10).

    * Compare the learned values to the true values.

2. Learning Curve Analysis:

   * Compute Root Mean Squared Error (RMSE) between predicted and true values.

   * Average over multiple runs to reduce variance.

   * Plot RMSE vs episodes for different 𝑛 and learning rates 𝛼.

3. Empirical Findings:

   * Small 𝑛 (TD-like) → fast but less accurate.

   * Large 𝑛 (MC-like) → unbiased but slower to propagate updates.

   * Intermediate 𝑛 often achieves the best trade-off.

### [Results](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk-ntd/generated_images/figure_7_2.png)
* With n = 1 (TD(0)), updates are local and incremental, leading to stable but slower propagation of terminal rewards.
* With large n (≈ MC), updates rely heavily on full returns, leading to high variance and slower convergence.
* With intermediate n (e.g., 3–5), the algorithm achieves the best performance by balancing bias and variance.

These findings mirror the theoretical insights that n-step TD can outperform both TD(0) and MC in practice.
