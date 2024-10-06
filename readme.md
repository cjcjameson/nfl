# NFL Prediction Games

A script, notebook, and set of tools to help with selecting which team to lose in Scott Shimamoto's NFL Elimination Pool prediction game

## The Rules of the Game

- Each week of the NFL season, you need to pick a team you think will lose their game this week.
- You can only pick a team once per season
- If the team you pick wins or ties, then you're eliminated from the pool
- There are 500-1000 contestants; you win big if you are the very last one standing!
    - You win pretty well if you're in the top 5 or 10; even the top 25 gets some prize.
    - But it's heavily weighted to a small number of winners

## Other information

- I'm building this tool halfway through the season -- so I'll state which teams I've already picked and which week we're on
- The way the NFL season works, teams have "bye" off-weeks where they don't play
- The playoffs matter, but to start with, let's just try to survive the regular season
- Ties can happen, but let's assume no-ties, for simplicity
- Predicting which team will win an NFL game is hard -- even lopsided matchups, the less-favored team is 20-25% likely to win
- I'll want to use a few factors to evaluate likelihood of a team winning or losing:
    - ELO rating
    - Home/Away/Neutral; home teams are stronger, away teams are weaker
    - Rest-days: a team coming off a bye or previous-week-Thursday are stronger. Teams that played last Monday are weaker.
- I want to be more confident in this week's outcomes; for later in the season, the predicted outcome of a game should be closer to 50/50

## Product Spec: Analytical Approach

### Phase 1: Simulate some seasons "season-runs"

- Given the schedule of an NFL season
    - Who's playing who, which week
    - The dates and locations of games
- Given the current strengths of teams
    - And some reasonable variation
- Given some manual adjustments, if I want to put in injury or personal preference information

"Play out" and store 100, or 1000, or 10,000 runs of the NFL season ... just the raw "who won each game, each week"

### Phase 2: Propose some Elimination-Pool selection strategies

Blindly pick a team to lose for each week, a team that's playing each week, and a team that hasn't been picked before

Create 100, or 1000, possible strategies

### Phase 3: Score the naive, broad strategies

Cross-compare each strategy against all the runs of the seasons. How many weeks does each strategy x season-run last before being incorrect?

For a given strategy:
- how often does it survive the whole season? As a percentage or just raw count of season-runs.
- how often does it survive "late" into the season... weeks 15, 16, or 17?

Calculate a composite score for how successful each strategy was: weight more heavily a full season survival but give partial credit for surviving many weeks

### Phase 4: Recommend good teams to pick for each week

Take the top 30 strategies (and double-count the 5 best-strategies, to give them more weight) ... what teams do they pick to lose each week?

List out the common teams for each week, that are shared among the best strategies.

Look for patterns:
- Maybe one week, it's just clear that the best strategies always pick a certain team. That needs to be a "Lock"
- Maybe one week, there are lots of different possible strategies

### Phase 5: Create new "recommended" strategies

Re-do Phase 2, but with "smart" strategies based on the recommendations from phase 4.

Still leave some room for random selection, but "hill-climb" towards using the recommended teams

## Technical Spec: what tools to use

- https://pypi.org/project/nfl-data-py/ `pip install nfl_data_py`
    - `nfl.import_schedules(years)` Returns dataframe with schedule information for years specified
- Pola.rs for data processing
    - Try to use the native Arrow columnar storage, but stay flexible; I'm not sure what the most efficient format will be
    - Consider DuckDB if I need to look at the data with SQL
- Consider a jupyter notebook if I need to write and show this like a notebook

## FAQ with Augment-AI

Q: Why use Polars instead of Pandas?
A: Polars is chosen for its speed and efficiency, especially for larger datasets. It's also now at version 1.0+, making it a reliable choice for data processing.

Q: How will the NFL schedule data be obtained?
A: We'll be using the nfl_data_py library (https://pypi.org/project/nfl-data-py/). Specifically, the `nfl.import_schedules(years)` function will be used to get schedule information.

Q: How will team strengths be determined?
A: Initially, ELO ratings will be manually input from a webpage. Future iterations may automate this process.

Q: How complex will the initial simulation be?
A: We'll start with a simple 50-50 simulation and incrementally add complexity, incorporating factors like ELO ratings, home/away advantage, and rest days.

Q: How will strategies be generated?
A: For Phase 2, strategies will be generated completely randomly, ensuring each team is only picked once per season.

Q: What scoring system will be used?
A: We'll use an exponential scoring system: (weeks survived / total weeks)^2 * 100. This gives more weight to strategies that survive longer.

Q: What will the user interface be?
A: The project will primarily use Jupyter notebooks as both the development environment and user interface.

Q: Is there a timeline for the project?
A: The initial goal is to have a working version within a week, with approximately 4 hours of coding time.

Q: How will the effectiveness of the predictions be tested?
A: Testing will initially be a manual process, relying on the developer's NFL knowledge. The script will highlight "surprising" or "controversial" picks for further scrutiny.

Q: Will the project include NFL playoffs?
A: Not initially. The focus is on the regular season for now.

Q: How will the code be structured?
A: The code will use classes to represent entities like teams, weeks, seasons, and strategies, promoting a clean and modular structure.