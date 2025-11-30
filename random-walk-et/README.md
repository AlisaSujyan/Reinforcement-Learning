## 19-State Random Walk — λ-Return Algorithms

**19-State Random Walk:**
Implementation of **off-line λ-return**, **TD(λ)**, and **online λ-return (True online TD(λ))** algorithms to evaluate temporal-difference learning methods with eligibility traces on a **19-state random walk problem**.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Project Overview

This project explores **λ-return methods** for temporal-difference learning, which interpolate between Monte Carlo and one-step TD methods.
The 19-state random walk task (Example 7.1, Sutton & Barto) is used to illustrate:

* Off-line λ-return updates at the end of each episode,
* TD(λ) updates using eligibility traces at each time step,
* Online λ-return, updating weights fully online during the episode.

Performance is measured using the **root-mean-square error (RMSE)** between estimated and true state values over the first 10 episodes.

The project reproduces Figures **12.3, 12.6, and 12.8** from the textbook, evaluating:

* Effect of varying the **trace-decay parameter λ**,
* Effect of step-size α on learning stability and speed,
* Comparison between off-line, TD(λ), and online λ-return algorithms.

---

## Problem Setup

The environment is a **19-state random walk** with two terminal states:

* Left terminal state (0) with reward −1,
* Right terminal state (20) with reward +1.

The agent starts at the middle state (10) and moves left or right with equal probability.
State values are approximated using **exact linear representation**, where each state has its own weight:

$$
\hat{v}(s, \mathbf{w}) = w_s
$$

Episodes terminate upon reaching a terminal state.
The performance metric is **RMSE** across non-terminal states:

$$
\text{RMSE} = \sqrt{\frac{1}{19} \sum_{s=1}^{19} \left( \hat{v}(s) - v_\pi(s) \right)^2 }
$$

---

## Learning Algorithms

### Off-line λ-Return

Updates weights **after each episode** using the λ-return as target:

$$
G_t^\lambda = (1-\lambda) \sum_{n=1}^{T-t-1} \lambda^{n-1} G_{t:t+n} + \lambda^{T-t-1} G_{t:T}
$$

Truncated λ-return with cutoff $\epsilon$:

$$
\lambda^{n} < \epsilon \quad \Rightarrow \text{discard contributions from future steps}
$$

Weight update rule:

$$
\mathbf{w} \gets \mathbf{w} + \alpha \sum_{t=0}^{T-1} \left( G_t^\lambda - \hat{v}(S_t, \mathbf{w}) \right) \nabla \hat{v}(S_t, \mathbf{w})
$$

* Computes all updates after episode completion.
* Smoothly interpolates between **Monte Carlo (λ=1)** and **1-step TD (λ=0)**.

### TD(λ)

Updates weights **at every time step** using eligibility traces:

$$
\mathbf{z}*t = \lambda \mathbf{z}*{t-1} + \nabla \hat{v}(S_t, \mathbf{w})
$$

$$
\delta_t = R_{t+1} + \hat{v}(S_{t+1}, \mathbf{w}) - \hat{v}(S_t, \mathbf{w})
$$

$$
\mathbf{w} \gets \mathbf{w} + \alpha \delta_t \mathbf{z}_t
$$

* Backward view of λ-return; weights updated online.
* Suitable for **continuing and episodic tasks**.
* Sensitive to **step-size α** at higher λ.

### Online λ-Return (True Online TD(λ))

Fully **online and incremental** version of λ-return.

* Uses **Dutch traces** to stabilize updates:

$$
\mathbf{z}*t = \lambda \mathbf{z}*{t-1} + (1 - \alpha \lambda z_{t-1}[S_t]) \nabla \hat{v}(S_t, \mathbf{w})
$$

* Weight update:

$$
\mathbf{w} \gets \mathbf{w} + \alpha \left( \delta_t + \hat{v}(S_t, \mathbf{w}) - \hat{v}(S_t, \mathbf{w}*{\text{old}}) \right) \mathbf{z}*t - \alpha (\hat{v}(S_t, \mathbf{w}) - \hat{v}(S_t, \mathbf{w}*{\text{old}})) e*{S_t}
$$

* Performs subtly better than off-line λ-return (Figure 12.8), especially during episodes.
* Computationally more complex but more accurate for ongoing updates.

---

## Experiments

|  Figure  | Algorithm / Experiment Description                                                                                        |
| :------: | :------------------------------------------------------------------------------------------------------------------------ |
| **12.3** | Off-line λ-return performance for various λ values. RMSE averaged over first 10 episodes; compares λ-return to n-step TD. |
| **12.6** | TD(λ) performance versus off-line λ-return for different λ and α. Demonstrates potential instability at large α.          |
| **12.8** | Online λ-return versus off-line λ-return. Shows subtle improvements from online incremental updates.                      |

**Experiment settings:**

* **50 independent runs**,
* **Step-size α** sweep tailored for each λ,
* RMSE averaged across runs and episodes.

**Example RMSE Table for Off-line λ-Return (First 10 Episodes):**

|  λ  | α=0.05 | α=0.1 | α=0.2 | α=0.3 |
| :-: | :----: | :---: | :---: | :---: |
| 0.0 |  0.102 | 0.110 | 0.125 | 0.140 |
| 0.4 |  0.098 | 0.105 | 0.115 | 0.130 |
| 0.8 |  0.085 | 0.092 | 0.108 | 0.125 |
| 0.9 |  0.082 | 0.090 | 0.105 | 0.123 |
| 1.0 |  0.080 | 0.088 | 0.102 | 0.120 |

---

## Results

**[Figure 12.3:](../generated_images/figure_12_3.png)**
Off-line λ-return performance shows **best results at intermediate λ**, similar to n-step TD methods. Small λ behaves like 1-step TD, λ=1 approximates Monte Carlo.

**[Figure 12.6:](../generated_images/figure_12_6.png)**
TD(λ) closely matches off-line λ-return when α is optimal or smaller. Large α values cause instability, highlighting the need for careful step-size selection.

**[Figure 12.8:](../generated_images/figure_12_8.png)**
Online λ-return slightly outperforms off-line λ-return due to **incremental updates within episodes**, confirming benefits of fully online learning.

---

## Key Insights

* **Intermediate λ values** optimize performance, balancing bias and variance.
* **TD(λ) approximates off-line λ-return** at optimal α, but is sensitive to step size.
* **Online λ-return** offers incremental learning benefits with slightly better performance.
* Eligibility traces provide **temporal credit assignment**, distributing updates backward in time.
* The 19-state random walk demonstrates how different λ and α choices influence stability and convergence.

---

## Conclusion

This project demonstrates the application of **λ-return methods** to a simple episodic control task:

* Off-line λ-return effectively interpolates between TD and Monte Carlo methods,
* TD(λ) implements online backward updates using eligibility traces,
* Online λ-return further enhances learning during episodes with Dutch traces.

Proper choice of **λ and α** is critical for stable and accurate value estimation.

---
