# ReliableVAEP

ReliableVAEP is a reliability layer for VAEP-based soccer player rankings. VAEP provides the player-value estimate, while ReliableVAEP measures how strongly the available match data supports the resulting ranking.

The project adds four main ideas to a standard VAEP leaderboard:

- bootstrap rank uncertainty;
- pairwise support between players;
- a Pairwise Consensus Score (PCS);
- a Reliability-Adjusted Rank (RAR).

The goal is not to replace VAEP. The goal is to show when an exact VAEP ranking is stable enough to trust and when it should be treated with caution.

## Data

The analyses use publicly available **StatsBomb Open Data**.

The final experiments cover:

- Premier League 2015/16;
- Barcelona, La Liga 2015/16–2019/20;
- Barcelona, La Liga 2008/09–2012/13;
- Bayer Leverkusen, Bundesliga 2023/24;
- FA Women's Super League 2018/19–2020/21;
- Real Madrid players from La Liga 2015/16 for the 2016 Ballon d'Or comparison.

The notebooks download the required StatsBomb data directly, so the raw event files are not included in this repository.

## Method

Players are ranked using VAEP per 90 minutes. ReliableVAEP then resamples each player's matches with replacement and rebuilds the leaderboard many times.

The final analyses use **3,500 bootstrap draws**. This value was chosen from the precision required for the 0.90 pairwise-support threshold. A minimum of 3,461 draws was required for a 95% Monte Carlo margin of about ±0.01, so the operating value was set to 3,500.

The bootstrap samples are used to calculate:

- a 95% rank interval for each player;
- the probability that one player ranks above another;
- PCS, estimated from the pairwise comparisons;
- RAR, obtained by sorting players by PCS.

RAR is evaluated using independent, non-overlapping match samples. Reproducibility is measured with Spearman rank correlation. A ranking is treated as stable when the median correlation is at least 0.80 for three consecutive tested sample sizes.

## Main findings

The two Barcelona periods reached sustained ranking stability at different sample sizes: 44 and 27 matches per player. This shows that there is no single minimum match count that can be applied universally.

In the Premier League 2015/16 stress test, 423 players were included. The median 95% rank interval covered about 201 positions, and 44.8% of player pairs had strong support. Across 600 independent comparisons, RAR was more reproducible than raw VAEP in 77.2% of cases, with a pooled median Spearman improvement of 0.0119.

Bayer Leverkusen 2023/24 and the three-season FA Women's Super League analysis remained unstable under the declared stability rule. RAR showed clearer gains in these less stable settings than in the stable Barcelona analyses.

The 2016 Ballon d'Or comparison was used only as a secondary external check. Agreement with the six Real Madrid players was modest, and RAR did not improve agreement over raw VAEP.

## Repository structure

```text
ReliableVAEP/
├── README.md
├── Notebooks/
│   ├── 01_premier_league_rar_stress_test.ipynb
│   ├── 02_barcelona_stability_and_rar.ipynb
│   ├── 03_bayer_leverkusen_rar.ipynb
│   ├── 04_wsl_longitudinal_rar.ipynb
│   ├── 05_real_madrid_ballondor_external_check.ipynb
│   └── 06_bootstrap_size_selection.ipynb
└── Results/
    ├── premier_league/
    ├── barcelona/
    ├── leverkusen/
    ├── wsl/
    ├── ballon_dor/
    └── bootstrap_selection/
```

## Notebooks

### 01 — Premier League 2015/16
Runs the main large-cohort stress test and compares raw VAEP with RAR using independent match samples.

### 02 — Barcelona
Tests ranking stability on two separate five-season Barcelona periods and examines how RAR behaves when rankings become stable.

### 03 — Bayer Leverkusen 2023/24
Tests ReliableVAEP on a single-team season that remains below the declared stability threshold.

### 04 — FA Women's Super League
Runs the longitudinal analysis over three WSL seasons and evaluates raw VAEP against RAR at several match counts.

### 05 — Real Madrid / 2016 Ballon d'Or
Compares the six Real Madrid players in the 2016 Ballon d'Or ranking with rankings derived from available 2015/16 La Liga event data.

### 06 — Bootstrap size selection
Documents the test used to justify the final choice of 3,500 bootstrap draws.

## Reproducing the analyses

The notebooks are designed to run independently in Kaggle.

1. Open a notebook in Kaggle.
2. Enable Internet access.
3. Run the notebook from top to bottom.
4. The notebook downloads the required StatsBomb Open Data.
5. Generated tables, figures and summaries are written to `/kaggle/working/`.

The notebooks use fixed random seeds and pinned package versions to support reproducibility.

## Results

The `Results/` folder contains the final tables, figures and summaries generated from the 3,500-draw analyses.

Only the final results used for the study are included. Older exploratory outputs and earlier bootstrap settings are not part of the repository.

## Data attribution

This project uses **StatsBomb Open Data**. StatsBomb remains the original source of the event and lineup data used in these analyses. Raw StatsBomb data are not redistributed here.
