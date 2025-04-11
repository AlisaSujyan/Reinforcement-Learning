# Tic-Tac-Toe Reinforcement Learning
This project demonstrates the application of Reinforcement Learning (RL) in training an AI to play Tic-Tac-Toe optimally. The AI learns by playing against an imperfect opponent and improving over time by estimating the probabilities of winning from each game state.

## How It Works
The AI maintains a value function, which is a table of numbers representing the estimated probability of winning from each possible board state.

The AI updates these probabilities, refining its estimates based on the results of previous moves.

The AI follows a greedy policy most of the time, selecting moves with the highest winning probability, but occasionally explores other moves to improve its learning.

The AI learns through self-play or against an opponent, continuously refining its strategy

## File Structure
The project is organized into the following files:

#### judge.py
This file contains the logic for managing the game, determining the winner and managing the state transitions between moves. The Judge class is responsible for controlling the flow of the game, ensuring that the player and the AI alternate moves.

#### player.py
This file defines the Player class, which represents the player (or AI) making the moves in the game. The player uses reinforcement learning to decide the best possible move based on the current state of the board. It can also make exploratory moves to improve its strategy.

#### state.py
This file contains the State class, which represents the current configuration of the Tic-Tac-Toe board. It includes methods for checking the available moves, updating the board after a move, and evaluating the board to determine if a player has won or if the game is a draw.

#### tic_tac_toe.py
This is the main file to run the game, integrating the Judge, Player, and State classes. It contains the main game loop, where the AI plays against the opponent.

## References
 Sutton R.S., Barto A.G. - [Reinforcement Learning: An Introduction (2nd edition)](https://archive.org/details/rlbook2018/mode/2up)
