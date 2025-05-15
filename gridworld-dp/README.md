# Gridworld via Dynamic Programming
This project implements Dynamic Programming methods for solving the Gridworld problem, a foundational environment in Reinforcement Learning (RL), as introduced in Sutton & Barto's "Reinforcement Learning: An Introduction".

#### It demonstrates:
* Iterative Policy Evaluation

* Value Function Convergence (in-place and out-of-place updates)

* Policy Improvement

* Visualization of value functions and greedy policies

## Problem Description
* Environment: A 4x4 grid world with two terminal states (top-left and bottom-right corners).

* Actions: up, down, left, right

* Policy: Equiprobable random policy (initially)

* Rewards: -1 per move, 0 at terminal states

* Transitions: Deterministic (except hitting walls, which results in no movement)

* Goal: Minimize the number of steps to reach terminal states (i.e., maximize cumulative reward)

## Concepts Demonstrated
* Iterative Policy Evaluation: Uses the Bellman expectation equation to evaluate a policy.

* In-place vs Out-of-place updates: Shows the difference in convergence dynamics.

* Policy Improvement Theorem: Demonstrates how greedy policies w.r.t. value functions can yield improved or optimal policies.

## Results
* The final value function reflects the negative expected number of steps to termination.

* The greedy policy derived from value function is optimal in this Gridworld setup.
