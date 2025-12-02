# Maze Game & Player Behaviour Analysis

## 1. Overview

I designed and developed a small mobile maze game using Unity to investigate how players explore routes and adapt their behaviour under time pressure, and how they respond to rewards. The game procedurally generates maze layouts and records player behaviour as timing and decision measures on each trial.

<p align="center">
  <img src="media/screenshot_3.jpg" width="300">
</p>

This project combines my background in cognitive science and time perception with my interest in game analytics. It allowed me to build a full pipeline from experimental/game design, to gathering gameplay data, to analysing game and player data in R, and finally reaching behavioural insights.

------------------------------------------------------------------------

## 2. Goals & Research Questions

The main goal of this project was to assess how players improve their mental agility, develop strategies, and adapt their behaviour to quantitative rewards in a game environment. More specifically, I was interested in questions such as:

-   How do **response times** change with practice?
-   How do specific **maze features** (turns, crossings, dead ends) affect performance?
-   Can we detect different **exploration strategies** from the sequence of visited cells?
-   How does the **reward** **system** affect player **engagement**?

These questions are closely related to my PhD work on time perception and uncertainty, but here they are applied to a game context.

------------------------------------------------------------------------

## 3. Game Design & Data Collection

The game presents players with a series of maze trials in which there are two possible paths, but only one leads to the exit. On each trial, a maze is **procedurally generated**, with path size and complexity varying across trials. The player must detect which path connects to the exit and respond as quickly as possible to earn points according to their accuracy and speed.

After the response, feedback is given by revealing the correct path in different colours depending on the success of the response. The score of each maze trial is stored and displayed at the top of the screen, accumulating the scores of 10 mazes in a row. After 10 mazes are completed, the block ends and the final average score is revealed.

To analyse behaviour, I implemented a custom **JSON data logging** **system** in Unity:

-   **Block data** summarises information corresponding to a set of 10 trials. This data informs about general performance within a session.
-   **Trial data** logs information to identify each maze, such as its timestamp, path length, response, completion time and score. This is where most of the behavioural information is stored and can be used to evaluate detailed performance, learning and biases.
-   **Cell data** stores the coordinates and type of each cell in the maze (turn, crossing, dead end, etc.). This allows me to rebuild each maze and evaluate factors that could influence difficulty or promote certain strategies or tendencies.

------------------------------------------------------------------------

## 4. Analysis Approach

I used **R** for data cleaning, transformation and visualisation. The analysis pipeline includes:

-   Importing and combining JSON files from different devices and sessions.
-   Creating tidy data tables for blocks, trials and cells.
-   Transforming player data to control for individual differences when needed (e.g., standardising response times within players).
-   Exploring relationships between:
    -   Difficulty metrics and performance metrics such as response time and accuracy;
    -   Learning/practice across game sessions.
-   Understanding factors that could influence participants to stop playing the game.
-   Looking for biases or systematic tendencies in participants’ responses that could help us predict what and when they are going to respond.

At this stage, the analysis is ongoing, but the current version already shows consistent patterns in how players adapt to the maze structure and when they decide to stop or keep playing.

------------------------------------------------------------------------

## 5. Key Findings (so far)

Although the project is still in progress and the analysis is being refined, preliminary explorations suggest:

### Player engagement and churn

- The dataset contains **3,043 mazes played by 11 players over 24 days**, with a highly unbalanced distribution: a few "power users" account for most of the games, while others only tried a handful of blocks.
- **Block churn** (blocks started but not completed) is relatively rare (~5%) and mostly concentrated in casual players. Players who played more blocks almost never left a block unfinished, showing great engagement.
- When blocks are abandoned, churn shows a **bimodal pattern**: some players quit after the first mazes (usually following negative scores), while others quit near the end of the block when the average score is still low despite a good last maze.
- Using a robust, data-driven threshold on the time gap between blocks, I separated short within-session breaks from **session endings**. Pauses inside a session show a weak inverted-U relationship with block score: players take the shortest breaks after very low or very high scores, and slightly longer pauses after mid-range scores. This suggests that both big failures and big successes can immediately drive re-engagement.

### Maze generator, fairness and difficulty

