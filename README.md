# fifa-analysis

**Intro:** 

I love sports and outdoor activities - but if there's one thing that I can't seem to wrap my head around, it's going to be soccer. However, my boyfriend loves playing and watching soccer and I have recently enjoyed the festivities of watching 2024 Euros and COPA America match with him which led me to do this analysis as I thought it'd be fun to kick off my very first data project around soccer, specifically the 2022 QATAR World Cup! 

**Objectives:** 

In this project, I will analyze player performance in the Qatar FIFA World Cup 2022 by categorizing players into three distinct groups: Defense, Attackers, and Midfielders. By calculating composite scores based on key performance metrics, I aim to identify the top players in each category. The composite scores will be derived from relevant metrics, each weighted according to its importance, to provide a comprehensive assessment of player performance. I also aim to answer a couple interesting questions such as looking at the correlation between age and players performance. Lastly, I will develop a model that would predict the number of goals based on the composite scores we have computed. 

You can view the complete code and the original dataset in my Github repository here [insert link]

**Questions to be answered:** 

- Who are the top 10 attackers?
- Who are the top 10 defenders?
- Who are the top 10 midfielders?
- How does age affect players performance? Does age contribute to different position peak performance? (identify the prime age range for different positions)
- Although midfielders are not necessarily the one who scores the goal, I wanted to understand the importance of it. Do higher composite scores for midfield players correlate with a higher number of match wins?

**Dataset:** 

I used the [FIFA World Cup 2022 Player Data](https://www.kaggle.com/datasets/swaptr/fifa-world-cup-2022-player-data) dataset collected and created by [Swaptr](https://www.kaggle.com/datasets/swaptr) on Kaggle. This dataset contains game statistics of all the teams competing at the FIFA World Cup Qatar 2022. This series of dataset captures all the action, from player statistics and team standings, to game scores and match performances. I specifically used the players_defense, player_passing, player_possession, and player_shooting csv files from the player data. 

**Approach:** 

**1) Categorize players into 3 different categories**

- I will categorize players into 3 different categories: Defense, Attacker, and Midfielders. To categorize players effectively, I will group them based on their primary roles on the field:
    - Defense comprises of DF
    - Attacker comprises of FW
    - Midfielder comprises of MF

**2) Calculation of Composite Scores**

- For each category, I will identify key performance metrics (KPI) that are crucial for assessing the effectiveness of players’ cumulative performance. Considering that there are various KPIs to choose from the player dataset, I will use my best judgement in soccer (more like my boyfriend’s) to pick and choose which factors are crucial to include.
- Next, I will assign different weights to each of these KPIs based on its importance (i.e to assess attackers’ performance, scoring a goal would have higher weights compared to shots on target)
- Weighting metrics for composite score would sum up to 1. This would ensure interpretability and normalization, as it represents a weighted average (i.e 40% weight to goals scored and 15% weight to expected goals)
- Composite scores will be calculated by applying the weights to the standardized metrics and summing the results

**3) Assigning weighted average for each KPI to determine player from each category cumulative performance** 

**Attacker KPIs**
player_shooting

| KPI  | Description | Normalize Weight  | Rationale for weighting  |
| --- | --- | --- | --- |
| goals | Goals scored or allowed | +0.3 | This is the most critical metric for attackers as their primary role is to score goals |
| shots | Shots  | +0.1 | Indicator of how often an attacker is attempting to score, which reflects their offensive activity  |
| shots_on_target  | Shots on target (does not include penalty kicks) | +0.15 | Indicator of attackers’ accuracy and effectiveness hitting the target |
|  goals_per_shot_on_target | Efficiency in converting accurate shots | +0.15 | Indicator of players finishing ability when their shots are accurately aimed |
| goals_per_shot | Efficiency in converting all shots (does not include penalty kicks) | +0.1 | Important for overall efficiency but slightly less critical than Goals per Shot on Target. |
| xG | Expected Goals xG totals include penalty kicks, but do not include penalty shootouts. Provided by Opta | +0.15 | Players likelihood of scoring goal based on past performance |
| npxG | Non-Penalty Expected Goals Provided by Opta | +0.05 | Indicator of players performance in creating high-quality chances during regular play, which can be more reflective of their overall attacking strength. |

