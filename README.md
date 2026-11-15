# A/B Test Analysis: Statistical Significance of Conversion Metrics
 
Analysis of four A/B tests with Python (pandas, statsmodels) and a Tableau dashboard that shows where the conversion metrics changed significantly and where they did not.

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1xD8AjPM2NiklnjzQbibfsSQwtxq86fY4?usp=sharing)  
**Dashboard:** [Tableau Public](https://public.tableau.com/views/ABTestResults_17946029366250/ABtest?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

## Goal
 
For each of four independent A/B tests, compare the conversion rates of group A (control) and group B (variant) and check whether the difference is statistically significant.
 
Metrics (conversion = number of events / number of sessions):
 
| Metric | Formula |
|---|---|
| Add payment info | `add_payment_info / session` |
| Add shipping info | `add_shipping_info / session` |
| Begin checkout | `begin_checkout / session` |
| New accounts | `new_accounts / session` |

## Data

- Source: BigQuery dataset `DA` (A/B test groups, sessions, session parameters, events, orders, accounts), exported as an aggregated table
- Period: 1 November 2020 - 27 January 2021 (88 days)
- Size: 800,996 rows and 9 columns; 4 tests, about 271,000 sessions per group (summed over the tests)
- Structure: one row = count of one event (`value`) for a date, country, device, continent, channel, test and test group
- Groups: `test_group` 1 = A (control), 2 = B (variant)
- Note: for `session` and `new account`, `value` is the number of unique sessions; for other events it is the number of events (not unique sessions), so one session can add more than one event
- Note: the raw data file is not included in this repository

 ## Method
 
- Statistical test: two-proportion z-test (`statsmodels.stats.proportion.proportions_ztest`).
- Significance level: alpha = 0.05.
- H0: the conversion rates in groups A and B are equal. If p-value < 0.05, H0 is rejected and the result is marked as significant.
- `metric_change = (conversion B - conversion A) / conversion A x 100%`.
- Metrics are stored in a list and processed in a loop, so a new metric can be added with one line.
- Results are calculated for each test overall and also split by country, device, continent and channel.

## Key results (overall, per test)
 
6 of 16 test-metric combinations are significant at alpha = 0.05:
 
- **Test 1:** `add_payment_info` (+12.5%), `add_shipping_info` (+6.6%) and `begin_checkout` (+6.7%) are significantly higher in group B. `new_accounts` is not significant.
- **Test 2:** no significant differences.
- **Test 3:** only `begin_checkout` is significant (-3.4% in group B).
- **Test 4:** `begin_checkout` (-2.4%) and `new_accounts` (-3.4%) are significant, both lower in group B.

Limitations:

- In the breakdowns, 369 of 1,793 comparisons (20.6%) are significant. Small segments (few sessions) give unreliable p-values, and the breakdowns overlap (the same sessions appear in several of them), so these shares are descriptive.
- 159 segment-metric combinations were skipped: 157 with no events in both groups and 2 where events were not lower than sessions.

## Tools
 
Python, pandas, NumPy, statsmodels, Google Colab, Tableau Public.
