---
layout: archive
permalink: /projects/interactive_visualisation/
title: <center>Interactive Visualization of Board Game Play for Humans and AI</center>
author_profile: true

---
<center>Mingyi Li and Rupali Bhati</center>
<center>CS 7250 Fall 2025 Final Project</center>



# Motivation

Artificial Intelligence (AI) agents are now achieving superhuman
performance in many games. The strategies developed by such agents can
be used to help human players improve their strategies. Moreover,
interactive visualisation tools can help users understand complex AI
strategies. To support this, we designed a recommendation-based
visualization tool that board-game players and researchers can use while
they are playing the game. The tool highlights both AI-generated and
human strategies, where the AI-generated strategy is a pre-trained
algorithm, while the human strategy is the most probable human move
based on the collected data. Visualising both the AI agent and human
strategies on the same board makes it easier for the user to understand
and compare how different players---human or artificial---approach the
game. We demonstrate the tool using Tic-Tac-Toe, a simple and highly
interpretable setting that allows users to clearly see how the
recommendations work.

# Data

## Existing Datasets

For the task of studying Tic-Tac-Toe moves, we found two publicly
available datasets:

-   Tic-Tac-Toe Endgame Data Set
    [\[link\]](https://www.kaggle.com/datasets/rsrishav/tictactoe-endgame-data-set):
    This dataset contains information about multiple games. Each row in
    the dataset contains the final configuration of the board as well as
    labels of win and loss. The columns in this dataset are the 9
    positions of the board and a final column of win/loss/draw. However,
    this dataset does not contain information about moves at each step
    of the game and, therefore, is not suitable for our analysis.

-   Tic-Tac-Toe Machine Learning dataset
    [\[link\]](https://www.kaggle.com/datasets/fabdelja/tictactoe/data):
    In this dataset, for each game, all moves made by the players are
    recorded along with the final result of the game (win/lost/draw).
    The columns show moves 1 to 7 and a final column indicating a
    win/loss/draw. However, the description of this dataset is
    incomplete. According to the description of the dataset, the game
    terminates after 3 up to 9 moves. However, the Tic-Tac-Toe game
    requires at least 5 moves for it to terminate (3 from the winner and
    2 from the loser). Moreover, this dataset only contains 7 moves,
    whereas there are a total of 9 moves in the game of Tic-Tac-Toe.
    Therefore, this dataset is not suitable for our analysis.

This renders us in a position to collect our own dataset. We have
created a game engine as explained below.

## Human Data

We built a Tic-Tac-Toe website[^1] to collect data. This website was
created using React and TypeScript.
Figure [1](#fig:wesbite_UI) shows the user interface of the website.
Here, a game ID is generated for each game. Moreover, we ask the players
to enter their names and their experience levels to aggregate over
players and experience levels. In
Figure [2](#fig:Game_UI),
we show one game. In the top half of the figure, we see the game being
played. In the lower half, we show a table preview of the actual data
that we are logging. Each row is a move from the player. The columns of
the table indicate the current state of the board as "S\", the move
played by a player as "move\", the player who played this move as
"player\", the next state of the board as "S\", and finally the outcome
of the game as "outcome\" that can take the values of "win\", "loss\" or
"draw\". You may notice that there aren't any "X's\" and "O's\" in the
table. For data analysis purposes, we indicate each place on the board
as a number. For example, an X is represented as 1, an empty space is
represented as 0, and an O is represented as -1. The move of each player
is the number of the cell on the Tic-Tac-Toe board. These numbers begin
from the top left corner, starting with 0, and span across each row
before moving to the next row. The move history on the interface
displays only part of the data that we collected and uploaded to the
database.

<figure id="fig:wesbite_UI">
  <img src="/assets/Interactive_viz_images/wesbite_UI.png" alt="User interface of the website used to collect data" style="width: 50%;">
  <figcaption>Figure 1: User interface of the website used to collect data.</figcaption>
</figure>



<figure id="fig:Game_UI">
  <img src="/assets/Interactive_viz_images/Game_UI.png" alt="Data Collection UI that shows the interactive game board and a preview of logged move history." style="width: 50%;">
  <figcaption>Figure 2: Data Collection UI that shows the interactive game board and a preview of logged move history.</figcaption>
</figure>

We collected all user data using a Firestore database [^2]. There are
two dataset beings stored: games and moves. The "games" dataset stores
all the game information, such as the player name, experience level, and
game duration. The "moves" dataset stores all individual moves
information, such as the previous and current board state (S and S' in
Fig [2](#fig:Game_UI)),
current move, player, outcome, and game ID that associates the move data
to the game data.

In our collected human dataset, the values are primarily quantitative,
which include the board states, moves, and players. This is because we
transform some categorical values into quantitative values for data
analysis purposes. For example, we encode all X moves as 1, O as -1, and
the board position from the upper left corner to the lower right corner
as 0 to 8. On the other hand, for the values that are associated with
the player and game status, the experience level
(novice/intermediate/expert) is ordered, and player names and game IDs
are nominal.

We did not have too much "dirty" data. However, while testing the
interface, we entered some test fields and played some testing games
that were also stored in the database. We had to separate these games
from the actual player data and make sure that while collecting the
data, players were not playing against themselves. To deal with this
situation, we added a "submit\" button after a game has ended. If a game
was played for the testing purposes or if people wanted to play it with
themselves for fun, they had the option to not submit the data at the
end. In this way, not all moves would be logged automatically.
Additionally, an unexpected situation was that the board state data
(stored as lists) could be hard to handle in the CSV format. Therefore,
we later also recorded the board state as a string (X as '1', O as '-1',
empty position as '.').

## AI Data 

We trained two AI agents:

#### AI - Agent 1

We trained an agent using the popular reinforcement learning algorithm
Q-learning [(cite)]{style="color: red"}. Q-learning is a model-free
method, which makes it especially useful for scaling to games more
complex than Tic-Tac-Toe. For our experiments, we set the learning rate
to 0.1 and trained the agent for 10,000 episodes to ensure it had
sufficient experience to learn effective strategies.

#### AI - Agent 2

We also trained an agent using a dynamic-programming-based reinforcement
learning method called Value Iteration [(cite)]{style="color: red"}.
This approach is simple and interpretable, making it easy to understand
how the agent evaluates states and updates its decisions. However,
because Value Iteration requires sweeping over the entire state space,
it does not scale well to larger or more complex games. In our setup, we
used a convergence threshold of 1e-6 and allowed up to 1,000 iterations.
This means that the algorithm will train until either the value updates
are less than 1e-6 or until 1000 iterations have been reached. This
ensures the algorithm reached a stable solution.\
Note that for both the AI agents, if the probability of multiple moves
is the same, it randomly breaks ties. Moreover, we have not trained
these agents fully because the game of Tic-Tac-Toe is too easy, and the
agents will give optimal answers. We wanted to emulate a scenario where
the task is complex, and the AI agents and the humans have diverging
opinions.

# Task Analysis

## Interview Findings

We learned that both participants were interested in comparing different
AI strategies. P2-player wanted to view AI decisions to compare their
differences and similarities, while P1-researcher wanted to know the
state in which AI algorithms diverge and disagree the most.

Both participants also showed interest in viewing a sequence of board
game states, which is how players make step-by-step moves. This would
help them learn about game-play strategies at a particular game state.
P1-researcher would want to further identify how optimal a move was, the
moment when the game went wrong, and the percentage of players following
optimal strategies. Similarly, the P2-player would like to know the win
rates of game moves to learn about the best decisions.

## Domain Task Analysis

We conducted affinity diagramming and abstracted the domain tasks
following Brehmer & Munzner's typology. We further ranked the task
priority by their levels of importance. The task analysis results are
represented in
Table [\[tab:domain_tasks\]](#tab:domain_tasks).

We focused on three core tasks: for each move on the board, viewing the
next move recommendations made by different AI agents or the most
frequent next move made by human players. This is because both
stakeholders (player and researcher) indicated interest in viewing how
AI agents play the game and comparing the decisions between different AI
agents (Task 1, 3). To complement the AI data, we collect some human
decisions to reveal how human players play the game (Task 2). We decided
to assign a lower task priority to each move's win rate, because
computing win rates from the data we collected (with a small sample
size) could be inaccurate, while showing the most frequent human next
move would be more understandable and feasible based on our datasets.



|        |    Domain Task         |  Abstraction | Task Level |  Stakeholder |  Priority |
| ------------- |:-------------:|  :-------------:|:-------------:|:-------------:|  -----:|
| 1 |   View move recommendations made by different AI agents    | Analyze, consume, discover | High Level |Both |  Most important|
|  2  |Know the most frequent next moves made by human players   | Query, compare              | Low Level       | Player           | Most important |
|  3  | Know the distribution of AI's next move probabilities     | Query, summarize            | Low Level      |  Both             | Most important |
|  4   |Identify the win rate or optimality of a move             | Analyze, consume, discover   |High Level      | Both            |  Medium important|
|  5  | Find the percentage of people playing optimal strategies  | Query, summarize           |  Low Level       | Researcher       | Least important|
|  6  | Investigate the first-move advantage                      | Analyze, produce, derive    | High Level      | Researcher       | Least important|



# Design Process

## Design Sketches

We sketched our interface based on the interview findings and domain
task analysis. In
Figure [3](#fig:digital_prototype_main), the left section is the game
being played, and the right section is a visualization board showing
recommended moves by AI agent 1 (AI-1) in blue, AI agent 2 (AI-2) in
green, and Human in red. The two AI agents are two different algorithms
recommending their best move. The Human move is the most common move
taken by humans based on our collected data. This visualization provides
an overview of human and AI strategies at a board state.

<figure id="fig:digital_prototype_main">
  <img src="/assets/Interactive_viz_images/digital_prototype_main.png" alt="Digital sketch of visualization" style="width: 50%;">
  <figcaption>Figure 3: Digital sketch of visualization.</figcaption>
</figure>

As shown in Figure [3](#fig:digital_prototype_main), there are four interactive
actions that a user can take from this screen:

1.  **Action 1: Make a move on the board** When a player makes a move on
    the board, the visualization changes as shown in
    Figure [4](#fig:Action_1). Note that the recommendation board is
    linked to the game and also changes. In this case, the AI-1 and the
    Human are recommending the same move. We chose to display human or
    AI suggested moves on a board in parallel so users could easily view
    the next possible actions. By showing different recommendations on
    the same board, the visualization also supports comparison between
    human/AI or AI/AI strategies.

    <figure id="fig:Action_1">
      <img src="/assets/Interactive_viz_images/Action_1.png" alt="Action 1: Make a move on the board" style="width: 50%;">
      <figcaption>Figure 4: Action 1: Make a move on the board.</figcaption>
    </figure>


2.  **Action 2: Select or deselect player strategies** The player can
    select or deselect which recommendations they want to see. For
    example, if the player only wants to see the recommendations from
    the Human, they may deselect AI-1 and AI-2 as shown in
    Figure [5](#fig:Action_2). We chose to use the filtering technique
    to enable the player to customize particular combination of
    recommendations that they want. For example, selecting AI-1 and AI-2
    together to compare AI strategies or selecting AI-1 and human to
    compare the decisions between human players and AI-1.

    <figure id="fig:Action_2">
      <img src="/assets/Interactive_viz_images/Action_2.png" alt="Action 2: Select or deselect player strategies" style="width: 50%;">
      <figcaption>Figure 5: Action 2: Select or deselect player strategies.</figcaption>
    </figure>

3.  **Action 3: Click on AI-1 to see a heatmap of the probability of the
    next move** When a player clicks on AI-1 on the recommendation
    board, a heatmap of AI-1's probability of the next move will appear
    on the screen as shown in
    Figure [6](#fig:Action_3). We chose to use details-on-demand
    technique to avoid overloading players while helping a player
    understand the distribution of AI suggested moves.

    <figure id="fig:Action_3">
      <img src="/assets/Interactive_viz_images/Action_3.png" alt="Action 3: Click on AI-1 to see a heatmap of the probability of the next move" style="width: 50%;">
      <figcaption>Figure 6: Action 3: Click on AI-1 to see a heatmap of the probability of the next move.</figcaption>
    </figure>

4.  **Action 4: Click on AI-2 to see a heatmap of the probability of the
    next move** Similar to action 3, when a player clicks on AI-2 on the
    recommendation board, a heatmap of AI-2's probability of the next
    move will appear on the screen as shown in
    Figure [7](#fig:Action_4). We keep actions 3 and 4 separate to not
    overwhelm the player with too much information.

    <figure id="fig:Action_4">
      <img src="/assets/Interactive_viz_images/Action_4.png" alt="Action 4: Click on AI-2 to see a heatmap of the probability of the next move" style="width: 50%;">
      <figcaption>Figure 7: Action 4: Click on AI-2 to see a heatmap of the probability of the next move.</figcaption>
    </figure>

## Usability Testing

We conducted our usability testing sessions with 4 students. We first
asked the participant to play a game of Tic-Tac-Toe and then use our
visualization tool to review the game that they played. They were also
encouraged to explore on the interface, including the feature that
selects different player strategies and displays AI heatmaps. Here are
some high-level notes and trends that we observed during the testing
sessions:

-   Unaware of the heatmap interaction: None of our participants noticed
    our heatmap interaction, even though we included a little hint below
    our visualization. Participants suggested placing heatmap hints
    anywhere else to make it more visible, adding an info icon next to
    the visualization header, or adding a short, animated gif when the
    user first opens the visualization website (suggested by our
    professor).

-   Unclear information about AI algorithms: Three of our participants
    showed interest in learning more about the AI algorithms that we
    used and why they could make different choices at some board
    configurations. Instead of encapsulating all the information in
    "AI1, AI2," P3 would like to get more information on which models we
    used, which parameters we set, and any other essential information
    that impacted the model performance. P2, P3, and P4 were confused
    about why the probabilities on the board cells had very close data
    (e.g., 0.14, 0.13) instead of having one move with significantly
    higher probability. P4 commented that when there was only one
    correct answer that led to success, the probability was still only
    off by 0.1. It would be helpful to have more distinctive
    probabilities, and one AI to be stronger, and another one could be a
    little weaker.

-   Refinement on the visualization interface: All Participants
    commented that our interface was fun, enjoyable, and simple. Still,
    they provided different comments on the future iteration of our
    interface. P1 suggested using different color coding for "occupied"
    and "open" cells for players to play on the board. P2 and P4 wanted
    a different visualization when two AI decisions collided (e.g.,
    alternating color, two symbols in the same cell). Both P3 and P4
    pointed out that having the grid lines in the heatmap could be
    helpful for understanding.

We have addressed the concerns in the final visualization.

# Final Visualization

## Visual Encoding

We applied the following visual encoding techniques to create our
visualization. First, we used **color-coding** for each game-play
strategy, including human, AI-1, and AI-2. This supports easy mapping
and differentiation between different strategies when users view the
board. The colors are based on the Tableau color palette, but we chose
three colors with enough contrast and avoided colors with existing
connotations, such as red and green, to reduce the bias of indicating a
move as the better or worse strategy. Next, we adopted **linking**
between the left board (game play) and the right board (recommendation
visualization) so the visualization will be updated in real-time while
players are making a new move. Also, the strategies on the
recommendation visualization could be **filtered** by selecting or
de-selecting the checkbox. This supports the flexibility of only viewing
AI-moves while comparing AI strategies or viewing human and AI's moves
while comparing human/AI strategies. Lastly, upon clicking the suggested
AI move on the right board, users could view a probability heatmap that
includes the distribution of probability among all the allowed positions
on the board. We used a **detail-on-demand** technique to avoid
overloading users and prioritized a single suggested move for AI,
leaving the probability details available only upon request.

### User Interface

In our visualization, there are two boards as shown in
Figure [8](#fig:interface-all). On the left side is the *Game Board*
where the game is being played. On the right side is the *Recommendation
Visualization*, which shows the recommendations of the two AI agents and
the most probable human move.

<figure id="fig:interface-all">
  <img src="/assets/Interactive_viz_images/interface-all.png" alt="User Interface" style="width: 50%;">
  <figcaption>Figure 8: User Interface.</figcaption>
</figure>

We have implemented the following interactive actions:

1.  Action 1: Make a move on the board. A player can make a move on the
    game board (see Figure [8](#fig:interface-all)) by clicking on the cell where they
    want to play

2.  Action 2: Select or deselect player strategies. We implemented the
    functionality to select and deselect player strategies. In
    Figure [9](#fig:checkbox), we have selected the AI-1 and the Human
    strategy, while we have deselected the AI-2 strategy.

    <figure id="fig:checkbox">
      <img src="/assets/Interactive_viz_images/checkbox-collision.png" alt="Users can select or deselection the player strategies using the checkbox. In the figure, when strategies overlap, we use a Venn Diagram to represent them in the same cell." style="width: 50%;">
      <figcaption>Figure 9: Users can select or deselection the player strategies using the checkbox. In the figure, when strategies overlap, we use a Venn Diagram to represent them in the same cell.</figcaption>
    </figure>

3.  Action 3: Click on AI-1 or AI-2 to see a heatmap of the probability
    of the next move. When the user clicks on a recommendation in the
    Recommendation Visualization board, a heatmap will pop up as shown
    in Figure [10](#fig:heatmap1) and Figure
     [11](#fig:heatmap2).
    The heatmap shows the probabilities of each move recommended by the
    AI agent. Hovering over the heatmap will display a detailed
    probability that the position has.

    <figure id="fig:heatmap1">
      <img src="/assets/Interactive_viz_images/heatmap-ai1.png" alt="The probability heatmap of AI-1 which indicates the middle position at the move with highest probability." style="width: 50%;">
      <figcaption>Figure 10: The probability heatmap of AI-1 which indicates the middle position at the move with highest probability.</figcaption>
    </figure>

    <figure id="fig:heatmap2">
      <img src="/assets/Interactive_viz_images/heatmap-ai2.png" alt="The probability heatmap of AI-2 which indicates the middle position at the move with least probability. It indicates that AI-2 has worse performance than AI-1." style="width: 50%;">
      <figcaption>Figure 11: The probability heatmap of AI-2 which indicates the middle position at the move with least probability. It indicates that AI-2 has worse performance than AI-1.</figcaption>
    </figure>

In addition, the interface includes an information icon that reminds
users about the probability heatmap interaction. It also has a separate
tab that explains the AI models (AI-1 and AI-2) that are used in the
recommendation visualization.

## Implementation

For the implementation of our visualization, we implemented the game
board using React and TypeScript. Users can interact with the board and
view real-time visualizations. To display the AI recommendations and the
most frequent human moves, we used React and TypeScript to add shapes
onto the game board. To generate visualizations for the probabilities of
AI's next moves (heatmap), we used Plotly [^3].

# Data Analysis

Collect more data for humans. Sometimes agents and humans suggest the
same move. Because of the way the agents are trained and due to
isomorphism in the game, the probabilities of many moves are closer in
range. If we train the AI agents for longer, or if we change the game
such that some moves are more obviously optimal, the probabilities of
the heatmap will change. The algorithms considered in this study are not
encouraged to keep the difference in probabilities of moves large. As
long as the optimal move gives the highest reward, that's good enough
for the algorithm.

AI-1 is often better and more sure of its choices as compared to AI-2.

From our observation of the human data, we noted that X players (who
play first) often tend to begin with the middle board position (i.e.,
position 4). Next, an interesting trend was that for the next move, if
the O player did not play the corner move (i.e., position 0, 2, 6, 8), O
would lose the game if X followed with optimal moves.

# Conclusion

We designed and built an exploratory visualization tool to help game
players and researchers investigate Tic-Tac-Toe strategies used by
humans and various AI models. Looking ahead, there are several
directions for future work, including personalizing visualizations based
on a player's game history, collecting more human gameplay data, and
examining additional AI models or variations of the current ones. We
also aim to expand the tool to support a wider range of board games,
strengthen the AI agents themselves, and explore more complex gaming
environments. With more data, we plan to add features such as a
timeline-style sketch that illustrates how each game unfolds, giving
users an even clearer understanding of strategic dynamics. This will
allow a user to analyse their own moves and compare them to the AI
models or the most probable human moves.

Authentically representing the difference in the probabilities of moves
and emphasizing the color contrast for users to be able to differentiate
the moves in the heatmap.

[^1]: https://tic-tac-toe-d1b97.web.app/

[^2]: https://firebase.google.com/docs/firestore

[^3]: https://plotly.com/javascript/