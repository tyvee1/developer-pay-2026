# What drives developer pay in 2026?

An analysis of the Stack Overflow Developer Survey 2026, written up for a general audience in the blog post
**[Where you live beats what you code](https://tyvee1.github.io/developer-pay-2026/)**.

## Motivation

Developers, hiring managers and people moving into software all want to know what a developer is paid and
what changes that number. The 2026 survey asks new questions about AI agents and work setting, so this
project asks five business questions:

1. **What drives developer pay in 2026?**
2. **Do developers who use AI agents earn more, and how much of that survives once experience, role and country are taken into account?**
3. **Does remote work pay more in the United States?**
4. **How accurately can a model predict a developer's pay?**
5. **What would three specific developers be predicted to earn?**

## Libraries

Python 3.11 with the versions in `requirements.txt`:

| Library | Used for |
|---|---|
| pandas | loading, assessing and cleaning the survey |
| NumPy | log transform, bootstrap resampling |
| scikit-learn | preprocessing pipeline, models, cross-validation, permutation importance, metrics |
| matplotlib, seaborn | every figure |
| notebook | running the analysis notebook |

## Files

| Path | Description |
|---|---|
| `developer_pay_2026.ipynb` | The full analysis, following CRISP-DM: business understanding, data understanding (gather, assess), data preparation (clean), modeling, evaluation, and the results for each question |
| `data/so_survey_2026_extract.csv.gz` | Every survey respondent, with the 14 columns this analysis uses (the full 122 MB file is over GitHub's 100 MB limit) |
| `data/README.md` | Where the data comes from, its licence, the column list, and how to rebuild the extract from the full file |
| `results/summary.json` | Every figure the notebook produced; the numbers in this README and the blog post are taken from it |
| `docs/index.md`, `docs/_config.yml` | The blog post, published with GitHub Pages |
| `docs/assets/*.png` | The figures, saved by the notebook and used in the blog post |
| `requirements.txt` | Library versions |

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

## Acknowledgements

- Data: [Stack Overflow Developer Survey 2026](https://survey.stackoverflow.co/), made available under the
  [Open Database License (ODbL) 1.0](https://opendatacommons.org/licenses/odbl/1-0/), with individual contents under
  the [Database Contents License (DbCL) 1.0](https://opendatacommons.org/licenses/dbcl/1-0/). The extract in
  `data/` is a derivative database and is shared under the same licence; see `data/README.md`.
- Thanks to every developer who took the survey.
- Built for the Udacity Data Scientist Nanodegree.

Author: Ty Verdell
