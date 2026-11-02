# econ3916-lab11-sql

Objective: I translated pandas operations into SQL with DuckDB, and checked where a join, a window function, or a missing value changes the answer.

Methodology:
- I translated select, filter, sort, groupby, and merge operations from pandas into SQL, and checked that both give the same rows.
- I joined customers to orders, counted rows before and after the join, and ran the same Welch t-test on the unjoined and joined tables.
- I used window functions, AVG OVER PARTITION BY and RANK, to compute group averages and ranks without collapsing rows.
- I tested SQL three-valued logic on a small survey table with missing incomes, and compared the SQL filter with the same filter in pandas.
- I queried a public Parquet file of about 3 million NYC taxi trips over HTTP without downloading it, reading only the columns the query needed.
- I asked an AI to translate three of my pandas steps into SQL, revised my prompt once, and compared the row counts of its queries with my pandas results.
- I rewrote a joined query that gave wrong averages, by aggregating customers before joining.

Key Findings:
- I showed the cardinality trap: a one-to-many LEFT JOIN turned 600 customer rows into 3,612 rows. The same Welch t-test on the same revenue figures went from t = 0.07 (p = 0.9413) to t = 0.71 (p = 0.4749), because the repeated rows shrank the standard error from 9.35 to 3.75 and moved the difference from 0.69 to 2.68.
- In Part 1, East had 282 customers with a mean revenue of 490.91, and West had 318 customers with a mean revenue of 490.22. After filtering to revenue over 400, only West survived a count over 230, with 249 customers and a mean revenue of 530.73, while East had 221.
- I used window functions to keep all 600 rows while putting each region average and rank on every row. East's top customer was customer 120, with revenue of 804.43, which is 313.52 above East's average of 490.91.
- I showed SQL three-valued logic: WHERE income != 50000 kept 3 of 5 survey rows, because a missing income compared with 50000 is unknown and WHERE keeps only true rows, while the same filter in pandas kept 5.
- I queried NYC taxi trips over HTTP, and one-passenger trips dominated with 2,159,728 trips, averaging 3.18 miles and a $26.17 fare, in 2.9 seconds on my Colab run.
- I checked the AI translation by row count. After revision, Step 3 matched 2 to 2, Step 4 matched 3,612 to 3,612, and Step 7 matched 5 to 5. The first version gave 3 for Step 7, because it dropped missing incomes.
- In the Challenge, the joined query gave wrong averages of 493.91 for East and 491.24 for West, against correct averages of 490.91 and 490.22, while its order totals of 136,076.60 for East and 155,101.46 for West were right. My rewritten query gives the correct averages and the same totals.
