# 1. State Aggregation on the 1000-State Random Walk

### Project Overview
This project implements state aggregation as a simple form of function approximation, applied to the 1000-state random walk problem.

The objective is to investigate how grouping states into aggregated representations affects the learned value function and the resulting approximation bias.

### Problem Description
We consider a random walk environment with 1000 non-terminal states, numbered from 1 to 1000, arranged linearly:

- **Initial state:** 500 (center of the chain).

- **Transitions:** From each state, the agent moves to one of 100 neighboring states to the left or right, chosen uniformly at random.

- **Termination:**

    - At the left edge (near state 1): the probability of terminating on the left increases as fewer left neighbors exist.

    - At the right edge (near state 1000): the probability of terminating on the right increases similarly.

- **Rewards:**

    - Termination on the left → −1

    - Termination on the right → +1

    - All intermediate transitions → 0

This defines an episodic, undiscounted prediction task where the agent estimates the expected return from each state under the given dynamics.

The true value function 𝑣_𝜋(𝑠) for this environment is nearly linear, slightly curving near the boundaries.

### Learning Algorithm: Gradient Monte Carlo with State Aggregation
State aggregation groups multiple states together, maintaining a single value estimate per group.
In this experiment, the 1000 states are partitioned into 10 groups of 100 states each:

    Group 1: 1–100,  Group 2: 101–200,…, Group 10: 901–1000

**Value Function Representation**
Each group 𝑖 is associated with one weight 𝑤_𝑖 and the approximate value function is:

$$
v^{(s, w)} = w_i \text{ if } s \text{ belongs to group } i
$$


**Update Rule**

Using the Gradient Monte Carlo algorithm (a special case of SGD), the update for each visited state 𝑆_𝑡 is:

$$
w_i \leftarrow w_i + \alpha (G_t - v^{(S_t, w)})
$$


where:
- 𝐺_𝑡 is the Monte Carlo return,

- 𝛼 is the step-size parameter.

The gradient 
∇𝑣^(𝑆_𝑡, 𝑤) is 1 for the active group and 0 otherwise, making this a clean case of tabular SGD with shared parameters.

### Experiments
- **Episodes:** 100,000

- **Step size:** 𝛼 = 2 × 10^−5

- **Discount factor:** 𝛾 = 1.0 (undiscounted task)

- **Number of groups:** 10

- **States per group:** 100

The true value function 𝑣_𝜋(𝑠) was compared to the approximate value function learned via Gradient Monte Carlo with state aggregation.

Additionally, the state visitation distribution 𝜇(𝑠) was estimated empirically during learning.

### [Results](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk-fa/generated_images/figure_9_1.png)
- The approximate value function shows a staircase pattern:

    - Within each group, all states share the same predicted value.

    - Between groups, abrupt changes occur, reflecting the coarseness of aggregation.

- The learned function closely matches the global minimum of the mean squared value error.

- Center states (near 500) dominate the visitation distribution 𝜇, while extreme states (1 and 1000) are visited rarely.

- The asymmetric weighting by 𝜇 biases the learned values:

    - Leftmost groups (e.g., states 1–100) have higher estimates than the unweighted average of their true values.

    - Rightmost groups (e.g., states 901–1000) have lower estimates.

- These shifts arise because frequently visited interior states exert stronger influence on the aggregated estimates than rarely visited boundary states.

#### Key Insights

- State aggregation introduces a bias–variance trade-off:

    - Fewer groups → smoother, biased approximation.

    - More groups → more accurate but less generalizable.

- The distribution of state visits 𝜇(𝑠) significantly affects approximation bias.

- Even this simple linear random walk demonstrates the limitations of coarse generalization when true value gradients are smooth.



# 2. Bootstrapping on the 1000-State Random Walk

### Project Overview

This project demonstrates the application of bootstrapping methods—specifically the semi-gradient TD(0) and n-step semi-gradient TD algorithms—on the 1000-state random walk environment.
By using state aggregation as a simple form of function approximation, this experiment compares the behavior and accuracy of bootstrapped (TD) learning methods with Monte Carlo (MC) methods in approximating value functions for large, continuous environments.

### Problem Description

The environment is a 1000-state random walk where:

- States are numbered 1 to 1000, left to right.

- Each episode starts at state 500 (the center).

- From a given state, the agent transitions:

    - To one of 100 neighboring states on the left or right, chosen uniformly at random.

    - If transitions exceed the boundary, the episode terminates:

       - Left termination: reward = -1

       - Right termination: reward = +1

    - All other transitions yield a reward of 0.

- The true value function 𝑣_𝜋 for this task is nearly linear, slightly curving near both ends.

This setup extends the classical random walk task (19 states) to a large-scale continuous version, introducing function approximation challenges and highlighting the role of bootstrapping.

### Learning Algorithm
The semi-gradient n-step Temporal Difference (TD) algorithm estimates the value function using state aggregation.
Each group of states shares one estimated value, enabling generalization across similar states.

**Algorithm Outline**
At each time step:
1. The agent selects an action and transitions to a new state with a received reward.

2. For each non-terminal state:

- Compute the n-step return:

$$
G_t^{(n)} = R_{t+1} + R_{t+2} + \cdots + R_{t+n} + v^{(S_{t+n}, w)}
$$



- Update the value function parameters:

   
$$
G_t^{(n)} = R_{t+1} + R_{t+2} + \cdots + R_{t+n} + v^{(S_{t+n}, w)}
$$


   - In the case of state aggregation, the gradient is 1 for the corresponding group and 0 for all others.

    
