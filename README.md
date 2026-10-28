# Cookie Cats A/B Test: Where Should the First Gate Go?

An end-to-end analysis of a mobile game A/B test, ending in a ship / don't-ship recommendation.

## The business question

Cookie Cats is a puzzle game that places "gates" which make players wait (or pay) before continuing. Players were randomly assigned to one of two versions:

- **gate_30:** first gate at level 30 (control)
- **gate_40:** first gate at level 40 (treatment)

**Does moving the first gate from level 30 to level 40 change player retention?**

## Result

**Recommendation: keep the first gate at level 30. Do not ship gate_40.**

| Metric | gate_30 | gate_40 | Difference (gate_40 − gate_30) | 95% CI | p-value |
|---|---|---|---|---|---|
| 1-day retention | 44.8% | 44.2% | −0.6 pp | −1.24 to +0.06 pp | 0.074 |
| 7-day retention (primary) | 19.0% | 18.2% | −0.8 pp | −1.33 to −0.31 pp | 0.0016 |
| Rounds played (mean, outlier excluded) | 51.3 | 51.3 | −0.04 | n/a | 0.051 (Mann-Whitney) |

- Moving the gate to level 40 reduced 7-day retention by about 0.8 percentage points, a relative drop of roughly 4%. A bootstrap check (10,000 resamples) agrees: −1.32 to −0.33 pp.
- Total play did not change meaningfully, so there is no engagement gain to offset the loss.
- Business impact: about 820 fewer players still active at day 7 per 100,000 installs.

## Approach

1. Framed the decision first: primary metric (7-day retention), secondary (1-day retention) and a guardrail (rounds played).
2. Validity checks: duplicate users, and sample ratio mismatch (chi-square test).
3. Explored outcomes, and found and handled an extreme outlier (49,854 rounds).
4. Two-proportion z-tests with confidence intervals for both retention metrics.
5. Bootstrap cross-check of the 7-day result.
6. Guardrail analysis using Mann-Whitney U and a bootstrap of median differences, because rounds are heavily skewed.
7. Power analysis to show which effect sizes the test could and could not detect.
8. Sensitivity check excluding users who never played a round.

## Limitations

- Minor group-size imbalance (44,700 vs 45,489; chi-square p = 0.009). It did not meet the usual alarm threshold (p < 0.001) but is worth flagging.
- One extreme outlier (likely a bot or logging error) was excluded from the rounds comparison only.
- No revenue data, so this analysis cannot say whether gate_40 earns more from in-app purchases.
- Only 7 days of follow-up, so long-term retention is unknown.
- The test could reliably detect drops of about 0.8 pp or more, but would likely miss effects under about 0.5 pp.

## Next steps

- Obtain revenue data and check whether gate_40 increases purchases.
- Test a gate earlier than level 30.
- Run a 30-day retention follow-up.

## Repository contents
- [Cookie_Cats_AB_Test.ipynb](Cookie_Cats_AB_Test.ipynb): the full analysis, with explanations and charts.
- [Cookie_Cats_AB_Test_Recommendation.docx](Cookie_Cats_AB_Test_Recommendation.docx): the one-page recommendation.
- [requirements.txt](requirements.txt): Python dependencies.
- 
## How to run

1. Download the dataset (search "Mobile Games A/B Testing - Cookie Cats" on Kaggle) and save it as `cookie_cats.csv` in the same folder as the notebook.
2. Install dependencies: `pip install -r requirements.txt`
3. Open `Cookie_Cats_AB_Test.ipynb` in Jupyter and run all cells.

## Tools

Python, pandas, NumPy, SciPy, statsmodels, matplotlib.
