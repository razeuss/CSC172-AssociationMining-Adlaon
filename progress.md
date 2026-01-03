# CSC172 Association Rule Mining Project Progress Report
**Student:** Joshua Radz T. Adlaon, 2022-2534  
**Date:** December 31, 2026  
**Repository:** https://github.com/razeuss/CSC172-AssociationMining-Adlaon

## Current Status
| Milestone | Status | Notes |
|---|---|---|
| Dataset Preparation | ✅ Completed | `dataset.csv` loaded; 4,920 transactions verified |
| Data Preprocessing | ✅ Completed | Symptom standardization + deduplication applied |
| EDA & Visualization | ✅ Completed | Class balance, top symptoms, transaction-length distribution produced |
| Apriori-Style Mining (k ≤ 3) | ✅ Completed | Frequent 1/2/3-itemsets counted and pruned via min-support |
| Rule Generation | ✅ Completed | Symptom→Symptom + Symptoms→Disease (CAR-style) rules generated |
| Rule Evaluation & Ranking | ✅ Completed | Support, confidence, lift, leverage computed; top rules tabulated |
| README / Report Draft | ✅ Completed | Draft structured for presentation; figures referenced |
| Video Presentation | ⏳ In Progress | Will record final walkthrough + demo of outputs |

---

## 1. Dataset Progress
- **Total transactions:** 4,920
- **Disease classes:** 41 (perfectly balanced: 120 rows per disease)
- **Unique symptoms after cleaning:** 131
- **Symptoms per transaction:** min = 3, max = 17, mean ≈ 7.45, median = 6
- **Preprocessing applied:**
  - lowercase normalization
  - underscore → space conversion
  - whitespace collapsing
  - empty slot removal
  - duplicate symptom removal per transaction

**Sample transaction preview:**
Transaction example: {'itching', 'skin rash', 'nodal skin eruptions', 'dischromic patches', 'disease::Fungal infection'}

---

## 2. EDA Progress

**Key Findings:**
- The dataset is **perfectly class-balanced**, which directly affects interpretation of lift for disease rules.
- Top symptoms by prevalence:
  1. Fatigue (≈ 39.27%)
  2. Vomiting (≈ 38.90%)
  3. High fever (≈ 27.68%)
  4. Loss of appetite (≈ 23.41%)
  5. Nausea (≈ 23.29%)

**Visualizations completed for presentation:**
- Disease class balance (rows per disease)
- Top symptom frequency (bar chart)
- Symptoms per transaction (histogram)
- Symptom similarity heatmap (Jaccard similarity for top symptoms)
- Support vs confidence rule landscape (bubble size = leverage)
- Bipartite symptom–disease network (top strong rules)

---

## 3. Mining & Rule Results (Current Outputs)

**Rule mining configuration**
- Symptom→Symptom:
  - min_support = 0.03 (≥ 148 records)
  - min_confidence = 0.50
- Symptoms→Disease (CAR-style):
  - min_support = 0.02 (≥ 99 records)
  - min_confidence = 0.40
  - consequent constrained to disease item (`disease::<…>`)

**Generated outputs**
- **Symptom → Symptom rules:** 1,025 rules
  - 143 single-antecedent rules
  - 882 two-antecedent rules
- **Symptoms → Disease rules:** 1,006 rules
  - 120 single-symptom rules
  - 886 two-symptom rules
- **Disease coverage (CAR rules):** 39 / 41 diseases covered under current thresholds  
  - Missing under thresholds: Heart attack, Hepatitis D

**Example high-strength Symptom → Symptom rules (from ranked outputs)**
- yellowing of eyes → loss of appetite (high confidence; strong leverage)
- nausea → vomiting (high support; strong confidence)

**Example single-symptom disease signatures**
- increased appetite → disease::Diabetes
- pain behind the eyes → disease::Dengue

---

## 4. Challenges Encountered & Solutions

| Issue | Status | Resolution |
|---|---|---|
| Symptom token inconsistency | ✅ Fixed | Standardized tokens (lowercase, underscore-to-space, whitespace normalization) |
| “Too perfect” lift values | ✅ Addressed | Reported leverage + explained lift inflation due to balanced class priors |
| Rule volume management | ✅ Fixed | Applied minimum support/confidence and bounded k ≤ 3 mining |
| Visualization readability | ✅ Fixed | Limited to top symptoms/rules; used presentation-ready labeling and layout |

---

## 5. Next Steps (Finalization)
- Export final figures into an `assets/` folder using consistent filenames for README.
- Add the final video link to the “Video Presentation” section.
- (Optional enhancement) Run a small parameter sweep to show how rule coverage changes with min_support and include a short summary table in the report.

---
