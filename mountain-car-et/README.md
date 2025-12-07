# Mountain Car — SARSA$(\lambda)$ with Eligibility Traces

**Mountain Car Task:**
Implementation of the **SARSA$(\lambda)$** algorithm on the continuous-control Mountain Car environment using **tile-coding function approximation** and several types of **eligibility traces** (accumulating, replacing, replacing with clearing, Dutch).
This project replicates Figures **12.10** and **12.11** from Sutton & Barto (2nd ed.), enabling direct comparison to the earlier **n-step SARSA** results from Chapter 10.

---

## Project Overview

This project evaluates how different eligibility-trace mechanisms affect learning performance in the Mountain Car problem.

The experiments show:

* The effect of varying the **trace-decay parameter λ**,
* Comparison of **SARSA$(\lambda)$** to **n-step SARSA**,
* The impact of trace types:

  * accumulating,
  * replacing,
  * replacing-with-clearing,
  * Dutch traces (true online SARSA).

The fading-trace bootstrapping strategy inherent in SARSA$(\lambda)$ often provides **more efficient learning** compared to fixed-length bootstrapping methods.

These experiments reproduce textbook Figures **12.10** and **12.11**, comparing:

* Learning speed under multiple λ values,
* Reward-per-episode performance across different trace types.

---

## Problem Setup

The Mountain Car task requires an agent to drive an underpowered car up a steep hill.
To succeed, the agent must **move away from the goal first**, building momentum — a classic challenge involving **delayed returns**.

At each step the agent selects one of three actions:

1. Full throttle reverse (−1)
2. Zero throttle (0)
3. Full throttle forward (+1)

The system dynamics:

$$
\dot{x}_{t+1} = \text{bound}!\left[\dot{x}_t + 0.001 A_t - 0.0025\cos(3x_t)\right]
$$

$$
x_{t+1} = \text{bound}[x_t + \dot{x}_{t+1}]
$$

with position and velocity constraints:

$$
-1.2 \le x_t \le 0.5,\quad -0.07 \le \dot{x}_t \le 0.07.
$$

The reward function:

$$
R_t = -1 \quad \text{until the car reaches } x \ge 0.5.
$$

Initial conditions:

$$
x_0 \sim U[-0.6,-0.4], \qquad \dot{x}_0 = 0.
$$

**Tile coding** converts continuous state–action pairs into sparse binary features:

* 8 tilings
* Scaled inputs for position and velocity
* Value approximation:

$$
\hat{q}(s,a,\mathbf{w}) = \sum_{i \in \text{active tiles}} w_i
$$

---

## Learning Algorithm

SARSA$(\lambda)$ combines:

1. **On-policy TD control**
2. **Eligibility traces** for temporal credit assignment
3. **Function approximation** via tile coding

### TD target

$$
G_t = R_{t+1} + \gamma \hat{q}(S_{t+1},A_{t+1})
$$

### TD error

$$
\delta_t = G_t - \hat{q}(S_t,A_t)
$$

### Eligibility trace update (depends on trace type)

Examples:

**Accumulating Trace**
$$
\mathbf{z}*t = \gamma\lambda \mathbf{z}*{t-1} + \nabla \hat{q}(S_t,A_t)
$$

**Replacing Trace**
Active components set to 1; others decay by $\gamma\lambda$.

**Dutch Trace (true online SARSA)**

$$
\mathbf{z}*t = \gamma\lambda\mathbf{z}*{t-1} + (1 - \alpha\gamma\lambda,z_{t-1}[i])\nabla \hat{q}
$$

### Weight update

$$
\mathbf{w} \gets \mathbf{w} + \alpha \delta_t \mathbf{z}_t
$$

---

## Experiments

Two major experiments replicate textbook Figures 12.10 and 12.11.

### **Experiment 1 — Figure 12.10**

**SARSA$(\lambda)$ with replacing traces**

* 30 runs
* 50 episodes
* λ ∈ {0.99, 0.95, 0.5, 0}
* Step sizes:

$$
\alpha \in\left{ \frac{1}{4}, \frac{2}{4}, \ldots, \frac{7}{4}\right}
$$

Performance measured as **steps per episode**, averaged over runs and episodes.

---

### **Experiment 2 — Figure 12.11**

**Comparison of eligibility trace types**

Trace types:

1. Accumulating
2. Dutch (true online SARSA)
3. Replacing
4. Replacing-with-clearing

* 30 runs
* 20 episodes
* λ = 0.9
* α ∈ {0.2, 0.4, ..., 2.0}

To stabilize results, episodes exceeding the step limit (5000 steps) are terminated and count as large negative rewards.

Results are averaged into a reward-per-episode metric:

$$
\text{Reward} = -\text{Steps per episode}
$$

---

## Results

### **[Figure 12.10:](../generated_images/figure_12_10.png)**

SARSA$(\lambda)$ with replacing traces achieves **more efficient learning** for larger λ values.
Compared to $n$-step SARSA (Chapter 10), SARSA$(\lambda)$ typically:

* Converges faster
* Requires fewer steps per episode
* Benefits from trace-based bootstrapping rather than fixed-$n$ updates

Intermediate λ values again provide the best trade-off between bias and variance.

---

### **[Figure 12.11:](../generated_images/figure_12_11.png)**

Comparison across trace types reveals:

* **True online SARSA$(\lambda)$ (Dutch traces)** provides the best performance.
* **Replacing traces** also perform well and are more stable than accumulating traces.
* **Accumulating traces** become unstable for large α (steep divergence at α > 0.6).
* **Replacing-with-clearing** offers advantages in deterministic tile-coded domains by reducing interference from non-selected actions.

---

## Key Insights

* SARSA$(\lambda)$ performs better than $n$-step SARSA due to **smooth bootstrapping decay**.
* Eligibility traces significantly affect performance — especially with function approximation.
* Dutch traces (true online SARSA) provide:

  * better stability,
  * smoother gradient flow,
  * improved early learning performance.
* Large step sizes can destabilize accumulating traces due to unbounded growth in trace values.
* Replacing traces help mitigate trace explosion by resetting active components.

---

## Conclusion

This project demonstrates the effectiveness of **SARSA$(\lambda)$** with different eligibility trace strategies on the Mountain Car control problem.

* Larger λ values improve early learning by extending temporal credit assignment.
* True online SARSA$(\lambda)$ (Dutch traces) yields the **best overall performance**.
* Trace selection dramatically influences stability, convergence speed, and reward efficiency.

These results show that **gradient-corrected traces** (Dutch) and **tile coding** form a powerful combination for continuous control tasks under SARSA$(\lambda)$.

---

