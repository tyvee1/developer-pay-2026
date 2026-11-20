# What drives developer pay in 2026?

An analysis of the Stack Overflow Developer Survey 2026, written up for a general audience in the blog post
**[Where you live beats what you code](https://tyvee1.github.io/developer-pay-2026/)**.

**Contents:** [Motivation](#motivation) · [Installation and libraries](#installation-and-libraries) ·
[Files](#files) · [How to run](#how-to-run) · [Technical details](#technical-details-what-i-tried) ·
[Results](#results) · [Licensing, authors and acknowledgements](#licensing-authors-and-acknowledgements)

## Motivation

In 2026 developers keep hearing the same advice for earning more: adopt AI agents, go remote, pick the right
role. I work in cloud architecture and wanted to know which of those actually moves pay, and by how much,
once you compare like with like. The 2026 survey is the first to ask about AI agents and work setting in
detail, so it can answer that. This project asks five business questions:

1. **What drives developer pay in 2026?**
2. **Do developers who use AI agents earn more, and how much of that survives once experience, role and country are taken into account?**
3. **Does remote work pay more in the United States?**
4. **How accurately can a model predict a developer's pay?**
5. **What would three specific developers be predicted to earn?**

## Installation and libraries

Python 3.11. Every library below ships with the Anaconda distribution; `requirements.txt` pins the exact
versions the notebook was run with, so a plain `pip` install reproduces it outside Anaconda too.

| Library | Used for |
|---|---|
| pandas | loading, assessing and cleaning the survey |
| NumPy | log transform, bootstrap resampling |
| scikit-learn | preprocessing pipeline, models, cross-validation, permutation importance, metrics |
| matplotlib, seaborn | every figure |
| notebook | running the analysis notebook |

## Files

The work most readers want is the notebook; the blog post is the plain-English version.

| Path | Description |
|---|---|
| `developer_pay_2026.ipynb` | The full analysis, following CRISP-DM: business understanding, data understanding (gather, assess), data preparation (clean), modeling, evaluation, and the results for each question |
| `data/so_survey_2026_extract.csv.gz` | Every survey respondent, with the 14 columns this analysis uses (the full 122 MB file is over GitHub's 100 MB limit) |
| `data/README.md` | Where the data comes from, its licence, the column list, and how to rebuild the extract from the full file |
| `results/summary.json` | Every figure the notebook produced; the numbers in this README and the blog post are taken from it |
| `docs/index.md`, `docs/_config.yml` | The blog post, published with GitHub Pages |
| `docs/assets/*.png` | The figures, saved by the notebook and used in the blog post |
| `requirements.txt` | Library versions |
| `LICENSE` | MIT licence for the code |

## How to run

With Python 3.11:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook developer_pay_2026.ipynb
```

Run all cells, or run the whole notebook without opening it:

```bash
jupyter nbconvert --to notebook --execute --inplace developer_pay_2026.ipynb
```

The notebook loads the shipped extract. To rebuild the extract from the full survey, download
the 2026 results from [survey.stackoverflow.co](https://survey.stackoverflow.co/), save
`survey_results_public.csv` to `data/raw/`, and run the notebook: it checks the file's SHA-256 before using it.

## Technical details: what I tried

**Data issues.** 11,712 developers reported a salary, but pay was entered by hand and converted
to US dollars, and values ranged from $1 to more than $30 million a year. Keeping $5,000 to $1,000,000 a year
removed 758 answers that were almost certainly typos, monthly figures or currency
mistakes. Later survey questions were often skipped; dropping incomplete rows would have kept only people
who finished the survey, so a skipped answer is kept as its own "Missing" category, and missing experience
is filled with the median plus a flag saying it was missing.

**Methods.** Pay is heavily skewed, so every model predicts the logarithm of pay and converts back to
dollars for reporting. Three models were compared with 5-fold cross-validation on the training rows only:
predicting the average (R² 0.00), ridge regression (0.48) and gradient boosting
(0.53). Boosting won because it can learn interactions, such as experience paying more in some
countries than others. Feature importance is measured by shuffling one column at a time on held-out rows.

**What worked and what didn't.** Comparing raw medians overstated both headline effects: the AI-agent gap
fell from 29% to 14% and the US remote gap from 42% to 4%
once the model held everything else fixed. The model is weakest when it stacks several high-paying traits
in one profile: its estimate for a senior US architect is above what similar respondents actually report.

## Results

Of 30,903 respondents, 10,954 are employed or self-employed developers reporting pay between
$5,000 and $1,000,000 a year.

1. **Country drives pay more than anything else.** Shuffling country cuts the model's R² by 0.60,
   about 8 times more than the next feature, professional experience. Median pay is $151,000 in the
   United States, $138,040 in Switzerland and $22,981 in India. Experience pays mostly in the first decade:
   for a typical US full-stack developer the model predicts $73k at zero years, $165k at ten and $167k at twenty.
2. **AI-agent users earn more, but half the gap is who they are.** Median pay is $103,726 for AI-agent users
   (n = 2,872) against $80,632 for non-users (n = 6,545), a 29% gap. Holding experience,
   role, country and the other features fixed, the model's gap is 14%. This is an association, not evidence
   that adopting agents raises pay.
3. **Remote work's premium in the US is mostly composition.** Median pay is $170,000 remote (n = 1,322) and
   $120,000 in-person (n = 406): a $50,000 gap, 95% bootstrap interval $38,500 to $59,500. With every
   other feature held fixed the model's remote premium is 4%.
4. **The model explains about half the variation in pay.** Gradient boosting reached a cross-validated R² of
   0.53 (ridge regression 0.48) and 0.51 on 2,191 held-out developers, on log pay. Its median error is $21,112
   (26% of salary) against $33,385 (40%) for predicting the average; the mean error is $35,157.
   Accuracy and recall apply to classification, not to a continuous salary, so the same features were also used to
   classify developers as above or below the training median of $85,296: accuracy 78% (majority-class
   baseline 47%), precision 81%, recall 76%, F1 79%.
5. **Scenarios.** A remote US software architect with 12 years' experience who uses AI agents is predicted at
   $241,930 (10th–90th percentile $138,578 to $405,861). The model runs above the $185,000 median of the 39 most similar
   respondents, so treat the point estimate as optimistic. The same profile in Germany is predicted at $118,104
   (similar respondents $102,355); a US full-stack developer with two years' experience working in-person, $71,869
   (similar respondents $90,000).

Limitations: the survey is self-selected, pay is converted to US dollars without adjusting for living costs,
and every gap above is an association rather than a cause. Age and gender were deliberately left out of the
model.

## Licensing, authors and acknowledgements

- **Code:** MIT licence; see `LICENSE`.
- **Data:** [Stack Overflow Developer Survey 2026](https://survey.stackoverflow.co/), made available under the
  [Open Database License (ODbL) 1.0](https://opendatacommons.org/licenses/odbl/1-0/), with individual contents under
  the [Database Contents License (DbCL) 1.0](https://opendatacommons.org/licenses/dbcl/1-0/). The extract in
  `data/` is a derivative database and is shared under the same licence; see `data/README.md`.
- **Author:** Ty Verdell.
- Thanks to every developer who took the survey. Built for the Udacity Data Scientist Nanodegree.
- **Disclaimer:** For learning only: these are estimates from survey answers, not career or financial advice. Not affiliated with or endorsed by Stack Overflow.
