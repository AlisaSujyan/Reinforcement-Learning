# Gridworld MDP
## Overview
This repository contains an implementation of the Gridworld Markov Decision Process (MDP) as described in Chapter 3 of Reinforcement Learning: An Introduction by Sutton and Barto. The Gridworld example illustrates key reinforcement learning concepts such as state-value functions, reward dynamics, and policy evaluation.

## Gridworld Problem
The Gridworld environment consists of a rectangular grid where an agent can take actions to move north, south, east, or west. The agent's objective is to navigate the grid while optimizing cumulative rewards.
### Key Features:
* States: Each cell in the grid represents a unique state.
* Actions: The agent can move in four directions: north, south, east, or west.
* Reward Structure:
  * Moving into a wall incurs a reward of -1 (negative penalty).
  * Moving out of special states A and B gives rewards of +10 and +5, respectively, while teleporting the agent to A' or B'.
  * All other moves yield a reward of 0.
* Value Function: The state-value function is computed for the equiprobable random policy (each action is equally likely).

## Project Breakdown
### 1. Gridworld Environment
* Implements the transition dynamics and reward structure.

* Handles special states (A and B) and boundary conditions.
### 2. State-Value Function Computation
* Uses the Bellman equation to compute the value function for a given policy.

* Implements policy evaluation and value iteration to determine optimal state values.

* Generates plots showing state values under different policies.
### 3. Policy Effects on State Values
* Equiprobable Random Policy: The agent selects each action with equal probability.

* Value Function Visualization: Negative values appear near edges due to frequent penalties for hitting walls.

* Analysis of Special States:
  * State A: Expected return is less than immediate reward (+10) since teleportation to A' leads to possible penalties.
  * State B: Expected return is greater than immediate reward (+5) due to potential gains from reaching A or B.

## Results and Analysis
The computed state-value function provides insights into:
* Expected long-term rewards from each state

* How state values change based on policy

* Effects of reward structure and transition dynamics
## References
 Sutton R.S., Barto A.G. - Reinforcement Learning: An Introduction (2nd edition) https://archive.org/details/rlbook2018/mode/2up