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
