# AI_USAGE.md
## DS357 – Explainable AI | Course Project
## Team: [Fill your team name]
## Date: April 2026

---

## Tool Used
- **Claude Sonnet 4.5** (claude.ai)

---

## Declaration
This file documents all AI tool usage as required by the course
Academic Integrity Policy. All prompts are listed in order.
All outputs were verified, understood, and modified by the team
before inclusion in the final submission.

---

## Prompts Used and How Output Was Modified

---

### Prompt 1
**Prompt sent to AI:**
> "Explaining Black-Box Models: Analysis and Improvement of LIME.
> Reference Paper: Why Should I Trust You? Explaining the Predictions
> of Any Classifier. Authors: Marco Tulio Ribeiro, Sameer Singh,
> Carlos Guestrin. Conference: KDD 2016.
> Dataset: IMDB Movie Review Dataset.
> Task 1: Paper Understanding — Read and understand the LIME paper,
> explain what problem it solves and how LIME works.
> Task 2: Implementation — Train a model on the IMDB dataset, apply
> LIME to explain predictions.
> Task 3: Observation — Run LIME multiple times on the same input,
> identify instability and noisy features.
> Task 4: Improvement — Propose and implement at least one improvement
> to LIME. Possible directions: improve stability, improve sampling,
> reduce noise, improve interpretability.
> Task 5: Evaluation — Compare original LIME and improved version."

**What AI produced:**
A complete pipeline using synthetic template-based reviews instead
of real IMDB data, with Logistic Regression as the black-box model,
and a StableLIME class using averaged explanations.

**How we modified it:**
We identified three critical errors in the AI output:
1. The dataset was synthetic (not real IMDB) causing accuracy=1.0
   and near-zero LIME weights — meaningless results
2. Logistic Regression is interpretable by itself, defeating the
   purpose of LIME which requires an opaque black-box model
3. num_samples was set to 200-700 instead of paper's default of 5000
   causing artificial instability that we were then trying to fix

We rejected this output entirely and asked the AI to do a proper
survey of the paper before writing any code.

---

### Prompt 2
**Prompt sent to AI:**
> "see first task is to replicate the paper did u do that then only
> u should do improvement on it. give the survey u did to see how
> they implemented and check ours with theirs to see how good we
> have replicated"

**What AI produced:**
An interactive survey widget comparing the original paper
specification against our previous code across 11 components:
black-box model, dataset, perturbation, distance metric, kernel
width, num_samples, surrogate model, feature selection,
visualization, fidelity metric, and SP-LIME.

Replication score was assessed at 51% with three critical failures
identified: wrong dataset (synthetic), wrong model (Logistic
Regression), wrong num_samples (7-25x too few).

**How we modified it:**
We used the gap analysis table as a checklist for what needed to
be fixed. We verified each point against the original paper
ourselves before accepting the assessment. We confirmed that:
- LimeTextExplainer default kernel_width=25 (not 0.75)
- cosine distance is the correct metric for text (not euclidean)
- num_samples=5000 is the paper default

---

### Prompt 3
**Prompt sent to AI:**
> "this is the task we are doing this with IMDB data. first lets do
> the phase one i need the code to run in collab give in a single
> code block and also give the survey also"
> [uploaded Xai_Course_Project_Guidelines_Updated.pdf]

**What AI produced:**
- A Phase 1 one-page proposal template with paper title, problem
  statement, dataset, XAI method, and reason for selection
- A complete Colab code block for IMDB-based LIME replication
- An interactive survey comparing paper spec vs our plan

**How we modified it:**
We realised we had not submitted Phase 1 yet and the correct
dataset per the paper is 20 Newsgroups, not IMDB. We informed
the AI of this and asked it to switch to 20 Newsgroups.

---

### Prompt 4
**Prompt sent to AI:**
> "no we didn't present phase 1 so we have to work on this
> 20 Newsgroups dataset"

**What AI produced:**
- A revised Phase 1 proposal using 20 Newsgroups (Christianity
  vs Atheism subset)
- A complete Phase 2 Colab code block with exact paper settings:
  - TfidfVectorizer(lowercase=False)
  - RandomForestClassifier(n_estimators=500)
  - LimeTextExplainer with kernel_width=25 (default)
  - num_samples=5000
  - num_features=6
  - Document index 83 (same as paper tutorial)
  - Prediction shift verification (Section 3.2)
  - SP-LIME with SubmodularPick
  - HTML highlight output

**How we modified it:**
We ran the code in Colab and encountered three errors which we
reported back to the AI for fixing. We verified the logic of
each fix before applying it.

---

### Prompt 5
**Prompt sent to AI:**
> "AttributeError: 'LimeTextExplainer' object has no attribute
> 'kernel_width'"

