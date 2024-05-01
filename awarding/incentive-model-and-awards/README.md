# Incentive model and awards

To incentivize **wardens**, C4 uses a unique scoring system with two primary goals: reward participants for finding unique bugs and also to make the audit resistant to Sybil attack. A secondary goal of the scoring system is to encourage participants to form teams and collaborate.

**Judges** are incentivized to review findings and decide their severity, validity, and quality by receiving a share of the prize pool themselves.

**Note:**
* `pie` is the number of shares assigned to that report or finding.
* `split` is the number of times those shares were divided, the findings count for a given group.
* `slice` is the number of shares assigned for that warden’s finding.

## High and Medium Risk bugs

Wardens are given shares for bugs discovered based on severity, and those shares give the owner a pro rata piece of the pie:

`Med Risk Slice: 3 * (0.9 ^ (split - 1)) / split`\
`High Risk Slice: 10 * (0.9 ^ (split - 1)) / split`

Please note that findings with partial credit still count as 1 finding in the algorithm. \
During awarding, each award is redeemed for: `award pool / pie / slice`.

### Bonus for best / selected for report

For each unique High or Medium finding, the submission selected for inclusion in the audit report receives a 30% slice bonus. The `pie` (total of slices) is also increased accordingly.

Let's look at an example of a set of High risk duplicates, with 3 satisfactory findings.

As per the formula, the pie would be: \
`10 * (0.9 ^ (findingCount - 1)) = 8.10`

