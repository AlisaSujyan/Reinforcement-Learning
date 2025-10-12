# Maximization Bias Example

## Project Overview

This project illustrates **maximization bias** in **Temporal-Difference (TD) control algorithms**, specifically in **Q-learning** and **Double Q-learning**, using a simple two-state Markov Decision Process (MDP).
The goal is to show how the **use of a single estimator for both action selection and evaluation** can lead to **systematic overestimation of action values**, resulting in suboptimal decision-making.

---

## Problem Description

The MDP used in this example consists of **two non-terminal states**, ( A ) and ( B ), and a terminal state.
The structure is as follows:

* **Starting State:** All episodes begin in state ( A ).
* **Available Actions from A:**

  * **Right:** Transitions directly to the terminal state with reward ( 0 ).
  * **Left:** Transitions to state ( B ), also with reward ( 0 ).
* **Available Actions from B:**

  * Each action leads **immediately to termination**, with a reward drawn from a **normal distribution**:
    $$
    R \sim \mathcal{N}(-0.1, 1.0^2)
    $$
* The **expected return** for any trajectory starting with the **Left** action is:
  $$
  \mathbb{E}[G_t | A, \text{left}] = -0.1
  $$
  Therefore, **taking Left from state A is suboptimal**.

However, **maximization bias** can cause Q-learning to **overestimate** the value of ( B ), making the **Left** action appear more favorable than it truly is.

---

## Learning Algorithms

### Q-Learning

The standard **Q-learning** algorithm (Equation 6.8 in Sutton & Barto) updates its action-value estimates as:
$$
Q(S_t, A_t) \leftarrow Q(S_t, A_t) + \alpha \left[R_{t+1} + \gamma \max_{a} Q(S_{t+1}, a) - Q(S_t, A_t)\right]
$$
Because both the **maximization** and **evaluation** are based on the same Q-values, this introduces **positive bias** in the estimates.

### Double Q-Learning

**Double Q-learning** mitigates this bias by maintaining **two independent estimators**, ( Q_1 ) and ( Q_2 ), and alternating updates:
$$
Q_1(S_t, A_t) \leftarrow Q_1(S_t, A_t) + \alpha \left[R_{t+1} + \gamma Q_2(S_{t+1}, \arg\max_a Q_1(S_{t+1}, a)) - Q_1(S_t, A_t)\right]
$$
and vice versa for ( Q_2 ).
This decouples **action selection** (argmax) from **evaluation** (expected value), significantly reducing overestimation.

---

## Experiment Configuration

| Parameter               |       Symbol        | Value | Description                                     |
| ----------------------- |:-------------------:| :---: | ----------------------------------------------- |
| Exploration probability | $$( \varepsilon )$$ |  0.1  | Chance of random action selection               |
| Step-size               |   $$( \alpha )$$    |  0.1  | Learning rate                                   |
| Discount factor         |   $$( \gamma )$$    |  1.0  | No discounting (episodic task)                  |
| Actions in B            |          —          |   10  | Determines number of reward samples per episode |

---

## Results

* **Q-learning** initially **overestimates** the value of the **Left** action due to **maximization bias**.

  * This causes the agent to **favor Left**, despite its true expected return being -0.1.
  * Even after convergence, **Q-learning** selects Left ≈ **5% more often** than optimal under the given parameters.
* **Double Q-learning** eliminates this bias, producing **unbiased estimates** and correct action selection frequencies.

### Qualitative Comparison

| Algorithm         | Bias     | Left-Action Frequency | Convergence Behavior    |
| ----------------- | -------- | --------------------- | ----------------------- |
| Q-learning        | Positive | ~55%                  | Overestimates B’s value |
| Double Q-learning | Neutral  | ~50%                  | Stable, unbiased        |

---

## Key Insights

* **Maximization bias** arises when the same samples are used for both **action selection** and **evaluation** in stochastic environments.
* **Double Q-learning** effectively removes this bias through **decoupled updates**.
* This experiment demonstrates the **importance of unbiased estimators** in reinforcement learning, especially for **value-based methods**.

---