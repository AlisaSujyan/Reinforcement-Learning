# Cliff Walking     
### Temporal-Difference Learning: SARSA vs Q-learning
This project implements and compares two Temporal-Difference (TD) control methods—SARSA and Q-learning—on the classic Cliff Walking gridworld environment, as described in Reinforcement Learning: An Introduction by Sutton & Barto.

### Q-learning: Off-policy TD Control
Q-learning is an off-policy TD control algorithm that learns the optimal action-value function 
𝑞 ∗ ( 𝑠 , 𝑎 ), regardless of the policy being followed during learning.

It updates action-values using the rule:
$$
Q(S_t, A_t) \leftarrow Q(S_t, A_t) + \alpha \left[ R_{t+1} + \max_a Q(S_{t+1}, a) - Q(S_t, A_t) \right]
$$
Even if the behavior policy is exploratory (e.g. 
𝜀
ε-greedy), Q-learning converges to the optimal policy under standard conditions. However, because it learns the greedy policy but follows an exploratory one, its online behavior may still be suboptimal during learning.

### SARSA: On-policy TD Control
In contrast, SARSA is an on-policy TD control method, which updates its action-values using:
$$
Q(S_t, A_t) \leftarrow Q(S_t, A_t) + \alpha \left[ R_{t+1} + Q(S_{t+1}, A_{t+1}) - Q(S_t, A_t) \right]
$$
SARSA learns the values of the policy it is actually following, which allows it to avoid risky states during training, resulting in safer online performance even if the final policy is suboptimal.


## Experiment: Cliff Walking
This gridworld is a classic testbed to contrast the two methods. The agent must move from a start to a goal while avoiding a cliff edge:

* All moves get a reward of -1

* Stepping into the "Cliff" gives -100 and sends the agent back to start

* γ=1, i.e. undiscounted

* ε=0.1 for both algorithms

## Observation
* Q-learning learns the shortest path (right next to the cliff), but due to 
ε-greedy exploration, it often falls off.

* SARSA learns a roundabout but safer path.

* As a result, SARSA performs better online, though Q-learning converges to the optimal policy.

##  Code Structure and Functions

* Hyperparameters: Define grid size, start/goal states, actions, discount factor, exploration rate (ε), and learning rate (α).

* step(state, action): Returns the next state and reward after taking an action, including penalties for falling off the cliff.

* choose_action(Q, state): Selects an action using ε-greedy policy (random with probability ε, else best action).

* sarsa(Q, expected=False): Runs one episode of SARSA or Expected SARSA, updating Q-values on-policy.

* q_learning(Q): Runs one episode of Q-learning, updating Q-values off-policy using the max next action.

* print_optimal_policy(Q): Prints the best action per state as arrows, showing the learned policy.

## Results & Analysis
#####  Reward per Episode
[The plot](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/cliff-walking/generated_images/example_6_6.png) shows the total reward per episode over time for SARSA and Q-learning agents on the Cliff Walking task:
* Q-learning quickly learns the shortest path along the cliff, which has higher expected return if followed perfectly. However, because it still explores (due to ε-greedy behavior), it often falls into the cliff, receiving -100 and restarting.

* SARSA, being on-policy, learns to avoid the cliff altogether by accounting for its own exploratory behavior. This results in a longer path but more consistent rewards.

Conclusion: SARSA performs better during training, as it is safer. Q-learning is riskier during training but eventually converges to the optimal policy if ε→0.

##### Performance Across Step-Sizes
[The second plot](https://github.com/AlisaSujyan/Reinforcement-Learning/blob/main/cliff-walking/generated_images/figure_6_3.png) shows how different values of the step-size parameter α affect performance for SARSA and Expected SARSA:
* Expected SARSA is shown to be more robust across different α values.

* Smaller α values learn more slowly but are more stable.

* Higher α values can lead to faster learning, but at the cost of variance or instability.

Conclusion: Tuning α is important. Expected SARSA can offer a smoother balance between convergence speed and stability.
