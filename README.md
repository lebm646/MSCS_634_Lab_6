# MSCS 634 — Lab 6: Association Rule Mining

**Author:** Minh Le  
**Course:** MSCS 634 — Advanced Big Data and Data Mining

This lab applies Apriori and FP-Growth to grocery baskets, compares their frequent itemsets and runtime, and interprets association rules using support, confidence, lift, and Seaborn visualizations.

## Files

- `MSCS_634_Lab_6.ipynb` — fully executed notebook covering data cleaning, exploration, both algorithms, rule interpretation, timing comparisons, and limitations.
- `README.md` — purpose, results, decisions, and reproduction instructions.

## Data and method

The public [Groceries dataset](https://michael.hahsler.net/arules/reference/Groceries.html) contains **9,835 transactions and 169 item categories** from 30 days at a grocery outlet. The notebook reads a [commit-pinned CSV mirror](https://github.com/MartinThoma/algorithms/blob/427b0a8670f21a82e539594949985d44b8d326c2/arules/groceries.csv) directly into memory and verifies its SHA-256 checksum. No separate dataset or generated plot files are required.

Cleaning trims surrounding whitespace (452 fields), handles blanks and within-basket duplicates, and encodes category presence as Boolean values. All 9,835 baskets remain. The 2,824 repeated basket patterns are retained because identical purchases can be distinct transactions.

Both algorithms use **1% minimum support** (at least 99 baskets), with no itemset-length restriction. Rules use **50% minimum confidence**. Assertions verify identical itemsets, support values, rule identities, confidence, and lift across algorithms.

## Key results

- Both algorithms identify **333 frequent itemsets** and **15 association rules**.
- **Whole milk** is the most frequent category: 2,513 baskets (**25.55%**).
- **Citrus fruit + root vegetables → other vegetables:** support **1.04%** (102 baskets), confidence **58.62%**, lift **3.03**.
- **Root vegetables + tropical fruit → other vegetables:** support **1.23%** (121 baskets), confidence **58.45%**, lift **3.02**.
- **Rolls/buns + root vegetables → other vegetables:** support **1.22%** (120 baskets), confidence **50.21%**, lift **2.59**.

Other vegetables occur in 19.35% of all baskets, so the first rule's 58.62% confidence represents roughly three times that baseline. These patterns suggest combinations to investigate for layout or basket-based recommendations, but do not establish causation or incremental sales.

In the saved execution, median mining time across seven alternating-order runs was **0.0857 seconds for Apriori** and **0.0982 seconds for FP-Growth**; Apriori was about **1.15× faster**. Both were warmed up before measurement. The notebook reports the timing distribution and interquartile range. Apriori's vectorized counting and manageable candidate set can outperform FP-tree overhead on this small, sparse dataset. This explanation is not a profiling result, and timing varies by hardware, library version, dataset, and threshold.

## Challenges and decisions

- Variable-length CSV rows require basket-aware parsing rather than a rectangular table loader.
- Whitespace normalization prevents artificial category differences. Identical baskets across rows must not be removed as duplicates.
- Co-occurrence counts require integer multiplication rather than Boolean multiplication.
- Single items dominate support rankings, so additional plots show combinations of at least two categories.
- The thresholds emphasize recurring patterns while keeping the analysis interpretable; they are exploratory choices, not optimized settings.
- Popular consequents can inflate confidence. Lift compares conditional prevalence with baseline prevalence.

The notebook includes eight figures: item popularity and basket-size exploration, a co-occurrence heatmap, four itemset charts, a runtime comparison, and paired confidence-versus-lift scatterplots.

The data represent one store and one month, with category-level aggregation and no prices, quantities, customer identifiers, or timestamps in the CSV. Rules are exploratory and have no held-out validation. New transactions and a practical evaluation would be needed before using them operationally.

## Reproduce

Use Python 3.13 and install the packages below in an environment outside this repository:

```bash
python -m pip install pandas==3.1.1 numpy==2.5.4 matplotlib==3.11.2 seaborn==0.13.2 mlxtend==0.25.0 ipykernel jupyterlab
python -m jupyter lab MSCS_634_Lab_6.ipynb
```

Select that environment's Python kernel, then **Restart Kernel and Run All Cells**. Internet access is needed for the public dataset download. The submitted notebook already contains all results and plots for review without rerunning. Runtime comparisons are recalculated on each run.

## Dataset reference

Hahsler, M., Hornik, K., & Reutterer, T. (2006). *Implications of probabilistic data modeling for mining association rules*. In *From Data and Information Analysis to Knowledge Engineering*, pp. 598–605. Springer-Verlag.
