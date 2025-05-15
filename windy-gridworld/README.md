# Windy Gridworld

This project implements the Windy Gridworld problem using SARSA (State-Action-Reward-State-Action), an on-policy temporal-difference reinforcement learning method. It is inspired by the environment described in "Reinforcement Learning: An Introduction" by Sutton and Barto.

## Problem Description

The [Windy Gridworld](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/windy-gridworld/book_images/Example_6_5_inset.PNG) is a 7×10 grid environment with a start and goal state. The agent can move in four directions: up, down, left, and right. However, a key twist in this environment is the crosswind blowing upward across the grid, which shifts the agent vertically depending on the column it is in.

| Column | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| ------ | - | - | - | - | - | - | - | - | - | - |
| Wind   | 0 | 0 | 0 | 1 | 1 | 1 | 2 | 2 | 1 | 0 |

* For example, if you're one cell to the right of the goal and move left, the wind pushes you upward, placing you above the goal.

## Learning Approach

* Algorithm: SARSA (on-policy TD learning)

* Exploration Strategy: ε-greedy

* Episodic Task: Starts at the beginning and ends when the goal is reached

* Reward Structure: Constant reward of -1 per step to encourage faster goal-reaching

###  Observations
* Over time, SARSA learns a greedy policy that optimally reaches the goal in 15 steps(the minimum possible).

* Continued ε-greedy exploration (~ε=0.1) leads to a slight average episode length of ~17 steps.

* Monte Carlo methods are unsuitable for this problem due to the possibility of infinite episodes.

* SARSA avoids this issue by learning during the episode and avoiding self-trapping policies.

## Results
An [example learning curve](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/windy-gridworld/generated_images/example_6_5.png) from SARSA shows:

* Decreasing episode lengths over time

* Goal reached in fewer steps

* Stable learning performance by ~8000 time steps