Warden A's finding is selected for report; therefore the pie is adjusted as follows: \
`new pie = previous pie + [selected finding's slice] * 0.3` \
`=> 8.1 + ( 2.7 * 0.3 ) = 8.91`

The resulting awards are:

| **Warden**  | **finding** | **risk** |        **pie**     | **split** |      **slice**      |       **award**        |
| ----------- | ----------- | ---------| ------------------ | --------- | ------------------- | ---------------------- |
| 'Warden A'  | 'H-02'      | '3'      |         8.91       |   3       |         3.51        |  1300                  |
| 'Warden B'  | 'H-02'      | '3'      |         8.91       |   3       |         2.70        |  1000                  |
| 'Warden C'  | 'H-02'      | '3'      |         8.91       |   3       |         2.70        |  1000                  |

### Bonuses for top competitors
For audits starting on or after April 30, 2024, there are two bonuses for top-performing wardens:

1. **Hunter bonus:** 10% of the HM pool will be awarded to the warden or team who identities the greatest number of unique HMs.
2. **Gatherer bonus:** 10% of the HM pool will be awarded to the warden or team who identifies the greatest number of valid HMs.

Both bonuses weigh Highs more heavily than Mediums, similarly to Code4rena's standard awarding mechanism.

### Duplicates getting partial credit

All issues which identify the same functional vulnerability will be considered duplicates regardless of effective rationalization of severity or exploit path.

However, any submissions which do not identify or effectively rationalize the top identified severity case may be judged as “partial credit” and may have their shares  divided at judge’s sole discretion (e.g. 25%, 50%, or 75% of the shares of a satisfactory submission in the duplicate set).

Let's first review an example of a duplicate group *without* partial findings.

| **Warden**  | **finding** | **risk** |        **pie**     | **split** |      **slice**      |       **award**        |
| ----------- | ----------- | ---------| ------------------ | --------- | ------------------- | ---------------------- |
| 'Warden A'  | 'H-02'      | '3'      |         8.91       |   3       |         3.51        |  1300                  |
| 'Warden B'  | 'H-02'      | '3'      |         8.91       |   3       |         2.70        |  1000                  |
| 'Warden C'  | 'H-02'      | '3'      |         8.91       |   3       |         2.70        |  1000                  |


Now, let's compare to a group of three high findings where two of the duplicate findings are identified as `partial-25`: the findings from wardens B and C.
Let's see how the pie and slices evolve.

| **Warden**  | **finding** | **risk** |        **pie**     | **split** |      **slice**      |       **award**        |
| ----------- | ----------- | ---------| ------------------ | --------- | ------------------- | ---------------------- |
| 'Warden A'  | 'H-01'      | '3'      |         4.86       |   3       |         3.51        |  1300                  |
| 'Warden B'  | 'H-01'      | '3'      |         4.86       |   3       |        0.675        |  250                   |
| 'Warden C'  | 'H-01'      | '3'      |         4.86       |   3       |        0.675        |  250                   |

The pie allocated to that findings group is adjusted accordingly so the award of the full-credit findings remains constant; only the partial findings' award is adjusted.

Let's compare another two sets of duplicates.

1. Group A:
  - 3 full-credit findings
  - Warden A's finding is selected for report
2. Group B:
  - Warden A's finding is selected for report
  - Warden B's finding is a full-credit duplicate
  - Warden C's finding is a partial-credit (`partial-25`) duplicate

**Group A**
| **Warden**  | **finding** | **risk** |        **pie**     | **split** |      **slice**      |       **award**        |
| ----------- | ----------- | ---------| ------------------ | --------- | ------------------- | ---------------------- |
| 'Warden A'  | 'M-02'      | '2'      |         2.673      |   3       |        1.0530       |  1300                  |
-> selected
| 'Warden B'  | 'M-02'      | '2'      |         2.673      |   3       |        0.81         |  1000                  |
-> full credit
| 'Warden C'  | 'M-02'      | '2'      |         2.673      |   3       |        0.81         |  1000                  |
-> full credit


**Group B**
| **Warden**  | **finding** | **risk** |        **pie**     | **split** |      **slice**      |       **award**        |
| ----------- | ----------- | ---------| ------------------ | --------- | ------------------- | ---------------------- |
| 'Warden A'  | 'M-01'      | '2'      |         2.0655     |   3       |        1.0530       |  1300                  |
-> selected
| 'Warden B'  | 'M-01'      | '2'      |         2.0655     |   3       |        0.81         |  1000                  |
-> full credit
| 'Warden C'  | 'M-01'      | '2'      |         2.0655     |   3       |        0.2025       |  250                   |
-> partial credit

We can see here that the logic behind the `partial-` labels only impacts the awards for partial findings; even though the pies vary, the awards stay the same.

**Conclusion:**

Only the award amounts for "partial" findings have been reduced, in line with expectations. The aim of this adjustment is to recalibrate the rewards allocated for these specific findings. Meanwhile, the awards for full-credit findings remain unchanged.

### Validator-improved submissions

[Validators](https://docs.code4rena.com/roles/certified-contributors/validators.md) may enhance submissions (add PoC, increase quality of report, etc.) in exchange for a % of the finding’s payout. 

For Validator-improved submissions:  if the judge believes the validator added a measurable enhancement, they get a split of the value of the issue:
- 25% cut → small enhancement
- 50% cut → med enhancement
- 75% cut → large enhancement

## QA and Gas Optimization Reports

In order to incentivize wardens to focus efforts on high and medium severity findings while also ensuring quality coverage, the pool’s allocation is capped for low severity, governance, and gas optimization findings.

Low and governance findings are submitted as a **single** QA report. Similarly, gas optimizations are submitted as a single gas report. For more on reports, see [Judging criteria](/awarding/judging-criteria/README.md).

QA and gas optimization reports are awarded on a curve based on the judge’s score.

- QA reports compete for a share of 4% of the prize pool (e.g. $2,000 for a $50,000 audit);
- The gas optimization pool varies from audit to audit;
- QA and Gas optimization reports are awarded on a curve.

There is a very high burden of quality and value provided for QA and gas optimization reports. Only submissions that demonstrate full effort worthy of consideration for inclusion in the report will be eligible for rewards.

**Note:** Audits pre-dating February 3, 2022 awarded low risk and gas optimization shares as: `Low Risk Shares: 1 * (0.9 ^ (findingCount - 1)) / findingCount`

### Ranks for QA and Gas reports

_These guidelines apply to all audits starting on or after April 30, 2024._ 

After post-judging QA is complete, the Judge and Validators vote to select the top 3 QA reports and Gas reports. (In the case of a tie vote, there may be a 4th place report.)

The 1st, 2nd, and 3rd place winners are awarded using a curve model that will be documented here ASAP. 

Satisfactory reports not among the winning reports will not be awarded -- but will count towards wardens' accuracy scores.

In the unlikely event that zero high- or medium-risk vulnerabilities are found, the HM award pool will be divided based on the QA Report curve, unless otherwise stated in the audit repo. 

## Satisfactory / unsatisfactory submissions

Any submissions deemed unsatisfactory are ineligible for awards, and count against wardens' accuracy scores.

The bar for satisfactory submissions is that they are roughly at a level that could be found in a draft report by a professional auditor: specifically on the merits of technical substance, with writing quality considered only where it interferes with comprehension of the technical message.

It is possible for a submission to be *technically* valid and still unsatisfactory. An “unsatisfactory” submission may meet any of these criteria:

- incorrect
- low/incomplete effort
- out of scope
- clearly overinflated severity
- proof of concept does not pass the burden of proof test
- approach is disrespectful of sponsors’ and judges’ time in some way

Any submissions that appear to be direct copies of other reports in the current audit will be collectively deemed unsatisfactory.

## Other submission types

As of April 30, 2024, the following submission types are paused:

### Bot reports

The first hour of each Code4rena audit is devoted to a bot race, to incentivize high quality automated findings as the first wave of the audit.

- The winning bot report is selected and shared with all wardens within 24 hours of the audit start time.
- The full set of issues identified by the best automated tools are considered out of scope for the audit and ineligible for awards.

Doing this eliminates the enormous overlapping effort of all wardens needing to document common low-hanging issues And because the best bot report is shared with auditors at the start of the audit, these findings serve as a thorough starting place for understanding the codebase and where weaknesses may exist.

**Ultimately, the bot race ensures human auditors are focused on things humans can do.**

By designating a portion of the pool in this direction, Code4rena creates a separate lane for the significant investment of effort that many auditors already make in automated tooling -- and rather than awarding 100 people for identifying the same issue, we award the best automated tools.

### Analyses

Analyses share high-level advice and insights from wardens' review of the code.

Where individual findings are the "trees" in an audit, the Analysis is a "forest"-level view.

Analyses compete for a portion of each audit's award pool, and are graded and awarded similarly to QA and Gas Optimization reports.

### Understanding historical grading for QA, Gas, and Analysis reports

For audits that started before April 30, 2024: 

- Analyses, QA reports and Gas reports in this time period were graded A, B, or C.
- C scores are unsatisfactory and ineligible for awards.
- All A-grade reports receive a score of 2; All B-grade reports get a 1. Awarding for QA and Gas reports is on a curve that's described [here](https://docs.code4rena.com/awarding/incentive-model-and-awards/curve-logic).
- Judges chose the best report in each category (Analysis, QA report, and Gas report), each of which earns the same 30% share bonus described under "High and Medium Risk bugs."

**Note:** if the `selected for report` submission has a B-grade label, it will still be treated as A-grade and given proportionally more than B-grade, plus the 30% bonus for being `selected for report`.
