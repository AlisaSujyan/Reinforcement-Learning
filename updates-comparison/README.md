# Expected vs. Sample Updates
## Project Overview

This project investigates the trade-offs between expected updates and sample updates in reinforcement learning, focusing on their computational costs, accuracy, and scalability.

- **Sample update**: Updates based on a single sampled successor state.

- **Expected update**: Updates by averaging over all possible successor states, weighted by their transition probabilities.

### Problem Description

We analyze updates to the action-value function 𝑄(𝑠, 𝑎) for a given state–action pair (𝑠, 𝑎).

- Let 𝑏 denote the branching factor, i.e., the number of possible next states 𝑠′ with nonzero probability 𝑝(𝑠′∣𝑠, 𝑎).

- Sample update requires one evaluation of 𝑄.

- Expected update requires evaluating 𝑄 for all 𝑏 successors.

Thus, an expected update is roughly 𝑏 times more computationally expensive than a sample update.

### Learning Algorithms
1. **Sample Update**

        Q(s,a)←Q(s,a)+α(r+γQ(s′,a′)−Q(s,a))
* Uses one sampled transition.

* Noisy but computationally cheap.
   
2. **Expected Update**


    Q(s,a)←Q(s,a)+α(r+γs′∑​p(s′∣s,a)a′max​Q(s′,a′)−Q(s,a))

* Uses all possible successor states.

* More accurate but costly when 𝑏 is large.

### Results
* For small branching factors, expected updates can be optimal: their higher cost is offset by variance-free estimates.

* For moderately large branching factors, the error drops dramatically after only a tiny fraction of 𝑏 sample updates.

  * In the same time required for a single expected update, many state–action pairs can be updated with sampling.

  * This achieves nearly the same accuracy (within a few percent) as an expected update.

* In large-scale problems with many state–action pairs, sample updates are far more efficient:

  * Better coverage of the state–action space.

  * Faster overall improvement despite individual updates being noisier.

#### [Performance]()
* The figure shows RMSE vs. number of computations for different branching factors 𝑏.

* As 𝑏 increases, expected updates become more expensive, and sample updates provide faster error reduction per computation unit.
