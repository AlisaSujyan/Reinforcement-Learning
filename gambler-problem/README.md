# Gambler's Problem – Value Iteration Solution

This project implements a solution to the Gambler's Problem as presented in Reinforcement Learning: An Introduction by Sutton and Barto, using value iteration. It includes visualization of value function updates over time and the final optimal policy.

## Problem Description

A gambler has the opportunity to bet on a sequence of coin flips:

* If the coin lands heads, they win the amount they staked.

* If it lands tails, they lose their stake.

* The goal is to reach $100, starting with any amount between 1 and 99.

* The game ends either in winning ($100) or losing (0).

### Formal Setup:
* States: Integer capital values from 0 to 100.

* Actions: At state s, the stake can be any integer in [0,min(s,100−s)].

* Reward: 0 for all transitions except reaching $100, which gives a reward of 1.

* Objective: Maximize the probability of reaching the goal.

## Algorithm: Value Iteration
The solution uses value iteration, a dynamic programming method that updates value estimates until convergence. The final policy is derived from the optimal value function.

## Output
1. Value Function Estimates over successive sweeps of value iteration.

2. Final Policy showing the stake chosen at each capital level.
