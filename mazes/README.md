# 1. When the Model Is Wrong — Changing Maze with Dyna-Q and Dyna-Q+

### Project Overview

This project investigates how planning methods behave when the learned model of the environment is incorrect.

The focus is on two scenarios:

- Blocking Maze: The optimal path is removed after some time steps, forcing the agent to adapt.

- Shortcut Maze: A better path becomes available, but the agent may fail to discover it due to model bias.

We compare the baseline Dyna-Q algorithm with the enhanced Dyna-Q+ method, which encourages exploration by rewarding actions not recently tried.

Key insights:

- Planning with a wrong model can lead to suboptimal behavior.

- Optimistic or exploratory modeling can help the agent correct its model more quickly.

- Dyna-Q+ effectively balances planning, exploration, and adaptation in non-stationary environments.

### Problem Description
We simulate two maze environments:

- Blocking Maze

    - Initial optimal path runs to the right of a barrier.

    - After 1000 steps, this path is blocked, and a longer path around the left becomes optimal.

    - Both Dyna-Q and Dyna-Q+ agents must adapt after the environment changes.

- Shortcut Maze

    - Initial optimal path goes around the left of a barrier.

    - After 3000 steps, a shorter path opens to the right without removing the original path.

    - Standard Dyna-Q typically fails to discover the shortcut due to reliance on its outdated model.

    - Dyna-Q+ uses exploration bonuses to test old actions and eventually discovers the shortcut.

Rewards are accumulated over time, and agents are compared in terms of their ability to recover optimal behavior after changes.

### Learning Algorithms

#### Dyna-Q

- Combines real experience with simulated updates from a learned model.

- Works well in stationary environments but can fail when the model becomes outdated.

#### Dyna-Q+

- Extends Dyna-Q by encouraging exploration of long-unvisited actions.

- Tracks the elapsed time since each state–action pair was last executed.

- Adds a bonus reward to encourage testing of potentially changed transitions:

- This allows the agent to revisit neglected parts of the environment and adapt when changes occur.

### Experiments

1. Blocking Maze

    - Measure recovery time after the optimal path is blocked.

    - Compare how quickly agents rediscover the new path.

2. Shortcut Maze

    - Measure whether agents discover the new shorter path.

    - Analyze the impact of planning bias vs exploration bonuses.

3. Metrics

    - Average cumulative reward over time.

   - Adaptation delay after environment changes.

### Results

- [Blocking Maze:](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/mazes/generated_images/figure_8_4.png)

    Both Dyna-Q and Dyna-Q+ eventually adapt, but Dyna-Q+ recovers faster due to its exploratory planning updates.

- [Shortcut Maze:](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/mazes/generated_images/figure_8_5.png)

   Dyna-Q often fails to detect the shortcut because its model discourages exploration of that direction. 

  Dyna-Q+, by contrast, systematically revisits neglected actions and successfully finds the new optimal path.

- Conclusion:

    Planning in dynamic environments requires not only accurate modeling but also proactive exploration. Dyna-Q+ provides a simple yet effective mechanism to achieve this balance.



# 2. Dyna Maze — Planning with Dyna-Q

### Project Overview
This project implements the Dyna-Q algorithm in the deterministic maze task.

The goal is to compare planning-based agents with a non-planning Q-learning agent in terms of learning speed and policy development.

Key ideas:

- Direct RL alone (tabular Q-learning) learns slowly, improving one step per episode.

- Planning accelerates learning by reusing past experience through a model of the environment.

- The number of planning updates per real step (𝑛) strongly affects convergence speed.

### Problem Description
- The environment is a 47-state maze with walls and obstacles.

- From each state, the agent can take one of 4 actions:

        𝑢𝑝, 𝑑𝑜𝑤𝑛, 𝑙𝑒𝑓𝑡, 𝑟𝑖𝑔ℎ𝑡


Moves are deterministic unless blocked by an obstacle or maze boundary.

- Rewards:

  - +1 when entering the goal state (G).

  - 0 on all other transitions.

- Episodes:

    - After reaching the goal, the agent is returned to the start state (S).

    - Discount factor: 𝛾 = 0.95.

### Learning Algorithm: Dyna-Q
The Dyna-Q agent combines Q-learning updates with planning from a learned model.
#### Q-Learning Update
At each real step:

`Q(S_t, A_t) = Q(S_t, A_t) + α * [ R_{t+1} + γ * max_a Q(S_{t+1}, a) - Q(S_t, A_t) ]`


Planning Updates
For 𝑛 planning steps:

1. Sample a past transition (𝑆, 𝐴, 𝑆′, 𝑅)from the model.