*Note: while goals_per_shot_on_target and goals_per_shot might not be mutually exclusive as I have already considered goals, shots, and shots_on_target as the KPI, I believe that these 2 metrics provide added value as they highlight different aspects of attacker shooting efficiency* 

**Defense KPIs**
player_defense

| KPI  | Description | Normalize Weight  | Rationale for weighting  |
| --- | --- | --- | --- |
| tackles  | Number of players tackled  | +0.2 | Tackles are crucial as they directly prevent the opposing team from advancing and creating scoring opportunities |
| tackles_won | Tackles in which the tackler's team won possession of the ball | +0.1  | Winning possession is important but slightly less critical than the act of tackling itself. |
| dribble_tackles | Number of dribblers tackled | +0.2 | Successfully tackling dribblers is highly valuable as it stops skilled opponents from making dangerous plays. |
| dribbled_past | Number of times dribbled past by opposing players | -0.1 | Being dribbled past indicates a defensive weakness |
| blocks | Numbers of times blocking the ball by standing in its path  | +0.1 | Blocking is essential for stopping shots and passes, contributing significantly to defensive strength. |
| interceptions | Number of times intercepting the ball | +0.2 | Interceptions are key to breaking up the opponent's play and initiating counterattacks. |
| clearances | Number of times clearing the ball from the defensive area. | +0.15 | Clearances are important for relieving pressure and preventing goal-scoring opportunities. |
| errors | Mistakes leading to an opponent’s shot | -0.1 | Defensive errors leading to shots are detrimental |

**Midfielders KPIs**
player_passing, player_posession

| KPI  | Description | Normalize Weight  | Reason for weighting  |
| --- | --- | --- | --- |
| passes_completed  | Total number of successful passes. | +0.1 | Key indicator of involvement and control in the game. High volume of completed passes shows consistency and reliability. |
| assisted_shots | Passes that lead directly to a shot. | +0.15 | Direct contribution to creating scoring opportunities, important for an attacking midfielder. |
| assists  | Passes that lead directly to a goal. | +0.15 | High impact on the game outcome, reflects direct contribution to the team’s success. |
| progressive_passes | Passes that move the ball significantly forward toward the opponent's goal | +0.1 | Demonstrates ability to advance play and create attacking opportunities. |
| pass_xa | The likelihood each completed pass becomes a goal assist given the pass type, phase of play, location, and distance. provided by Opta. | +0.1 | Reflects midfielders pass performance in assist  |
| dribbles_completed  | Number of successful dribbles | +0.05 | Indicates ability to bypass opponents and create space |
| xg_assist  |  | +0.15 |  |
| touches  | Number of times a player touched the ball. Note: Receiving a pass, then dribbling, then sending a pass counts as one touch | +0.05 | Shows overall involvement in play and ability to influence the game. |
| touches_mid_3rd  | Number of touches in the middle third of the pitch | +0.1 | Indicates control and influence in the central area, key for linking defense and attack. |
| miscontrols  | Number of times a player failed when attempting to gain control of a ball | -0.02 | Reflects technical skill and ability to maintain possession |
| dispossessed  | Number of times a player loses control of the ball after being tackled by an opposing player. | -0.03 | Indicates ability to withstand pressure and maintain possession |
| passes_received  | Number of times a player successfully received a pass | +0.05 | Shows availability and positioning, indicating ability to support team play |
| progressive_passes_received  | Number of forward passes received by the player | +0.1 | Demonstrates ability to receive and make use of progressive passes, important for advancing play. |

Please bear in mind that the above metrics and weighted scores are subjective and may differ from person to person!

**Tools:** 
- Python (Panda, Numpy, Matplotlib)
- Predictive modelling (Random Forest Regressor, Gradient Boost Regressor, SVR, Neural Network) 
