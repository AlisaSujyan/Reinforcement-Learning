# Ten-Armed Bandits

## Overview
This repository contains an implementation and exploration of the Multi-Armed Bandit problem, as described in Chapter 2 of Reinforcement Learning: An Introduction by Sutton and Barto. The Multi-Armed Bandit is a fundamental problem in reinforcement learning where an agent must balance exploration and exploitation to maximize cumulative rewards.

## Multi-Armed Bandit Problem
The Multi-Armed Bandit problem is a simplified reinforcement learning scenario in which an agent selects from a set of actions (or arms) to maximize its expected reward. Each arm provides a stochastic reward based on an unknown probability distribution. The challenge is to develop strategies that optimize action selection over time.

### Key Concepts:
* #### k-Armed Bandits
The problem consists of k different actions, each with an unknown reward distribution.
* #### Exploration vs. Exploitation
The trade-off between selecting the best-known action (exploitation) versus trying new actions to discover better rewards (exploration).
* #### Action-Value Methods
Techniques for estimating the expected reward of each action.

## Project Breakdown
This project consists of five main parts, each exploring a different aspect of the Multi-Armed Bandit problem.
### 1. Reward Distribution
* Simulates and visualizes the reward distribution of a 10-armed bandit.

* Generates a violin plot (Figure 2.1) to show the variation in rewards per action.
### 2. Greedy vs. ε-Greedy Action Selection
* Compares pure greedy action selection with ε-greedy methods for ε = {0, 0.1, 0.01}.

* Evaluates average reward and optimal action selection percentage.

* Generates plots (Figure 2.2) showing how exploration affects learning.
### 3. Optimistic Initial Values vs. Realistic Initial Values
* Compares two bandits: one with optimistic initial values (Q1 = 5), the other with realistic initial values (Q1 = 0).

* Examines how optimism influences early exploration.

* Generates plots (Figure 2.3) showing the impact of initialization strategies.
### 4. Upper-Confidence-Bound (UCB) Action Selection
* Compares UCB action selection (c = 2) against ε-greedy (ε = 0.1).

* Evaluates how UCB balances exploration and exploitation.

* Generates plots (Figure 2.4) showing average reward over time.
### 5. Gradient Bandit Algorithms (GBA)
* Implements GBA with and without a baseline for different step sizes (α = 0.1, 0.4).

* Evaluates how the gradient approach dynamically adjusts action preferences.

* Generates plots (Figure 2.5) showing the effect of different step sizes on optimal action selection.

## Results and Analysis
The experiments compare different strategies based on:

* Cumulative reward over time

* Optimal action selection percentage