- The **maze generator is fair** with respect to the final outcome: the correct exit side (top vs bottom) is almost perfectly balanced across mazes.
- The **path length** (number of cells from the correct entrance to the centre) follows a right-skewed unimodal distribution, with most mazes having paths around 40–45 cells and very few extremely short or extremely long paths.
- Cell-type analysis (dead ends, corridors, crossings and turns) shows that most mazes are composed mainly of **turns and corridors**, with dead ends and crossings making up a smaller proportion. Turn cells are especially frequent around the centre, while dead ends tend to appear at the corners.
- As expected, **longer paths lead to slightly longer response times**, both for correct and incorrect decisions. The effect is statistically reliable but small, which is consistent with the idea that players sometimes use shortcuts rather than following the full path.
- The **“horizon” between the regions captured by each algorithm** is usually centred with a moderate vertical range. However, mazes where this horizon extends over a wider range of rows tend to be **less accurately solved**, suggesting that vertically “twisty” paths are more confusing and drive more errors.

### Behaviour and decision-making

- Players performed **well above chance**, with individual accuracies ranging from about 68% to 86% (mean ≈ 76%) and relatively low variability across players.
- **Response bias** towards one side (top vs bottom) was generally small. Apparent biases were only observed in players with very few trials, where random variations are hard to distinguish from true preferences.
- Response-time distributions show that **most decisions are made well before the 10-second limit**, typically within the first half of the available time.
- The data reveals a clear **speed–accuracy tradeoff**: very fast responses (0–1.5 s) are noticeably less accurate, while accuracy stabilises once players spend at least ~1.5 s on the decision. This is an important design insight: the current 10-second window is generous enough to allow careful decisions without strongly penalising thoughtful play.

### Learning and individual differences

- At the **overall scale**, most players show a gradual decrease in response time as they accumulate trials, with accuracy staying stable or improving slightly. This is consistent with **long-term learning** (better strategies, more familiarity with the interface).
- At the **session level**, there is some evidence of a short **warm-up period**: early trials in a session are often a bit slower, and performance stabilises after a few blocks. Effects on accuracy are weaker and more heterogeneous across players.
- At the **block level** (within 10-maze sequences), response times and accuracy remain relatively stable across trial positions. Players do not seem to systematically speed up or slow down as the block progresses, nor does accuracy peak in specific trials.
- Across all these analyses, there are clear **individual differences** in engagement (how much and when people play), typical response times, accuracy, and learning profiles. “Heavy” players tend to show more stable behaviour and lower block-churn rates than casual players.
  
(I plan to update this section as I refine the analysis and run more robust models.)

------------------------------------------------------------------------

## 6. Learnings & Next Steps

From a **data and analysis** perspective:

- I now have a complete end-to-end pipeline: local JSON files from the game in Unity, data cleaning and reproducible report in R. This allowed me to explore engagement, difficulty and learning using the same dataset.
- The current analyses are mostly descriptive or based on simple regression models. A natural next step is to move towards **mixed-effects models** that explicitly account for the nested structure of the data (trials within blocks, blocks within sessions, sessions within players) or even computational models for complex processes.
- The structural metrics of the maze (path length, cell types, horizon centre and range) already show promising links to performance. I plan to **extend the difficulty metrics** (e.g., path symmetry, similarity between consecutive mazes, distance of the path from the maze borders) and build predictive models of maze difficulty and player performance.
- With more data, I also want to analyse **post-error behaviour** (how mistakes affect subsequent RT and accuracy) and **serial dependencies** (e.g., whether a run of correct/incorrect answers changes risk-taking and speed).

From a **game design and UX** perspective:

- Block and session churn analyses suggest that **early performance in a block** is especially important: starting with a few bad mazes increases the risk of quitting. This informs potential design changes such as “gentler” early mazes, dynamic difficulty adjustment, or tailored feedback after very negative starts.
- The speed–accuracy tradeoff shows that **players usually do not need the full 10 seconds** to make a good decision, but very fast answers are risky. This supports the current timing/scoring scheme, and opens the door to future experiments with shorter deadlines or alternative reward functions.
- The horizon and cell-type analyses help identify **structural patterns that make mazes genuinely harder**, rather than just visually noisy. These insights can be used both to design more engaging levels and to avoid generating mazes that are unfairly confusing.
- Finally, the clear individual differences in engagement and performance suggest that the game could benefit from **personalised difficulty curves or recommendations** (for example, nudging “power users” to take breaks when performance drops, or offering easier mazes to new players who struggle at the beginning).

On the **technical side**, the next steps include:
- Moving from local JSON files to a **remote database / backend** to collect data from more players in the wild.
- Packaging the current R code as a **reusable analysis notebook or dashboard** so that I can quickly re-run the entire pipeline when new data comes in.
- Keep developing the game to solve bugs and add new features that provide more gameplay options as well as new aspects to analyse. 
