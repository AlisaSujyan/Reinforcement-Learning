# Blackjack
This project implements Monte Carlo methods for solving the game of Blackjack, following exercises and examples from Chapter 5 of "Reinforcement Learning: An Introduction" by Sutton and Barto (2nd Edition).

We cover:
* Exploring Starts for finding the optimal policy.
* On-Policy First-Visit Monte Carlo Control with epsilon-soft policies.
* Off-Policy Evaluation using importance sampling.

### Project Structure

The main Python file contains:

- **Game Setup** 
  * Definitions for cards, player and dealer actions, and policies

- **Core Gameplay**
  * play() simulates a full round of Blackjack

- **Policies**
  * target_policy_player() – fixed strategy for the player

  * behavior_off_policy_player() – random behavior for off-policy sampling

- **Monte Carlo Algorithms**

  * monte_carlo_on_policy() – estimates value function under the player's policy

  * monte_carlo_es() – learns optimal policy via Exploring Starts

  * monte_carlo_off_policy() – evaluates a fixed policy with importance sampling

### Project Results

#### Figure_5_1: Value Function for Usable/Non-Usable Ace


* This figure contains two heatmaps or surface plots showing the state-value function under a fixed policy.

* One plot is for states where the player holds a usable ace, and the other for when they do not.

* The axes represent:

  * Player's sum (from 12 to 21)

  * Dealer's visible card (from 1 to 10)

* The values show the expected return from each state under the player's policy.

Figure visualizes how the presence of a usable ace affects decision quality.
Shows how state values vary depending on what card the dealer shows.
Also it's a direct output of Monte Carlo on-policy prediction.

#### Figure_5_2: Optimal Policy and Value via Exploring Starts

* This figure is split into:

  * A policy plot showing the optimal action (hit or stick) in each state

  * A value function plot learned under the optimal policy

* It results from applying Monte Carlo Exploring Starts (MC-ES) to estimate the optimal policy and value.

Figure shows how MC-ES can learn an optimal strategy from scratch through exploration.
The value plot shows how good it is to be in each state under the learned optimal policy.

#### Figure_5_3: Off-Policy Evaluation Error

* This is a line plot comparing two estimators:

  * Ordinary Importance Sampling

  * Weighted Importance Sampling

* The x-axis is the number of episodes

* The y-axis is the mean-squared error (MSE) of the value estimate compared to the true value

Figure highlights the variance and stability differences between the two estimators.
Figure shows that weighted importance sampling generally has lower variance and is more stable, especially in the long run.

## References
 Sutton R.S., Barto A.G. - [Reinforcement Learning: An Introduction (2nd edition)](https://archive.org/details/rlbook2018/mode/2up)
