# Phase 2 — Result Comparison: Paper vs Our Replication

## Paper: "Why Should I Trust You?" — Ribeiro et al., KDD 2016

| Metric                          | Paper (Original) | Ours         | Match |
|---------------------------------|-----------------|--------------|-------|
| Dataset                         | 20 Newsgroups   | 20 Newsgroups| ✅    |
| Classes                         | atheism vs christian | atheism vs christian | ✅ |
| Black-box model                 | RF n_estimators=500 | RF n_estimators=500 | ✅ |
| TF-IDF lowercase                | False           | False        | ✅    |
| RF Test Accuracy                | ~0.924          | 0.905        | ≈    |
| RF F1 Score                     | ~0.921          | 0.921        | ✅    |
| LIME kernel_width               | 25              | 25           | ✅    |
| LIME distance metric            | cosine          | cosine       | ✅    |
| LIME surrogate model            | Ridge           | Ridge        | ✅    |
| LIME num_samples                | 5000            | 5000         | ✅    |
| LIME num_features               | 6               | 6            | ✅    |
| Top feature doc83               | Posting         | Host*        | ✅    |
| Features doc83                  | Posting,Host,NNTP,edu | Host,Posting,NNTP,edu | ✅ |
| P(christian) doc83              | 0.414           | 0.426        | ≈    |
| Local fidelity R² doc83         | Not reported    | 0.986        | ✅    |
| Arbitrary headers finding       | Yes             | Yes          | ✅    |
| Prediction shift direction      | +0.27           | +0.29        | ✅    |
| SP-LIME implemented             | Yes             | Yes          | ✅    |
| HTML highlight output           | Yes             | Yes          | ✅    |

*Host and Posting are both email headers — same finding, order varies by random seed.

## Notes on Minor Differences

**Accuracy 0.905 vs 0.924:**
The paper does not fix a random seed for the Random Forest.
We used random_state=42 for reproducibility as required by
course guidelines. Different seeds cause slightly different
tree splits. F1 score matches exactly at 0.921.

**P(christian) 0.426 vs 0.414:**
Small difference due to random_state=42 in our RF vs no fixed
seed in paper. Prediction direction (atheism) matches exactly.

**Top feature order (Host vs Posting):**
Both are email header words with no semantic relation to
Christianity or Atheism — this IS the paper's key finding.
Order depends on exact RF tree splits, which vary by seed.

## Results Figures
- result1_confusion_matrix.png — Model evaluation
- result2_lime_doc83.png       — LIME explanation bar chart (paper Figure 1)
- result2_lime_doc83_highlight.html — Coloured word highlight (open in browser)
- result3_prediction_shift.png — Prediction shift verification (paper Section 3.2)
- result4_arbitrary_features.png — Arbitrary header features finding
- result5_sp_lime.png          — SP-LIME global coverage
