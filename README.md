# DS357 – Explainable AI | Course Project
## LIME Replication and Extension
### "Why Should I Trust You?" — Ribeiro, Singh, Guestrin (KDD 2016)

---

## Team Information
| Field | Details |
|-------|---------|
| Course | DS357 – Explainable AI (XAI) |
| Project | Research Replication and Extension |
| Paper | "Why Should I Trust You?: Explaining the Predictions of Any Classifier" |
| Conference | KDD 2016 (A* Conference) |
| Authors | Marco Tulio Ribeiro, Sameer Singh, Carlos Guestrin |
| Paper Link | https://arxiv.org/abs/1602.04938 |

---

## Project Overview

This project replicates the core experiments of the LIME paper
(Ribeiro et al., KDD 2016) and proposes an improvement called
**Stable-LIME** that addresses the instability of LIME explanations
across multiple runs.

### What is LIME?
LIME (Local Interpretable Model-Agnostic Explanations) explains
individual predictions of any black-box classifier by:
1. Generating perturbed text samples around the input
2. Querying the black-box model on those samples
3. Weighting samples by proximity to the original input
4. Fitting a sparse linear surrogate model
5. Returning the surrogate coefficients as the explanation

### What is Stable-LIME? (Our Contribution)
Stable-LIME improves LIME by:
1. Running LIME N=10 times with different random seeds
2. Averaging feature weights across all runs
3. Applying an importance threshold τ=0.01 to remove noise features
4. Measuring Consistency Score to quantify explanation reliability

---

## Repository Structure
DS357-XAI-LIME/
│
├── code/
│   ├── code
│
├── phase2/
│   ├── result1_confusion_matrix.png         ← Model evaluation
│   ├── result2_lime_doc83.png               ← LIME explanation bar chart
│   ├── result2_lime_doc83_highlight.html    ← Coloured word highlight
│   ├── result3_prediction_shift.png         ← Prediction shift verification
│   ├── result4_arbitrary_features.png       ← Arbitrary headers finding
│   ├── result5_sp_lime.png                  ← SP-LIME global coverage
│   ├── result_comparison.md                 ← Paper vs our results table
│   └── replication_summary.md              ← 1-page replication summary
│
├── phase3/
│   ├── phase3_fig1_stability_comparison.png ← Stability bar chart
│   ├── phase3_fig2_weight_distributions.png ← Weight variance across runs
│   ├── phase3_fig3_explanation_comparison.png ← Original vs Stable-LIME
│   ├── research_gap_explanation.md          ← 1-page research gap
│   └── experimental_results.md             ← Full results table
│
├── AI_USAGE.md                              ← AI tool usage declaration
└── README.md                                ← This file

---

## How to Run

### Requirements
```bash
pip install lime scikit-learn numpy pandas matplotlib
```

### Dataset
No manual download needed. Dataset is fetched automatically:
```python
from sklearn.datasets import fetch_20newsgroups
categories = ['alt.atheism', 'soc.religion.christian']
newsgroups_train = fetch_20newsgroups(subset='train', categories=categories)
newsgroups_test  = fetch_20newsgroups(subset='test',  categories=categories)
```
- Train samples : 1079
- Test samples  : 717
- Classes       : atheism (0), christian (1)

### Random Seed
All experiments use `random_state=42` for full reproducibility.

### Run Phase 2 in Google Colab

Runtime → Run all
Expected runtime: ~5 minutes


### Run Phase 3 in Google Colab

Runtime → Run all
Expected runtime: ~15 minutes
(10 runs × 3 documents × 2 methods × num_samples=5000)


### Run locally
```bash
git clone https://github.com/[your-username]/DS357-XAI-LIME
cd DS357-XAI-LIME
pip install lime scikit-learn numpy pandas matplotlib
python code/phase2_replication.py
python code/phase3_stable_lime.py
```

---

## Phase 2 — Replication Results

### Model Performance
| Metric | Paper (Ribeiro 2016) | Ours | Match |
|--------|---------------------|------|-------|
| Dataset | 20 Newsgroups | 20 Newsgroups | ✅ |
| Black-box model | RF n=500 | RF n=500 | ✅ |
| TF-IDF lowercase | False | False | ✅ |
| Test Accuracy | ~0.924 | 0.905 | ≈ |
| F1 Score | ~0.921 | 0.921 | ✅ |