### Experiments

Two configurations were explored:

1. TD(0) with 10 groups of 100 states

    - Matches the configuration from the state aggregation experiment (Figure 9.1).

    - Reveals how TD’s bootstrapping bias impacts value approximation.

2. n-step TD with 20 groups of 50 states

    - Chosen to make results quantitatively comparable to the 19-state tabular random walk task (Figure 7.2).

    - Typical transitions (~50 states left/right) mimic the tabular single-step dynamics.

Performance was measured as the unweighted average RMS error across all states and over the first 10 episodes, following Sutton & Barto’s methodology.


### [Results](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk-fa/generated_images/figure_9_2.png)

- TD(0) produced an approximate value function farther from the true values than gradient MC (due to bootstrapping bias).

- Despite this, TD methods exhibited faster convergence and lower variance, confirming their advantage in learning rate.

- n-step TD achieved similar qualitative results to the 19-state random walk, validating the consistency of temporal difference learning across both tabular and function approximation settings.

- Increasing the number of steps 𝑛 improved accuracy up to a point, after which variance began to dominate.

#### Key Insights

- Bootstrapping trades accuracy for speed, enabling efficient learning from incomplete episodes.

- State aggregation provides a simple, interpretable way to scale value estimation to large state spaces.

- n-step TD bridges the gap between Monte Carlo and TD(0), offering a continuum between full returns and bootstrapped updates.

# 3. Polynomials vs Fourier Basis — 1000-State Random Walk

###  Project Overview

This project compares **polynomial** and **Fourier** basis functions for **linear value function approximation** on the **1000-state random walk** problem. 

The main goal is to evaluate how the choice of basis function family affects the **learning speed**, **stability**, and **asymptotic accuracy** of the **Gradient Monte Carlo** algorithm when applied to a continuous approximation problem.

While both bases can represent smooth functions, they differ significantly in their **numerical conditioning** and **generalization behavior**, particularly in online learning scenarios.


### Problem Description

The environment is a **1000-state random walk**, a generalization of the classic random walk problem:

* **States:** 1–1000 (non-terminal), with terminal states at 0 and 1001.
* **Start state:** 500 (center).
* **Transitions:** From the current state, the agent moves randomly left or right up to 100 states.
* **Rewards:**

  * Left termination → −1
  * Right termination → +1
  * All other transitions → 0

The goal is to approximate the **true state-value function** $$ ( v_\pi(s) ), $$ which is nearly linear across the state space, using **different function bases** and measure their root-mean-squared error (RMSE) during learning.


### Learning Algorithms

### Gradient Monte Carlo with Function Approximation

The **Gradient Monte Carlo (MC)** algorithm is used to update value estimates based on complete-episode returns.

Update rule:

$$
\mathbf{w}_{t+1} = \mathbf{w}_t + \alpha \left(G_t - \hat{v}(S_t, \mathbf{w}*t)\right) \nabla*{\mathbf{w}} \hat{v}(S_t, \mathbf{w}_t)
$$

where $$ ( \hat{v}(S_t, \mathbf{w}) )$$ is a **linear approximation** of the value function:

$$
\hat{v}(S_t, \mathbf{w}) = \mathbf{w}^\top \mathbf{x}(S_t)
$$

and $$ ( \mathbf{x}(S_t) ) $$ is a **feature vector** derived from either **polynomial** or **Fourier** bases.



### Polynomial Basis

For a given order ( n ), the polynomial basis defines feature components as powers of the normalized state:

$$
x_i(s) = s^i, \quad i = 0, 1, \dots, n
$$

This basis often struggles with **numerical instability** and **overfitting**, especially for high-order terms or online updates.



### Fourier Basis

The Fourier basis uses **cosine terms** to represent periodic functions:

$$
x_i(s) = \cos(i \pi s), \quad i = 0, 1, \dots, n
$$

This basis tends to be **better conditioned** and provides smoother approximations with **less bias** across the state space, especially for continuous inputs.



### Experiments

#### Setup

* **Number of runs:** 1
* **Episodes per run:** 5000
* **Orders:** 5, 10, 20
* **Step-sizes:** $$ ( \alpha = 1\times10^{-4} ) and ( 5\times10^{-5} ) $$
* **Algorithms:** Gradient Monte Carlo with each basis type.
* **Performance metric:** RMSE between predicted values and analytically derived true values.

The true values were computed using **Dynamic Programming** (iterative policy evaluation) until convergence.



### Evaluation

For each configuration:

1. Initialize value function with chosen basis type and order.
2. Run Gradient MC for 5000 episodes.
3. Compute RMSE at every episode.
4. Average results over runs.
5. Plot **learning curves** comparing convergence speed and asymptotic error.


### [Results]()

* **Fourier basis** consistently outperformed the **polynomial basis** in both convergence speed and stability.
* As the order increased:

  * The **Fourier** features captured more fine-grained structure without overfitting.
  * The **polynomial** features became numerically unstable and prone to oscillation.
* **Low-order** Fourier bases achieved near-optimal accuracy faster than any polynomial configuration.

Empirically, these results reinforce the conclusion that **polynomial bases are not well-suited for online reinforcement learning**, while **Fourier bases** offer a more robust, smooth generalization behavior.


### Key Insights

* Fourier bases generalize better in continuous domains due to their smooth orthogonal representation.
* Polynomial approximations suffer from poor conditioning as order increases.
* Even in simple value prediction tasks, **basis selection critically affects learning dynamics**.
* The Fourier basis has become a preferred choice for function approximation in modern RL settings.
