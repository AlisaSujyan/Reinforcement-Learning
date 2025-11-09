## An Access-Control Queuing Task — Differential Semi-Gradient SARSA

**Access-Control Queuing Task:**
Implementation of the differential semi-gradient SARSA algorithm using **tile-coding function approximation** to learn an optimal acceptance policy in a server access control problem with multiple customer priorities.

---

## Project Overview

This project investigates a continuing decision-making task in which an agent must manage access to a limited number of servers.
At each time step, a customer with a random priority arrives, and the agent must decide whether to **accept** or **reject** the request.
The goal is to maximize **average long-term reward** without discounting.

The task reproduces **Figure 10.5** from the Sutton & Barto textbook (*Reinforcement Learning: An Introduction, 2nd ed.*), showing:

* The learned **differential value function** for each priority and server state.
* The resulting **policy** (accept/reject) derived from learned values.

---

## Problem Setup

There are **10 servers**, and **customers of four priority levels** (`0–3`) arrive one at a time to a single queue.

### Task Dynamics

* Each customer offers a reward based on priority:
  $$
  \text{Reward(priority)} = {1, 2, 4, 8}
  $$
* On each step, the agent must **accept** or **reject** the customer.
* If all servers are busy, the customer **must** be rejected.
* Each busy server becomes free with probability:
  $$
  p = 0.06
  $$
* The queue never empties, and customer priorities are uniformly random.
* The process continues indefinitely, forming a **continuing task**.

The agent’s state is represented by:
$$
S_t = (\text{number of free servers}, \text{customer priority})
$$
and the action set is:
$$
A_t \in {\text{reject}, \text{accept}}
$$

The task objective is to **maximize the differential return**:
$$
\text{maximize } \mathbb{E}[R_t - \bar{R}]
$$
where $\bar{R}$ is the learned estimate of the **average reward rate**.

---

## Learning Algorithm

The **differential semi-gradient SARSA** algorithm is applied with **tile-coding** to approximate the action-value function:
$$
\hat{q}(s, a, \mathbf{w}) = \mathbf{w}^\top \mathbf{x}(s, a)
$$

For each transition $(S_t, A_t, R_{t+1}, S_{t+1}, A_{t+1})$, the update is:
$$
\begin{aligned}
\delta_t &= R_{t+1} - \bar{R} + \hat{q}(S_{t+1}, A_{t+1}, \mathbf{w}) - \hat{q}(S_t, A_t, \mathbf{w}) \
\bar{R} &\leftarrow \bar{R} + \beta , \delta_t \
\mathbf{w} &\leftarrow \mathbf{w} + \alpha , \delta_t , \nabla \hat{q}(S_t, A_t, \mathbf{w})
\end{aligned}
$$

### Parameters

|        Symbol        | Description                                | Value |
| :------------------: | :----------------------------------------- | :---: |
|       $\alpha$       | Step size for state-action value           |  0.01 |
|        $\beta$       | Step size for average reward               |  0.01 |
|     $\varepsilon$    | Exploration probability                    |  0.1  |
|          $p$         | Probability of a busy server becoming free |  0.06 |
| $n_{\text{servers}}$ | Total number of servers                    |   10  |
| $n_{\text{tilings}}$ | Number of overlapping tilings              |   8   |

The agent follows an **$\varepsilon$-greedy** policy based on $\hat{q}(s, a, \mathbf{w})$, with mandatory rejection when no servers are available.

---

## Experiments

The implementation runs the continuing task for **1 million time steps**, training the agent to balance acceptance and rejection decisions.
After learning, the estimated **state values** and **policy** are visualized.

|  Figure  | Experiment Description                                                                                                                                                        |
| :------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **10.5** | Differential semi-gradient SARSA solution showing: <br>• Estimated differential value of the best action per state <br>• Learned policy (0 = Reject, 1 = Accept) as a heatmap |

The experiments compute and plot:

1. **Value Function** — differential value of the best action for each combination of free servers and priority.
2. **Policy Map** — binary grid showing when to accept or reject requests.

---

## Results

**[Figure 10.5:](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/access-control/generated_images/figure_10_5.png)**
The top plot shows the **differential value function** for each customer priority across the range of free servers.
Higher priorities yield larger value differences, as expected from their reward structure.

The bottom heatmap visualizes the **policy**:

* Light (0) → Reject
* Dark (1) → Accept

The policy shows a clear boundary:
low-priority customers are accepted only when several servers are free, while high-priority customers are accepted more aggressively.
The learned average reward stabilizes around:
$$
\bar{R} \approx 2.31
$$

---

## Key Insights

* The agent learns a **priority-based acceptance threshold**: accept high-priority customers even when resources are scarce.
* **Differential learning** (without discounting) effectively handles continuing tasks with steady-state reward.
* **Tile coding** provides a smooth approximation over discrete state-action pairs.
* Lower data density near extreme states (few free servers) can cause irregularities in estimated values.
* The approach generalizes the tabular case while remaining stable and interpretable.

---

## Conclusion

This project implements and analyzes **differential semi-gradient SARSA** for a resource-allocation problem under uncertainty.

* Stable average-reward learning in continuing environments.
* Clear, interpretable policies based on resource availability and request priority.
* The practicality of tile coding even in tabular-like settings.

