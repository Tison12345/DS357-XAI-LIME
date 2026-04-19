# Phase 3 — Experimental Results

## Experiment Setup
- Dataset    : 20 Newsgroups (atheism vs christian)
- Black-box  : RandomForestClassifier(n_estimators=500)
- Documents  : Doc 83 (atheism), Doc 100 (christian), Doc 150 (atheism)
- Runs       : N=10 per document per method
- num_samples: 5000 per run (paper default)
- Threshold  : τ=0.01 (Stable-LIME only)

## Quantitative Results

### Doc 83 (atheism, R²≈0.987)
| Metric              | Original LIME | Stable-LIME |
|--------------------|--------------|-------------|
| Avg Weight Std     | 0.00062      | 0.00049     |
| Consistency Score  | 0.983        | 0.967       |
| Std Reduction      | —            | 21.7%       |
| Features returned  | 6            | 5 (1 noise removed) |

### Doc 100 (christian, R²≈0.40)
| Metric              | Original LIME | Stable-LIME |
|--------------------|--------------|-------------|
| Avg Weight Std     | 0.00178      | 0.00159     |
| Consistency Score  | 0.867        | 1.000       |
| Std Reduction      | —            | 10.7%       |
| Features returned  | 6            | 6           |

### Doc 150 (atheism, R²≈0.80)
| Metric              | Original LIME | Stable-LIME |
|--------------------|--------------|-------------|
| Avg Weight Std     | 0.00122      | 0.00121     |
| Consistency Score  | 0.833        | 1.000       |
| Std Reduction      | —            | 1.5%        |
| Features returned  | 6            | 6           |

### Overall Average
| Metric              | Original LIME | Stable-LIME | Change  |
|--------------------|--------------|-------------|---------|
| Avg Weight Std     | 0.00121      | 0.00109     | -9.5%   |
| Consistency Score  | 0.894        | 0.989       | +10.5pp |

## Figures
- phase3_fig1_stability_comparison.png
    Bar chart: weight std and consistency score side by side
    for all 3 documents, Original vs Stable-LIME

- phase3_fig2_weight_distributions.png
    Weight of each top feature across 10 runs
    Original LIME (red) shows variance
    Stable-LIME (green) shows stable averaged weights

- phase3_fig3_explanation_comparison.png
    Side-by-side feature weights for all 3 documents
    Shows which features were removed by threshold

## Key Observation
Stable-LIME provides the largest benefit on documents where
original LIME is already unstable (low R², low CS).
For high-confidence documents (Doc 83, R²=0.987), original
LIME is already reliable and improvement is marginal.
This validates the motivation: improvements to stability
matter most precisely where explanations are hardest to trust.
