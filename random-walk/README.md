# Random Walk

## Overview 
This project implements and compares two classical reinforcement learning prediction methods — Temporal-Difference learning (TD(0)) and constant-α Monte Carlo (MC) — on a simple Markov Reward Process (MRP) designed as a random walk. The primary aim is to empirically examine which method better estimates the true value function over time.

## Problem Description
We simulate a random walk [environment](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk/book_images/Example_6_2_top.PNG) with 7 states:

*  Non-terminal states:  A, B, C, D, E (internally indexed as states 1–5)

*  Terminal states: Left (index 0) and Right (index 6)

Each episode begins in the center state (C, index 3) and proceeds left or right with equal probability. The episode ends when the agent reaches either terminal state. A reward of +1 is given only when the agent reaches the right terminal state, all other transitions result in a reward of 0.

The goal is to estimate the expected return (value) from each non-terminal state. The true value of a state is its probability of ending the episode on the right. For example:

* v(A) = 1/6

* v(B) = 2/6

* v(C) = 3/6

* v(D) = 4/6

* v(E) = 5/6

## Learning Algorithms
Two prediction methods are implemented:
1. Monte Carlo (MC) Prediction

   * Updates values using the final return at the end of the episode.

   * Must wait until episode ends.

   * Update rule:
   
         V(S_t) ← V(S_t) + α [G_t - V(S_t)]

    
2. Temporal-Difference (TD(0)) Prediction

   * Updates values after every transition using the TD target.

   * Learns from estimates ("bootstrapping").

   * Update rule:
   
         V(S_t) ← V(S_t) + α [R_{t+1} + V(S_{t+1}) - V(S_t)]

    
## Experiments

#### 1. Temporal Difference (TD) Estimations Over Time:

* Tracks estimated values using TD(0) after 0, 1, 10, and 100 episodes.

* Compares the estimates to the true values to visualize convergence.

#### 2. Learning Curve Comparison (TD vs MC):

* Calculates the Root Mean Squared Error (RMSE) between learned and true value functions.

* Runs 100 independent simulations to average RMSE results.

* Tests various step-size (α) values for both TD and MC.

* Shows that TD generally performs better and more consistently than MC.

#### 3. Batch Updating Version:

* Applies batch learning for both TD(0) and MC:

  * After each new episode, all previous episodes are replayed until value estimates converge.

  * Despite MC being optimal for sample returns, TD achieves better RMSE results.

* Reinforces the theoretical advantage of TD’s bootstrapping mechanism.

## Code Structure:
* [random_walk.py](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk/src/random_walk.py): Contains the core algorithm implementations:

  * monte_carlo(): Performs Monte Carlo prediction updates.

  * temporal_difference(): Performs TD(0) updates.

  * batch_updating(): Implements batch update logic for both methods.

  * Defines true state values and initial value estimates.

* [random_walk.ipynb](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk/notebooks/random_walk.ipynb):

  * Imports functions from random_walk.py

  * Executes the experiments and plots the results

  * Produces visualizations to compare the learning behavior of TD and MC.

## Results

#### [example_6_2.png](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk/generated_images/example_6_2.png)
First figure replicates the left plot of [Example_6_2_bottom.PNG](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk/book_images/Example_6_2_bottom.PNG) from the book. It shows how the estimated value function evolves over time using TD(0). Each curve represents the value estimates after a different number of episodes (e.g., after 0, 1, 10, and 100 episodes).

Key insights:

* Initially, all values start at 0.5.

* With more episodes, values gradually approach the true values.

* After 100 episodes, TD(0)'s estimates are close to the true values.


Second figure corresponds to the right plot of [Example_6_2_bottom.PNG](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk/book_images/Example_6_2_bottom.PNG) from the book. It compares the learning speed and stability of MC and TD(0) by plotting the RMSE over episodes.

Key insights:

* TD(0) consistently shows lower RMSE than MC for the same number of episodes.

* TD converges faster than MC across different values of α.

* MC suffers more from variance due to having to wait for full returns, while TD benefits from bootstrapping after each transition.

* The superiority of TD is especially clear in early episodes, where sample efficiency matters most.

#### [figure_6_2.png](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/random-walk/generated_images/figure_6_2.png)
This plot compares the performance of Temporal-Difference (TD) and Monte Carlo (MC) methods during batch training. The x-axis shows the number of episodes, while the y-axis represents the RMS error averaged over all states. Both methods reduce error over time, but TD consistently achieves lower error than MC. This demonstrates that TD learns more efficiently and converges faster, making it more effective for value prediction in batch settings.


### Conclusion
This project illustrates the practical advantage of TD learning over Monte Carlo methods in prediction tasks. TD methods:

* Learn online and incrementally.

* Require no model of the environment.

* Use data more efficiently, especially in stochastic or continuous tasks.

This makes TD(0) a foundational algorithm in modern reinforcement learning systems.

## References
 Sutton R.S., Barto A.G. - [Reinforcement Learning: An Introduction (2nd edition)](https://archive.org/details/rlbook2018/mode/2up)
