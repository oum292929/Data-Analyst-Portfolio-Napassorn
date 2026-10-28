# FIFA World Cup First-Goal Analysis (1990–2026)

## Project Overview

This project explores whether the team that scores the first goal tends to win a FIFA World Cup match, and examines how red cards received by that team are associated with match outcomes.

The project uses Python for data collection, cleaning, exploratory analysis, and visualization. It is designed to demonstrate a reproducible analytics workflow: translating questions into measurable outcomes, checking data quality, analyzing patterns, and communicating limitations.

> **Project status: validation in progress.** The original notebook contains inconsistencies between data-collection and visualization cells. Reported counts and percentages should be treated as provisional until the notebook is refactored and rerun from top to bottom.

## Business / Analytical Questions

1. Among matches with at least one recorded goal, how often does the first-scoring team win, draw, or lose?
2. How often does the first-scoring team fail to win after scoring first?
3. Within this dataset, how are red cards for the first-scoring team associated with its match outcome?

## Data Source

- **Source:** [openfootball/worldcup.json](https://github.com/openfootball/worldcup.json)
- **Coverage intended by the original notebook:** FIFA World Cup editions from 1990 through 2026.
- **Unit of analysis:** One match.
- **Fields used or intended:** tournament year, round, teams, score, goal minute, first-scoring team, match outcome, and red-card information.

The dataset is collected from the source repository by the notebook. Coverage, schema differences across tournament years, and completeness of event-level goal/card data must be validated before interpreting the results. The analysis excludes matches without a recorded goal, so findings do not describe all World Cup matches.

## Methods

1. Retrieve match records from the source repository.
2. Normalize match and round fields across source formats.
3. Identify the first-scoring team from goal-event minutes.
4. Determine the match outcome, distinguishing regulation time, extra time, and penalties where the source supports that distinction.
5. Analyze outcomes overall and compare outcomes by red-card status.
6. Visualize counts and proportions, while reporting sample sizes and limitations.

## Tools

- Python
- pandas
- matplotlib
- seaborn
- Jupyter Notebook

## Repository Structure

```text
worldcup-first-goal-analysis/
├── README.md
├── DATA_QUALITY_REVIEW.md
├── requirements.txt
├── Football_Game.ipynb
├── data/                 # Optional; add only appropriately licensed/reusable data
└── images/               # Exported charts for the README, after validation
```

## How to Run

1. Install the dependencies listed in `requirements.txt`.
2. Open `Football_Game.ipynb` in Jupyter Notebook or Google Colab.
3. Run all cells from top to bottom.
4. Confirm that the data-quality checks pass and that the generated counts match the summary tables before relying on the results.

The notebook retrieves data from GitHub, so running the data-collection step requires an internet connection. The notebook should be refactored so that data collection and cleaning happen once and later cells reuse the same validated DataFrame.

## Key Findings

**Pending validation.** The original notebook reports that teams scoring first win more often than they lose, and that the red-card subgroup has a lower observed win rate. These results are not presented as final findings here because the original cells use inconsistent DataFrame schemas and the red-card counting logic may double-count events. Recompute all figures after fixing the issues documented in `DATA_QUALITY_REVIEW.md`.

## Limitations

- The dataset covers only the tournament editions and records available in the selected source repository.
- Matches with no recorded goals are excluded from first-goal analysis.
- Goal-event minutes can include added time; parsing only the first integer can lose ordering information (for example, `45+2`).
- Red-card information must be deduplicated and card-type definitions verified.
- The original analysis counts red cards across the match rather than restricting them to events occurring after the first goal.
- The number of matches in the red-card subgroup may be small; subgroup percentages can therefore be unstable.
- Observational associations do not establish causation. Team strength, match state, timing of cards, and other factors may influence outcomes.
- Penalty shoot-outs are not equivalent to goals scored during open play and should be reported separately from regulation/extra-time outcomes.

## Next Improvements

- Refactor extraction and transformation into one reusable pipeline.
- Add automated data-quality checks and a data dictionary.
- Verify score semantics and event schemas against the source files.
- Recalculate every summary from the same cleaned dataset.
- Add a concise findings section only after validation.
- Export clean, accessible charts with labels and sample sizes.
