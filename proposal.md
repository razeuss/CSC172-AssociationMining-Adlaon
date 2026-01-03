# CSC172 Data Mining and Analysis Project Proposal

**Student:** Joshua Radz T. Adlaon, 2022-2534  
**Date:** December 31, 2026  

## 1. Project Title
Explainable Disease–Symptom Pattern Mining with Apriori Association Rules

## 2. Problem Statement
Disease–symptom datasets are often stored in a simple table format: one disease label and multiple symptom fields per record. While easy to collect and store, this format is difficult to interpret because many symptoms are **non-specific** (e.g., fatigue, fever, nausea) and appear across multiple diseases. Looking at symptoms individually rarely explains the structure of the dataset or why certain symptom combinations appear consistently.

In many analysis workflows, the default approach is to build a predictive classifier. However, for final-project evaluation and stakeholder-friendly reporting, prediction alone is not sufficient: the analysis must produce **transparent, verifiable evidence** that can be inspected, ranked, and explained. Association Rule Mining (ARM) addresses this need by producing human-readable **IF–THEN rules** and quantifying their strength with standard metrics. ARM is especially suitable when the goal is to discover:
- which symptoms consistently co-occur as stable “clusters,” and
- which symptom combinations strongly align with specific disease labels—without relying on black-box model reasoning.

This project mines an explainable rule library from a Kaggle disease–symptom dataset by treating each patient record as a transaction (basket of symptoms). The output is a presentation-ready set of symptom co-occurrence rules and symptom→disease class association rules, supported by measurable metrics and clear visualizations.

## 3. Objectives
- Build a complete Association Rule Mining workflow that converts tabular symptom records into a high-quality transactional representation.
- Perform robust cleaning and standardization of symptom tokens to prevent “item fragmentation” (same symptom treated as different items due to formatting issues).
- Construct two mining views:
  - **Symptoms-only transactions** to discover symptom co-occurrence structure (Symptom → Symptom).
  - **Heterogeneous transactions** by appending a disease item (`disease::<DiseaseName>`) to enable **Symptoms → Disease** rules (Class Association Rules within ARM).
- Apply an **Apriori-style bounded frequent itemset strategy** (k ≤ 3) to keep patterns interpretable and presentation-ready.
- Generate rules and evaluate them using **support, confidence, lift, and leverage** (leverage included to provide grounded interpretation when lift inflates due to class balance).
- Produce clear, defensible outputs: ranked rule tables and visuals that summarize dataset structure and rule behavior.

## 4. Dataset Plan
- **Source:** Kaggle — “Disease Symptom Description Dataset”  
  https://www.kaggle.com/datasets/itachi9604/disease-symptom-description-dataset  
- **File Used:** `dataset.csv`
- **Scale (expected):**
  - 4,920 transactions (rows)
  - 41 disease classes
  - 17 symptom fields per row (some rows have fewer valid symptoms)
  - ~131 unique symptom items after cleaning
- **Acquisition:** Download CSV from Kaggle and load directly in Python (pandas).

## 5. Technical Approach

### Architecture & Pipeline

**Phase 1: Data Cleaning and Symptom Standardization**
- Normalize symptom strings (trim whitespace, lowercase, convert underscores to spaces, collapse repeated spaces).
- Drop empty symptom slots; remove duplicates within a transaction.
- Ensure disease labels are consistent for class association mining.

**Phase 2: Transaction Construction**
- Convert each row into a symptom set:  
  `T = {symptom_1, symptom_2, …}`
- Build a second “heterogeneous” view by appending the disease label as an item:  
  `T' = {symptom_1, symptom_2, …, disease::<DiseaseName>}`

**Phase 3: Exploratory Data Analysis (EDA)**
- Class balance visualization (rows per disease).
- Symptom frequency distribution (top symptoms by prevalence).
- Transaction length distribution (symptoms per record).
- Summary stats to justify minimum support thresholds and interpret rule behavior.

**Phase 4: Frequent Itemset Mining (Apriori-Style Bounded Strategy, k ≤ 3)**
- Count 1-itemsets, 2-itemsets, and 3-itemsets from transactions.
- Apply minimum-support pruning to retain only frequent patterns.
- Keep k ≤ 3 to preserve interpretability and presentation clarity.

**Phase 5: Association Rule Generation**
- **Symptom → Symptom rules:** derived from frequent pairs and triples (single-antecedent and two-antecedent rules).
- **Symptoms → Disease rules:** restrict consequents to a disease item (`disease::<…>`) to produce Class Association Rules.

**Phase 6: Rule Evaluation & Ranking**
- Support, confidence, lift, leverage computed for every rule.
- Rules ranked by a combination of confidence + leverage (and lift used cautiously in balanced-class context).

**Phase 7: Visualization & Communication**
- Jaccard similarity heatmap among top symptoms to highlight symptom cluster structure.
- Support vs confidence “rule landscape” plot (bubble size = leverage) for disease rules.
- Bipartite symptom–disease network for the strongest symptom→disease rules.

### Model & Algorithm Selection

| Component | Choice | Justification |
|---|---|---|
| Frequent Itemset Strategy | Apriori-style bounded mining (k ≤ 3) | Interpretable itemsets, feasible for dataset size, presentation-ready |
| Rule Types | Symptom→Symptom + Symptoms→Disease (CAR-style) | Reveals both structure and label-linked signatures |
| Metrics | Support, Confidence, Lift, Leverage | Standard ARM metrics; leverage stabilizes interpretation under class balance |
| Visuals | Frequency, distributions, similarity heatmap, rule landscape, bipartite network | Fast communication of structure + rule strength to non-technical viewers |
| Tools | pandas, numpy, matplotlib, networkx | Lightweight, reproducible, sufficient for this project scope |

### Framework & Dependencies
- Python 3.9+
- pandas, numpy
- matplotlib
- networkx (for rule networks)
- Jupyter Notebook / JupyterLab

### Hardware & Compute Strategy
- Runs on a standard laptop or Google Colab.
- Dataset scale is moderate; the bounded k ≤ 3 strategy is computationally feasible without specialized hardware.

### Reproducible Pipeline (Notebook Outputs)
1. Load `dataset.csv`
2. Clean/standardize symptoms and disease labels
3. Build transactions (symptoms-only and symptoms+disease)
4. EDA plots + dataset summary statistics
5. Count frequent itemsets (k ≤ 3) + apply support thresholds
6. Generate and filter rules by confidence thresholds
7. Export top rules and figures for README/report inclusion

## 6. Expected Challenges & Mitigations

| Challenge | Potential Issue | Mitigation Strategy |
|---|---|---|
| Token inconsistency | Same symptom appears as multiple items (underscores, spacing) | Strict standardization: lowercase, underscore-to-space, whitespace collapsing, dedupe within transactions |
| Threshold tuning | Too high support/conf removes informative rules; too low creates noisy rule explosion | Use EDA to motivate thresholds; iterate with small parameter sweep; keep k ≤ 3 to control complexity |
| Inflated lift under class balance | Perfectly balanced classes can produce very high lift for confidence=1 rules | Report **leverage** alongside lift; explain why lift becomes large due to low base rate |
| Rule redundancy | Many rules are variations of the same cluster | Add ranking + optional pruning (keep top-k by confidence/leverage; remove dominated rules) |
| Visual clutter | Networks/heatmaps can become unreadable if too dense | Limit to top-N symptoms/rules; use clear labeling; export publication-ready figures for README |
| Interpretation risk | Audience may read rules as clinical facts | Include explicit disclaimer: correlation-based patterns; dataset-level evidence only |

---