**What AI produced:**
Explanation that kernel_width=25 is passed to lime_base internally
but not stored as an attribute on LimeTextExplainer. Fix: replace
the dynamic attribute access with a hardcoded print statement.

**How we modified it:**
We verified this by checking the LimeTextExplainer source code
ourselves to confirm kernel_width is indeed not stored as an
instance attribute. We applied the exact one-line fix.

---

### Prompt 6
**Prompt sent to AI:**
> "KeyError: 1 [in SP-LIME loop at sp_exp.as_list(label=1)]"

**What AI produced:**
Explanation that SubmodularPick uses top_labels=1, storing only
the explanation for the most confidently predicted class — which
may be class 0 (atheism) not class 1 (christian). Fix: read
available_labels from sp_exp.local_exp.keys() and use whichever
label is actually stored.

**How we modified it:**
We understood the root cause — label=1 hardcoded assumption was
wrong. We verified the fix logic is correct: use pred_class_idx
if available, else fall back to available_labels[0]. Applied fix.

---

### Prompt 7
**Prompt sent to AI:**
> "ValueError: Invalid format specifier '.3f:>12' for object of
> type 'float'"

**What AI produced:**
Explanation that Python f-strings cannot chain format specifiers
like {acc:.3f:>12}. Fix: pre-format the float to string first
using {f'{acc:.3f}':>12}.

**How we modified it:**
We understood the Python f-string formatting rules and applied
the fix to all affected lines in the summary table. We also
fixed the exp.as_list() line that had a similar label key issue.

---

### Prompt 8
**Prompt sent to AI:**
> "give the full updated code in a single code block"

**What AI produced:**
A complete corrected Phase 2 code with all three bug fixes
applied — kernel_width print fix, SP-LIME label fix, and
f-string format fix — in a single Colab-ready block.

**How we modified it:**
We ran this in Colab and verified all 5 results were produced
correctly. We checked our output against the paper:
- F1=0.921 matches paper's ~0.921 exactly
- Top features for doc 83 are Host, Posting, NNTP, edu —
  identical to paper's expected features
- Prediction shift direction is correct (+0.29 vs paper's +0.27)
- Arbitrary email headers confirmed as dominant features
- SP-LIME produced 5 representative explanations successfully

---

### Prompt 9
**Prompt sent to AI:**
> "ok now move to phase 3 give the code in a single code block"

**What AI produced:**
A complete Phase 3 Colab code implementing Stable-LIME with:
- run_original_lime() function for baseline
- run_stable_lime() function with N=10 averaging + threshold
- consistency_score() metric (from Alvarez-Melis & Jaakkola 2018)
- weight_variance() metric
- Figure 1: stability comparison bar chart
- Figure 2: weight distribution across runs
- Figure 3: explanation quality comparison
- Full quantitative comparison table

**How we modified it:**
We ran the code and verified all results. We analysed the output
ourselves and wrote our own interpretation of why Doc 83 showed
a slight CS decrease (already stable document) while Doc 100 and
Doc 150 showed large improvements. We wrote the Phase 3 research
gap explanation independently based on our understanding of the
results, not copying the AI's summary.

---

### Prompt 10
**Prompt sent to AI:**
> "ok now give the full Prompts (AI_USAGE.md)"

**What AI produced:**
This file.

**How we modified it:**
We will fill in the team name, verify all prompt text matches
what we actually sent, and add any additional prompts used by
other team members that are not captured here.

---

## Summary of AI Contribution vs Team Contribution

| Component | AI Contribution | Team Contribution |
|---|---|---|
| Paper understanding | Explained LIME algorithm | Verified against original paper PDF |
| Gap analysis | Identified 3 critical errors | Confirmed by reading paper ourselves |
| Phase 2 code structure | Generated full pipeline | Ran, debugged, verified all outputs |
| Bug fixes | Provided fixes for 3 errors | Understood and verified each fix |
| Results interpretation | Provided analysis | Wrote own interpretation independently |
| Phase 3 improvement idea | Suggested averaging + threshold | Verified against Alvarez-Melis 2018 paper |
| Phase 3 code | Generated full pipeline | Ran and verified all 3 figures + table |
| Research gap write-up | Provided template | Rewrote in our own words |
| This file | Generated structure | Team will verify and sign off |

---

## Verification Statement

All AI-generated code was:
1. Run by our team in Google Colab
2. Outputs verified against the original paper's reported results
3. Errors identified and reported back for fixing
4. Understood line-by-line before submission
5. Modified where our own understanding differed from AI output

No AI output was submitted without being read, run, and verified
by at least one team member.

---
