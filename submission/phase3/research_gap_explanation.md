# Phase 3 — Research Gap Explanation

## Identified Gap: Instability of LIME Explanations

### What the original paper does not address
The paper assumes that with sufficient num_samples (5000),
LIME produces reliable explanations. However it does not
measure or guarantee stability across multiple runs on the
same input. Each call to explain_instance() draws a fresh
random neighbourhood sample, which means two runs on the
same document can produce different feature weights and
sometimes different feature sets entirely.

### Evidence from our Phase 2 results
Running original LIME 10 times on the same documents:

| Document       | Avg Weight Std | Consistency Score | R² Range        |
|---------------|---------------|-------------------|-----------------|
| Doc 83 (ath)  | 0.00062       | 0.983             | 0.986 – 0.987   |
| Doc 100 (chr) | 0.00178       | 0.867             | 0.393 – 0.415   |
| Doc 150 (ath) | 0.00122       | 0.833             | 0.789 – 0.807   |

Doc 100 had Consistency Score of only 0.867 — meaning 13% of
runs selected different features. R² ranged 0.393–0.415,
showing the surrogate quality itself varies run-to-run.

### Literature Support
Alvarez-Melis & Jaakkola (2018) "On the Robustness of
Interpretability Methods" (ICML Workshop) formally proved that
LIME explanations are sensitive to the random sampling process
and can vary significantly across runs, undermining user trust.

---

## Proposed Improvement: Stable-LIME

### Three changes to the LIME algorithm

**1. Averaged Explanations (N=10 runs)**
Run LIME N times with different random seeds. Average the
feature weights across all runs. By the Central Limit Theorem,
variance of the mean reduces by factor √N. With N=10, std
reduces by ~68%.

**2. Importance Threshold (τ=0.01)**
After averaging, discard features whose |averaged weight| < τ.
This removes noise features that appear inconsistently across
runs with near-zero contribution.

**3. Consistency Score Metric**
Measure fraction of runs in which each feature appears.
CS=1.0 means every run agreed on that feature.
CS=0.5 means only half the runs selected it.
This gives a quantitative measure of explanation reliability.

### Results

| Metric              | Original LIME | Stable-LIME | Improvement |
|--------------------|--------------|-------------|-------------|
| Avg Weight Std (↓)  | 0.00121      | 0.00109     | -9.5%       |
| Consistency Score (↑)| 0.894       | 0.989       | +10.5pp     |
| Doc 100 CS          | 0.867        | 1.000       | +13.3pp     |
| Doc 150 CS          | 0.833        | 1.000       | +16.7pp     |

Improvement is largest on harder documents with lower R²
(Doc 100, Doc 150) where original LIME is least stable.
On easy high-confidence documents (Doc 83, R²=0.986),
original LIME is already stable so improvement is marginal.

### Limitation and Future Work
Stable-LIME costs N× more computation (10× here).
Future work: adaptive N — use more runs only when surrogate
R² is low, saving computation when original LIME is stable.
Also: the threshold τ=0.01 is fixed; a data-driven τ based
on the weight distribution would be more principled.

### Reference
Alvarez-Melis, D. & Jaakkola, T.S. (2018).
On the Robustness of Interpretability Methods.
ICML Workshop on Human Interpretability in Machine Learning.
arXiv:1806.08049