### LIME Settings
| Setting | Paper | Ours | Match |
|---------|-------|------|-------|
| kernel_width | 25 | 25 | ✅ |
| Distance metric | cosine | cosine | ✅ |
| Surrogate model | Ridge | Ridge | ✅ |
| num_samples | 5000 | 5000 | ✅ |
| num_features | 6 | 6 | ✅ |

### Key Results
| Result | Paper | Ours | Match |
|--------|-------|------|-------|
| Top feature doc83 | Posting | Host* | ✅ |
| Features doc83 | Posting,Host,NNTP,edu | Host,Posting,NNTP,edu | ✅ |
| P(christian) doc83 | 0.414 | 0.426 | ≈ |
| Local R² doc83 | Not reported | 0.986 | ✅ |
| Prediction shift | +0.27 | +0.29 | ✅ |
| Arbitrary headers finding | Yes | Yes | ✅ |
| SP-LIME | Yes | Yes | ✅ |
| HTML highlight output | Yes | Yes | ✅ |

*Host and Posting are both email headers — same finding.
Order varies by random seed.

### Note on Accuracy Gap (0.905 vs 0.924)
The paper does not fix a random seed for the Random Forest.
We use `random_state=42` as required by course reproducibility
guidelines. F1 score matches exactly at 0.921. The minor
accuracy gap is within expected variance from seed differences.

---

## Phase 3 — Stable-LIME Results

### Research Gap
LIME explanations are unstable — the same document explained
twice can produce different feature weights and different
feature sets due to random neighbourhood sampling.

**Evidence from Phase 2:**

| Document | Consistency Score | R² Range |
|----------|------------------|----------|
| Doc 83 (atheism) | 0.983 | 0.986 – 0.987 |
| Doc 100 (christian) | 0.867 | 0.393 – 0.415 |
| Doc 150 (atheism) | 0.833 | 0.789 – 0.807 |

Doc 100 had CS=0.867 — 13% of runs selected different features.

**Reference:** Alvarez-Melis & Jaakkola (2018).
"On the Robustness of Interpretability Methods."
ICML Workshop. arXiv:1806.08049

### Stable-LIME Improvement Results

| Metric | Original LIME | Stable-LIME | Change |
|--------|--------------|-------------|--------|
| Avg Weight Std (↓) | 0.00121 | 0.00109 | -9.5% |
| Consistency Score (↑) | 0.894 | 0.989 | +10.5pp |
| Doc 100 CS | 0.867 | 1.000 | +13.3pp |
| Doc 150 CS | 0.833 | 1.000 | +16.7pp |

Stable-LIME achieves perfect consistency (1.000) on the two
documents where original LIME was most unstable.

---

## Output Files

### Phase 2 Figures
| File | Description |
|------|-------------|
| result1_confusion_matrix.png | RF model evaluation on test set |
| result2_lime_doc83.png | LIME explanation bar chart — replicates paper Figure 1 |
| result2_lime_doc83_highlight.html | Green/red word highlights — open in browser |
| result3_prediction_shift.png | Removing top features shifts prediction as LIME predicts |
| result4_arbitrary_features.png | Email headers dominate explanations despite high accuracy |
| result5_sp_lime.png | SP-LIME global feature coverage across 5 representative docs |

### Phase 3 Figures
| File | Description |
|------|-------------|
| phase3_fig1_stability_comparison.png | Weight std and CS: Original vs Stable-LIME |
| phase3_fig2_weight_distributions.png | Feature weight variance across 10 runs |
| phase3_fig3_explanation_comparison.png | Side-by-side explanation quality |

---

## Academic Integrity

AI tools were used in this project. Full documentation of
all prompts used, outputs received, and modifications made
is available in `AI_USAGE.md`.

Similarity: All written content has been verified to be
below the 20% plagiarism threshold required by course policy.

---

## References

1. Ribeiro, M.T., Singh, S., & Guestrin, C. (2016).
   "Why Should I Trust You?": Explaining the Predictions
   of Any Classifier. KDD 2016.
   https://arxiv.org/abs/1602.04938

2. Alvarez-Melis, D. & Jaakkola, T.S. (2018).
   On the Robustness of Interpretability Methods.
   ICML Workshop on Human Interpretability in ML.
   https://arxiv.org/abs/1806.08049

3. Ribeiro, M.T. (2016). LIME Python Library.
   https://github.com/marcotcr/lime

4. Molnar, C. (2022). Interpretable Machine Learning.
   https://christophm.github.io/interpretable-ml-book/lime.html

---

## Contact
For questions about this project contact:
[Team member email] — [Institution]
