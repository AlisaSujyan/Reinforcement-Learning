# Trajectory Sampling — Effects of Update Distribution in Planning

### Project Overview
This project investigates the effect of update distribution on planning efficiency in large, random episodic tasks, using one-step expected tabular updates.

Key goals:

- Compare uniform sampling (cycling through all state–action pairs) with on-policy sampling (updating state–action pairs encountered under the current policy).

- Understand how the distribution of updates influences planning speed and policy quality.

- Explore the effect of branching factor and state space size on planning dynamics.

### Problem Description

- The environment is a randomly generated MDP:

    - Each of the ∣𝑆∣ states has 2 possible actions.

    - Each action transitions to 𝑏 next states (branching factor), chosen randomly, with equal probability.

    - Transitions terminate with a probability of 0.1, ending the episode.

     - Rewards are drawn from a Gaussian distribution (𝜇 = 0 , 𝜎 = 1).

- Tasks are undiscounted and episodic.

- Planning can be evaluated at any point by computing $$\tilde{v}_\pi(s_0)$$ the expected return from the start state under the current greedy policy.

This setup allows controlled experiments isolating the effect of update distribution on planning efficiency.

### Learning Algorithm: One-Step Expected Updates
#### Uniform Sampling
- Cycles deterministically through all state–action pairs.

- Updates each pair in-place according to:

$$
Q(s, a) \leftarrow \mathbb{E}\Big[ R + \gamma \max_{a'} Q(s', a') \Big]
$$
- Ensures all state–action pairs are treated equally.

#### On-Policy Sampling
- Simulates episodes starting from the same initial state.

- Updates only the state–action pairs encountered under an ε-greedy policy (𝜀 = 0.1).

- Initially focuses updates on frequently visited states, speeding early planning, but may slow long-term convergence due to neglected pairs.

### Experiments

1. **Varying Branching Factor**

    - Tasks with 1, 3, and 10 next states per action.

    - Compare uniform vs on-policy sampling in terms of expected return as a function of updates.

2. **Scaling State Space**

    - Experiments with 1,000 vs 10,000 states.

    - Examine how the advantage of on-policy focusing changes with increasing state space.

3. Metrics

    - Expected return from the start state under the current greedy policy.

    - Performance plotted against the number of expected updates.


### [Results]([fogure8_8](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/trajectory-sampling/generated_images/figure_8_8.png))

- Small branching factor (b = 1):

    - On-policy updates provide faster initial planning.

    - Long-term, uniform updates catch up and may surpass due to more comprehensive coverage.

- Larger branching factor (b = 3 or 10):

    - Initial advantage of on-policy focusing is shorter and less pronounced.

- Large state spaces (10,000 states):

    - On-policy focusing offers a significant early advantage.

    - Initial faster planning lasts longer as the number of states increases.

**Conclusion:**

The update distribution significantly influences planning efficiency.
- On-policy focusing accelerates early learning, particularly in large or sparse MDPs.

- Uniform sampling ensures long-term comprehensive updates, avoiding neglect of rarely visited states.
