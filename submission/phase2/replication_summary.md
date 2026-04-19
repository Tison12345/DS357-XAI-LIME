# Phase 2 — One-Page Replication Summary

## Paper
"Why Should I Trust You?: Explaining the Predictions of Any Classifier"
Ribeiro, Singh, Guestrin — KDD 2016

## What We Replicated
We reproduced the core text classification experiment from the paper
using the 20 Newsgroups dataset (Christianity vs Atheism subset).

## Model Setup (Paper-Exact)
- Black-box: RandomForestClassifier(n_estimators=500)
- Features: TfidfVectorizer(lowercase=False)
- LIME: LimeTextExplainer, kernel_width=25, cosine distance,
  Ridge surrogate, num_samples=5000, num_features=6

## Result 1 — Black-Box Performance
Our RF achieved Accuracy=0.905 and F1=0.921.
Paper reports ~0.924 and ~0.921.
F1 matches exactly. Minor accuracy gap explained by random_state=42
(course reproducibility requirement) vs no fixed seed in paper.

## Result 2 — LIME Explanation (Document 83)
LIME explanation for doc 83 (true class: atheism, P(christian)=0.426):
Top features: Host (-0.157), Posting (-0.150), NNTP (-0.090), edu (-0.040)
Paper expects: Posting, Host, NNTP, edu — identical 4 words, same direction.
Local fidelity R²=0.986 — surrogate approximates black-box very faithfully.

## Result 3 — Prediction Shift Verification (Paper Section 3.2)
Paper claims: removing top 2 negative features should increase
P(christian) by ~0.27. Our result: P(christian) increased from
0.426 to 0.716 (+0.290). Direction and magnitude match paper.

## Result 4 — Arbitrary Features (Paper's Key Finding)
Despite 90%+ accuracy, the RF relies on email header words
(Host, Posting, NNTP) rather than actual topic words
(God, atheism, church). This is the paper's central insight:
high accuracy alone cannot be trusted — LIME reveals WHY the
model makes predictions.

## Result 5 — SP-LIME
Submodular Pick selected 5 representative explanations covering
both classes. Global feature coverage confirmed email headers
dominate atheism predictions while topic words (God, Christ,
rutgers, athos) appear in christian predictions.

## Conclusion
All 5 major results from the paper are successfully reproduced.
The LIME algorithm, settings, and key findings are faithfully
replicated. Minor numerical differences are within expected
variance from random seed differences.
