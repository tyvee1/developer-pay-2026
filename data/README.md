# Data

## Source

[Stack Overflow Developer Survey 2026](https://survey.stackoverflow.co/), file `survey_results_public.csv`
from the 2026 results (122,407,316 bytes).

SHA-256 of the full file:
`ad520b4d5d71f240141e63f0d6f81a3b1898cbdc69efe9ea985496533b15fc2c`

## Licence

The survey database is made available under the
[Open Database License (ODbL) 1.0](https://opendatacommons.org/licenses/odbl/1-0/). Its individual contents are
made available under the [Database Contents License (DbCL) 1.0](https://opendatacommons.org/licenses/dbcl/1-0/).

`so_survey_2026_extract.csv.gz` is a derivative database of the Stack Overflow Developer Survey 2026. It is
made available under the Open Database License 1.0, and its contents under the Database Contents License 1.0.

## What the extract contains

All 30,903 respondents, unfiltered, and these 14 columns of the original file, unchanged:

`ResponseId`, `Employment`, `CodeMain`, `Country`, `ConvertedCompYearly`, `Role`, `OrgSize`, `Industry`,
`WorkSetting`, `Education`, `YearsCode`, `WorkExp`, `AISelect`, `AIDaily`

The question behind each column is given in the survey's published schema file.

## Rebuilding the extract

Save the full file as `data/raw/survey_results_public.csv` and run the notebook. It checks the SHA-256 above
before reading the file, then rewrites the extract from it. `data/raw/` is excluded from git because the
full file is larger than GitHub allows.
