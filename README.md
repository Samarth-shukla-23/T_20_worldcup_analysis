# ICC Men's T20 World Cup 2022 — Player Performance Analysis

Exploratory data analysis of the 2022 T20 World Cup (Australia) using Python and pandas.
Starting from raw JSON scorecards, the project builds clean match, batting and bowling datasets
and uses them to identify the tournament's **power hitters**, **anchor batsmen**,
**economical bowlers** and **leading wicket-takers**.

## Questions this project answers

1. Which batsmen scored quickly *and* consistently hit boundaries (power hitters)?
2. Which batsmen built innings steadily, scoring heavily while facing many balls (anchors)?
3. Which bowlers kept the run rate down (economical bowlers)?
4. Which bowlers took the most wickets?

## Dataset

Three JSON files, converted to CSV in the notebook:

| File | Contents | Key columns |
|------|----------|-------------|
| `t20_wc_match_results` | One row per match (45 matches) | `team1`, `team2`, `winner`, `margin`, `ground`, `matchDate`, `match_id` |
| `t20_wc_batting_summary` | One row per batsman innings | `match`, `teamInnings`, `battingPos`, `batsmanName`, `runs`, `balls`, `4s`, `6s`, `SR`, `out/not out`, `match_id` |
| `t20_wc_bowling_summary` | One row per bowling spell (500 rows) | `match`, `bowlingTeam`, `bowlerName`, `overs`, `maiden`, `runs`, `wickets`, `economy`, `0s`, `4s`, `6s`, `wides`, `noBalls`, `match_id` |

**Source:** *(add where the JSON files came from, e.g. ESPNcricinfo / Kaggle link, and check its licence or terms of use)*

## Data preparation

1. Load each JSON file and flatten it into a pandas DataFrame
2. Rename the scorecard field to `match_id` (e.g. `T20I # 1823`)
3. Build a lookup of `"Team A Vs Team B"` → `match_id` (both team orders) and map it onto the batting and bowling tables so all three datasets can be joined
4. Derive an `out/not out` column from the dismissal text, then drop the raw dismissal column
5. Convert `runs`, `balls`, `4s`, `6s`, `SR`, `wickets`, `economy` and similar columns from text to numeric
6. Export the clean tables to CSV

## Methodology

| Category | Criteria |
|----------|----------|
| **Power hitters** | Innings with strike rate > 160, more than 20 runs, and a boundary at least every 4 balls (`balls / (4s + 6s) <= 4`). Only players with **2 or more** such innings are kept. |
| **Anchor batsmen** | Excluding the power-hitters' innings: total runs > 200, balls faced > 150, and average strike rate > 110. |
| **Economical bowlers** | Average economy rate ≤ 5.0 across the tournament. |
| **Top wicket-takers** | More than 11 wickets in the tournament. |

These thresholds are judgement calls, not official cricket definitions.

## Key findings

**Power hitters** (qualifying innings only)

| Batsman | Qualifying innings | Runs in those innings | Avg. strike rate |
|---------|-------------------|-----------------------|------------------|
| Suryakumar Yadav | 3 | 142 | 211.8 |
| Quinton de Kock | 2 | 110 | 213.4 |
| Glenn Maxwell | 2 | 77 | 180.2 |
| Finn Allen | 2 | 74 | 220.1 |

**Anchor batsmen**

| Batsman | Runs | Balls faced | Avg. strike rate |
|---------|------|-------------|------------------|
| Virat Kohli | 296 | 217 | 129.9 |
| Max O'Dowd | 242 | 215 | 110.9 |
| Jos Buttler | 225 | 156 | 115.5 |
| Kusal Mendis | 223 | 156 | 126.7 |
| Lorcan Tucker | 204 | 163 | 114.8 |

**Economical bowlers** (average economy ≤ 5.0): Nasum Ahmed (3.50), Moeen Ali (4.50),
Aayan Afzal Khan (4.61), Jason Holder (4.67), Zahoor Khan (4.75), Colin Ackermann (5.00).

**Leading wicket-takers:** Wanindu Hasaranga (15), Bas de Leede (13), Sam Curran (13),
Blessing Muzarabani (12).

## Visualizations

*(Save your plots with `plt.savefig("images/<name>.png", dpi=150, bbox_inches="tight")` and embed them here.)*

![Power hitters vs. other batsmen](images/powerhitters_comparison.png)
![Anchor batsmen vs. other batsmen](images/anchor_comparison.png)
![Economical bowlers](images/economical_bowlers.png)

## Tech stack

Python · pandas · NumPy · Matplotlib · Jupyter Notebook

## Repository structure

```
t20-world-cup-2022-analysis/
├── README.md
├── requirements.txt
├── data/
│   ├── json/          # raw scorecards
│   └── csv/           # cleaned tables produced by the notebook
├── notebooks/
│   └── T_20_analysis.ipynb
└── images/            # saved plots used in this README
```

## How to run

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
pip install -r requirements.txt
jupyter notebook notebooks/T_20_analysis.ipynb
```

Place the three JSON files in `data/json/` before running.

## Limitations

- Strike-rate and economy thresholds are subjective; different cut-offs would give different lists.
- Averages of per-match economy and strike rate are unweighted, so players with very few balls or overs can rank unusually well or badly.
- Power-hitter run totals count only their qualifying innings, not their full tournament totals.
- Player names are used exactly as they appear in the scorecards (including captain `(c)` and wicketkeeper `†` markers).

## Future work

- Clean player names and aggregate stats per player across all innings
- Use weighted metrics (total runs ÷ total balls or overs) with a minimum-balls / minimum-overs filter
- Add team-level analysis: powerplay vs. death-over scoring, win margins, results by venue
- Build an interactive dashboard (Streamlit or Power BI)