2. Update the action-value estimate using the same Q-learning rule.

    `Q(S, A) = Q(S, A) + α * [ R + γ * max_a Q(S', a) - Q(S, A) ]`

#### Algorithm Parameters

- Step size: 𝛼 = 0.1

- Exploration: 𝜀 = 0.1 (ε-greedy action selection with random tie-breaking)

- Planning steps: 𝑛 ∈ {0, 5, 50}

  - 𝑛 = 0 → pure Q-learning (no planning).

  - Larger 𝑛 → faster learning.


### Experiments

1. **Learning curves:**

- Average steps to reach the goal per episode.

- Compare across different numbers of planning steps 𝑛.

- Results averaged over 30 runs with fixed random seeds.

2. **Policy snapshots:**

- Visualize the policy after the 2nd episode for 𝑛 = 0 vs 𝑛 = 50.

- Show how planning accelerates policy expansion.


### [Results](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/mazes/generated_images/figure_8_2.png)

- n = 0 (Q-learning only):

    - Learns one new step per episode.

    - Requires ~25 episodes to reach near-optimal performance.

- n = 5 (moderate planning):

    - Learns a complete policy in ~5 episodes.

- n = 50 (extensive planning):

    - Achieves optimal performance in just 3 episodes.

    - By the end of the 2nd episode, the policy already extends nearly to the start state.

**Conclusion:** 
Planning through Dyna-Q provides dramatic improvements in learning speed compared to direct RL. Larger planning step counts accelerate convergence, though at the cost of increased computation per step.



# 3. Prioritized Sweeping on Mazes

### Project Overview
This project implements the Prioritized Sweeping algorithm for model-based reinforcement learning in maze tasks.

Prioritized sweeping improves the efficiency of planning by focusing updates on the most critical state–action pairs, rather than sampling uniformly as in Dyna-Q.
This dramatically accelerates convergence to optimal policies — often by a factor of 5–10× compared to standard Dyna-Q with the same number of planning updates.

### Problem Description
We extend the classic Dyna Maze environment:

- **States:** A grid-based maze (structure same as in Figure 8.2), but experiments vary the resolution of the grid.

- **Actions:** {𝑢𝑝, 𝑑𝑜𝑤𝑛, 𝑙𝑒𝑓𝑡, 𝑟𝑖𝑔ℎ𝑡}, deterministic unless blocked by walls or maze boundaries.

- **Rewards:**
  - +1 on entering the goal state (G)

  - 0 otherwise

- Episodes:

    - After reaching the goal, the agent is reset to the start state (S)

    - Discount factor: 𝛾 = 0.95

The task is episodic, with the goal of minimizing the number of steps to reach 𝐺.

### Learning Algorithm: Prioritized Sweeping
#### Key Idea
Unlike unprioritized Dyna-Q, which selects planning updates at random, Prioritized Sweeping maintains a priority queue of transitions.

- Transitions with larger expected updates (higher TD error) are given higher priority.

- Updates propagate quickly backward along chains of relevant states, speeding up policy improvement.

#### Update Rule
For a sampled transition (𝑆, 𝐴, 𝑆′, 𝑅):

`δ = R + γ * max_a Q(S', a) - Q(S, A)`

`Q(S, A) = Q(S, A) + α * δ`

If ∣𝛿∣ exceeds a threshold, the transition (and its predecessors) are added to the priority queue for future updates.

**Algorithm Parameters** 

- Step size: 𝛼 = 0.1
- Discount: 𝛾 = 0.95
- Exploration: 𝜀 = 0.1 (ε-greedy policy)
- Planning steps per real step: 𝑛 = 5 (same as baseline Dyna-Q)
- Priority threshold: user-defined (𝜃)


### Experiments

1. **Baseline Comparison**

- Compare Prioritized Sweeping with standard Dyna-Q, both using at most 𝑛 = 5 planning steps per real step.

- Environments: multiple maze resolutions (grid sizes).

2. **Metrics**

- Average number of steps to goal per episode

- Learning speed (episodes to optimal performance)

- Number of backups as a measure of computation

### [Results](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/mazes/generated_images/example_8_4.png)

- **Efficiency:**
Prioritized Sweeping converges to optimal policies 5–10× faster than standard Dyna-Q.

- **Planning Quality:**
By focusing updates on states with the largest errors, learning propagates much more effectively through the maze.

- **Scalability:**
The performance advantage persists across different maze sizes, making Prioritized Sweeping more practical for large state spaces.

**Conclusion:**
Prioritized Sweeping significantly outperforms unprioritized planning in maze tasks by directing computation where it is most valuable. This shows the importance of prioritization in model-based reinforcement learning.
