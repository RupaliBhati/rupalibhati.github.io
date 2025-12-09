---
layout: archive
permalink: /projects/interactive_visualisation/
title: <center>Interactive Visualization of Board Game Play for Humans and AI</center>
author_profile: true

---
<center>Mingyi Li and Rupali Bhati</center>
<center>CS 7250 Fall 2025 Final Project</center>

### Motivation

Artificial Intelligence (AI) agents are now achieving superhuman performance in many games. The strategies developed by such agents can be used to help human players improve their strategies. Moreover, interactive visualisation tools can help users understand complex AI strategies.
To support this, we designed a recommendation-based visualization tool that board-game players and researchers can use while they are playing the game. The tool highlights both AI-generated and human strategies, where the AI-generated strategy is a pre-trained algorithm, while the human strategy is the most probable human move based on the collected data. Visualising both the AI agent and human strategies on the same board makes it easier for the user to understand and compare how different players—human or artificial—approach the game. We demonstrate the tool using Tic-Tac-Toe, a simple and highly interpretable setting that allows users to clearly see how the recommendations work. 


### Data

#### Existing Datasets
For the task of studying Tic-Tac-Toe moves, we found two publicly available datasets:
- [Tic-Tac-Toe Endgame Data Set](https://www.kaggle.com/datasets/rsrishav/tictactoe-endgame-data-set): This dataset contains information about multiple games. Each row in the dataset contains the final configuration of the board as well as labels of win and loss. The columns in this dataset are the 9 positions of the board and a final column of win/loss/draw. However, this dataset does not contain information about moves at each step of the game and, therefore, is not suitable for our analysis.
- [Tic-Tac-Toe Machine Learning dataset](https://www.kaggle.com/datasets/fabdelja/tictactoe/data): In this dataset, for each game, all moves made by the players are recorded along with the final result of the game (win/lost/draw). The columns show moves 1 to 7 and a final column indicating a win/loss/draw. However, the description of this dataset is incomplete. According to the description of the dataset, the game terminates after 3 up to 9 moves. However, the Tic-Tac-Toe game requires at least 5 moves for it to terminate (3 from the winner and 2 from the loser). Moreover, this dataset only contains 7 moves, whereas there are a total of 9 moves in the game of Tic-Tac-Toe. Therefore, this dataset is not suitable for our analysis.


This renders us in a position to collect our own dataset. We have created a game engine as explained below. 

#### Human Data

#### AI Data

### Task Analysis

#### Interview Findings

#### Domain Task Analysis

### Design Process

#### Design Sketches


#### Usability Testing

### Final Visualization

#### Visual Encoding

#### User Interface

#### Implementation

### Data Analysis

### Conclusion
