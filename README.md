# Explainable Disease–Symptom Pattern Mining with Apriori Association Rules
**CSC172 Data Mining and Analysis Final Project**  
*Mindanao State University – Iligan Institute of Technology*  
**Student:** Joshua Radz T. Adlaon (2022-2534)  
**Semester:** AY 2025–2026 | Sem 1  

## Abstract

Symptom–disease datasets are naturally “transactional”: each record can be viewed as a basket of symptoms linked to a disease label. However, interpreting these datasets is not straightforward. Many symptoms are **non-specific** (e.g., fatigue, fever, nausea) and appear across multiple diseases, making it difficult to understand which symptom combinations are meaningful. While predictive models can output a label, they often fail to provide the kind of **transparent reasoning** required in classroom evaluation, auditing, or explainability-focused analysis. In contrast, Association Rule Mining (ARM) produces simple and readable statements—**“if these symptoms appear, then this symptom or disease tends to appear as well”**—and quantifies their strength with standard rule metrics.

This project applies a canonical **Apriori-style frequent itemset mining strategy** to the **Disease Symptom Description Dataset** (`dataset.csv`) from Kaggle, treating each patient record as a transaction. The work produces two complementary sets of knowledge. First, it mines **Symptom → Symptom** association rules to uncover stable co-occurrence patterns that can be interpreted as symptom clusters or dataset “signatures.” These rules help answer questions such as: *Which symptoms consistently occur together?* and *Which symptom combinations represent recognizable patterns?* Second, it enables **Class Association Rules (CARs)** by representing the disease label as a heterogeneous item (`disease::<DiseaseName>`), allowing the discovery of **Symptoms → Disease** rules within the same ARM framework. This dual-view design creates both structure-focused insights (symptom clustering) and outcome-linked insights (symptom combinations associated with disease labels).

To ensure the project is presentation-ready, the pipeline emphasizes clean preprocessing, reproducible transaction construction, clear rule-ranking tables, and visuals that summarize dataset structure and rule behavior. Rules are evaluated using **support, confidence, lift, and leverage**, where leverage is included to provide a more grounded view of rule usefulness—especially important in a dataset with perfectly balanced class counts. Exploratory Data Analysis (EDA) motivates parameter choices and supports interpretation by showing symptom prevalence, transaction-size distribution, symptom similarity structure, and the connectivity of strong symptom–disease relationships. The contribution of this work is a complete and defensible ARM workflow that produces **interpretable patterns** and **clear visual evidence**, suitable for final project presentation and verification.

---

## Table of Contents
- [Abstract](#abstract)
- [Introduction](#introduction)
  - [Problem Statement](#problem-statement)
  - [Objectives](#objectives)
  - [Scope and Limitations](#scope-and-limitations)
- [Dataset Description](#dataset-description)
  - [Source](#source)
  - [Schema](#schema)
  - [Dataset Statistics](#dataset-statistics)
- [Methodology](#methodology)
  - [1) Data Cleaning and Standardization](#1-data-cleaning-and-standardization)
  - [2) Transaction Construction](#2-transaction-construction)
  - [3) Apriori Mining Strategy](#3-apriori-mining-strategy)
  - [4) Rule Generation](#4-rule-generation)
  - [5) Rule Quality Metrics](#5-rule-quality-metrics)
  - [6) Visualization Design](#6-visualization-design)
  - [Reproducibility](#reproducibility)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Results](#results)
  - [A) Symptom → Symptom Rules](#a-symptom--symptom-rules)
  - [B) Symptoms → Disease Rules (Class Association Rules)](#b-symptoms--disease-rules-class-association-rules)
  - [C) Cross-Pattern Structure (Similarity + Network)](#c-cross-pattern-structure-similarity--network)
- [Discussion](#discussion)
  - [Key Insights](#key-insights)
  - [Why Some Rules Look “Too Perfect”](#why-some-rules-look-too-perfect)
  - [Limitations](#limitations)
  - [Future Improvements](#future-improvements)
- [Conclusion](#conclusion)
- [Video Presentation](#video-presentation)

---

## Introduction

### Problem Statement

Disease–symptom datasets are commonly used in educational mining projects because they are easy to store in a table format: one disease label and multiple symptom fields. Yet the moment we attempt to interpret them, several real data-mining challenges appear:

1. **Symptoms are highly overlapping and weak individually.** A single symptom often appears in many diseases, making it difficult to use one symptom as evidence for any meaningful conclusion. What matters is the **pattern**: which symptoms appear together, how consistently they co-occur, and whether certain symptom sets behave like a recognizable signature.
2. **Interpretability is a core requirement for project evaluation.** In a final project setting, it is not enough to say “the model predicts X.” The project must explain:
   - what patterns exist,
   - how strong they are,
   - and why those patterns should be trusted as data-driven evidence.
3. **Noisy symptom tokens can break mining quality.** Formatting issues (underscores, inconsistent spacing, duplicates, missing symptom slots) fragment the item space and lead to misleading results. For ARM, consistency is critical because each symptom token becomes an item.
4. **We need outcome-linked rules without sacrificing transparency.** A strong project should not only find symptom clusters, but also show how symptom combinations relate to disease labels—while remaining interpretable and measurable.

This project solves an explainability-centered data mining problem:

> **How can we extract strong, human-readable patterns from symptom transactions—both symptom co-occurrence structure and symptom combinations linked to disease labels—using a defensible Apriori-based Association Rule Mining approach and presentation-quality evidence?**

To answer this, the project treats each row as a transaction (basket of symptoms) and mines:
- **Symptom → Symptom rules** for co-occurrence structure, and  
- **Symptoms → Disease rules** (Class Association Rules) by representing disease labels as a special item (`disease::<DiseaseName>`).

The output is not a black-box decision; it is a ranked set of **transparent rules**, supported by metrics and visuals that make conclusions easy to justify.

### Objectives

This project aims to build a complete and verifiable ARM pipeline with outputs that are easy to present:

- **Data Quality and Consistency**
  - Clean and standardize symptom tokens to prevent item fragmentation (e.g., whitespace removal, underscore handling, normalization, and deduplication within transactions).
  - Ensure disease labels remain consistent and suitable for class-based rule mining.

- **Transaction Modeling**
  - Construct **symptoms-only transactions** for pure co-occurrence mining.
  - Construct **heterogeneous transactions** by appending `disease::<DiseaseName>` to support Class Association Rules (Symptoms → Disease).

- **Apriori-Based Pattern Mining**
  - Implement Apriori-style mining for frequent itemsets (1-itemsets, 2-itemsets, and 3-itemsets), applying pruning via minimum support thresholds.
  - Keep itemsets small enough to remain interpretable, auditable, and presentation-ready.

- **Rule Discovery and Evaluation**
  - Generate association rules from frequent itemsets and rank them using:
    - **Support** (prevalence),
    - **Confidence** (conditional reliability),
    - **Lift** (strength beyond chance),
    - **Leverage** (absolute improvement beyond chance; helpful when lift inflates due to balanced class rates).

- **Exploration, Interpretation, and Communication**
  - Use EDA to understand symptom prevalence, transaction size distribution, and dataset structure.
  - Produce clear tables and visuals that communicate both:
    - dataset structure (frequency, distribution), and
    - mining outcomes (rule quality, similarity, network structure).

### Scope and Limitations

**Scope**
- Mine **frequent symptom combinations** and generate **association rules** using an Apriori-style approach.
- Produce two rule families aligned with ARM objectives:
  - **Symptom → Symptom** rules to reveal co-occurrence signatures.
  - **Symptoms → Disease** rules (CARs) using heterogeneous items (`disease::<DiseaseName>`).
- Evaluate and present results using standard ARM metrics and visuals (frequency, transaction structure, similarity heatmap, rule-quality plot, bipartite network).

**Limitations**
- The dataset is **perfectly class-balanced** (equal rows per disease), which can cause lift to become very large for confidence=1.0 class rules. The project addresses interpretability by reporting **leverage** alongside lift.
- Association rules capture **co-occurrence**, not causation; strong rules do not prove medical relationships.
- Symptom representation is **binary presence/absence** (no severity, timing, or context), limiting realism.
- Findings reflect patterns in the dataset and may not generalize to real clinical populations.
- Mining quality depends on preprocessing; any remaining inconsistencies in symptom naming can still affect results.

---

## Dataset Description

### Source
Kaggle: Disease Symptom Description Dataset  
(You are using the exported file `dataset.csv` from that dataset.)

### Schema

| Column | Description |
|---|---|
| `Disease` | Disease label (class) |
| `Symptom_1` … `Symptom_17` | Symptom tokens (strings), some rows have fewer than 17 valid symptoms |

### Dataset Statistics

- **Rows (transactions):** 4,920  
- **Diseases (classes):** 41  
- **Rows per disease:** 120 each (perfectly balanced)  
- **Unique symptoms:** 131  
- **Symptoms per transaction:** min = 3, max = 17, average ≈ 7.45  

---

## Methodology

This project is implemented in two notebooks:
- `data_cleaning_standardization.ipynb` (cleaning + export of cleaned dataset and transactions)
- `arm.ipynb` (EDA, frequent itemset counting, rule generation, ranking, and visualizations)

### Pipeline Overview (End-to-End)
1. **Load data** (prefer cleaned dataset if available)  
2. **Clean & standardize symptom tokens** (consistent item space)  
3. **Construct transactions** (symptoms-only and symptoms+disease)  
4. **Count frequent itemsets (k = 1 to 3)** using an Apriori-style bounded strategy  
5. **Generate rules** (Symptom→Symptom and Symptoms→Disease) with thresholds  
6. **Compute metrics** (support, confidence, lift, leverage)  
7. **Rank results + visualize** for presentation-ready interpretation  

### 1) Data Cleaning and Standardization
Notebook: `data_cleaning_standardization.ipynb`

ARM is extremely sensitive to token inconsistency—two spellings of the same symptom become two different “items.” To prevent fragmented patterns, the notebook performs rule-based cleaning:

- **Symptom token cleaning**
  - trims whitespace,
  - replaces underscores `_` with spaces,
  - collapses repeated spaces,
  - converts to lowercase (e.g., `"High_Fever "` → `"high fever"`),
  - removes null/empty symptom fields.
- **Disease label cleaning**
  - strips leading/trailing whitespace while keeping the original wording.

Outputs saved for reproducibility:
- cleaned dataset: `outputs/dataset_cleaned.csv`
- symptom-only transactions: `outputs/transactions_symptoms.csv`
- symptom+disease transactions: `outputs/transactions_symptoms_plus_disease.csv`

### 2) Transaction Construction
Notebook: `arm.ipynb`

Each row is modeled as a basket (set) of symptoms:

- **Symptoms-only view**  
  `sym_transactions`: each transaction = sorted unique symptom set  
  Used for Symptom → Symptom rules.

- **Heterogeneous view (Symptoms + Disease-as-Item)**  
  `sym_dis_transactions`: same symptom set plus a disease item `disease::<DiseaseName>`  
  Used for Symptoms → Disease rules (CAR-style mining within ARM).

Important transaction handling decisions (implemented in the notebook):
- duplicates within a row are removed (`set()`),
- transactions are sorted for consistent itemset keys,
- transactions with at least 2 symptoms are kept for symptom-only mining (the dataset already satisfies this in practice).

### 3) Apriori Mining Strategy
This project uses an **Apriori-style bounded approach (k ≤ 3)** that is accurate to the notebook implementation:

**Frequent itemset counting (k = 1, 2, 3)**
- For each transaction, the notebook enumerates combinations of size 1, 2, and 3:
  - **1-itemsets** counted via a `Counter` update (`c1`)
  - **2-itemsets** counted via pair combinations (`c2`)
  - **3-itemsets** counted via triple combinations (`c3`)
- Pair and triple keys are kept in canonical order (sorted tuples) so counts are consistent.

**Thresholding**
Instead of generating larger candidate sets iteratively, the notebook:
- counts all itemsets up to size 3 (feasible due to moderate basket sizes),
- then applies minimum-support pruning using count thresholds:

For Symptom → Symptom mining:
- `min_support_ss = 0.03` → minimum count ≈ `ceil(0.03 × 4920) = 148`
- `min_confidence_ss = 0.50`

For Symptoms → Disease mining:
- `min_support_sd = 0.02` → minimum count ≈ `ceil(0.02 × 4920) = 99`
- `min_confidence_sd = 0.40`

Why cap k at 3?
- it keeps results interpretable (pairs and triples are easy to explain),
- it keeps runtime small while still capturing meaningful symptom patterns,
- it matches the project’s goal: explainable and presentation-ready rules.

### 4) Rule Generation
Rules are generated directly from counted frequent itemsets.

**A) Symptom → Symptom rules**
- From frequent pairs `(A, B)`:
  - generate both directions `A → B` and `B → A` (each checked against confidence threshold).
- From frequent triples `(A, B, C)`:
  - generate `AB → C`, `AC → B`, `BC → A` (antecedent length = 2).

**B) Symptoms → Disease rules (Class Association Rules via heterogeneous items)**
Rules are constrained so the **consequent must be a disease item**:

- From 2-itemsets: `{symptom, disease::<X>}` → `symptom → disease::<X>`
- From 3-itemsets: `{symptom1, symptom2, disease::<X>}` → `{symptom1, symptom2} → disease::<X>`

To ensure correctness and clarity, the notebook only accepts triples that contain **exactly one disease item** and treats all remaining items as symptoms. Rules are kept only if they satisfy support and confidence thresholds.

### 5) Rule Quality Metrics
For every rule `X → Y`, the notebook computes:

- **Support:** `P(X ∪ Y)`  
- **Confidence:** `P(Y | X)`  
- **Lift:** `confidence / P(Y)` (strength beyond chance)  
- **Leverage:** `P(X ∪ Y) − P(X)P(Y)` (absolute improvement beyond chance)

Leverage is especially emphasized because the dataset’s balanced class distribution can inflate lift values for class rules.

### 6) Visualization Design
The notebook creates visuals that directly support interpretation and presentation:

- **Transaction size histogram**: shows how many symptoms appear per record (basket size).
- **Top symptom frequency bar chart**: highlights dominant symptoms and motivates multi-symptom rules.
- **Jaccard similarity heatmap (top 20 symptoms)**: shows which frequent symptoms behave similarly across transactions using intersection/union similarity.
- **Support vs Confidence bubble plot (Symptoms → Disease rules)**:
  - selects a readable subset (top confident rules),
  - bubble size reflects leverage,
  - annotates standout rules for presentation.
- **Bipartite symptom–disease network (top 35 edges)**:
  - symptom nodes on one side, disease nodes on the other,
  - edges represent strong single-symptom rules,
  - edge width reflects relative rule strength for quick visual scanning.

### Reproducibility
Run order:
1. `data_cleaning_standardization.ipynb` (generates cleaned outputs)  
2. `arm.ipynb` (EDA → mining → rules → visuals)

Figures exported from `arm.ipynb` should be placed in the `assets/` folder using the filenames referenced below.

---

## Exploratory Data Analysis

EDA is used to understand the dataset before rule mining.

<!-- REQUIRED VISUAL (arm.ipynb): Title = "Disease Class Balance (Rows per Disease)" -->
![Disease Class Balance (Rows per Disease)](assets/disease_class_balance.png)

<!-- REQUIRED VISUAL (arm.ipynb): Title = "Top 15 Symptoms by Frequency" -->
![Top 15 Symptoms by Frequency](assets/top15_symptoms.png)

<!-- REQUIRED VISUAL (arm.ipynb): Title = "Distribution of Symptoms per Transaction" -->
![Distribution of Symptoms per Transaction](assets/distribution_of_symptoms.png)

EDA highlights:
- The dataset is class-balanced (equal rows per disease), influencing how lift behaves for class rules.
- Some symptoms are extremely frequent across many diseases, motivating the need for multi-symptom rules.
- Basket sizes are moderate (≈7–8 symptoms on average), making bounded Apriori counting feasible and interpretable.

---

## Results

### Mining Summary (Configuration + Output Size)

The following settings were used in `arm.ipynb`, and the mining produced rule sets that are large enough to be meaningful but still manageable for presentation:

| Component | Thresholds / Output |
|---|---|
| **Symptom → Symptom** | `min_support = 0.03` (≥ 148 rows), `min_confidence = 0.50` |
| **Symptoms → Disease (CAR)** | `min_support = 0.02` (≥ 99 rows), `min_confidence = 0.40` |
| **# Symptom → Symptom rules** | **1,025** rules (143 single-antecedent, 882 two-antecedent) |
| **# Symptoms → Disease rules** | **1,006** rules (120 single-symptom, 886 two-symptom) |
| **Diseases covered by CAR rules** | **39 / 41** diseases under current thresholds |

> Note: Two diseases (**Heart attack** and **Hepatitis D**) do not appear in the CAR rule set under the current minimum support/confidence thresholds. This is an important and meaningful outcome: it suggests that their symptom patterns are either less distinctive or do not meet the “high-coverage” requirement imposed by `min_support_sd`.

---

### A) Symptom → Symptom Rules

These rules capture **stable symptom co-occurrence patterns**—in other words, symptom clusters that behave like signatures in the dataset. Because rules pass both support and confidence thresholds, they represent patterns that are not only strong but also reasonably frequent.

**Examples (high confidence + high leverage):**

| Antecedent | Consequent | Support | Confidence | Lift | Leverage |
|---|---|---:|---:|---:|---:|
| yellowing of eyes | loss of appetite | 0.1598 | 0.9632 | 4.1138 | 0.1209 |
| yellowish skin | abdominal pain | 0.1549 | 0.8355 | 3.9833 | 0.1160 |
| nausea | vomiting | 0.1988 | 0.8534 | 2.1937 | 0.1082 |
| abdominal pain | vomiting | 0.1768 | 0.8430 | 2.1670 | 0.0952 |
| high fever | fatigue | 0.1988 | 0.7181 | 1.8286 | 0.0901 |

**What these patterns mean (interpretation):**
- Rules involving **yellowing of eyes / yellowish skin / loss of appetite** form a strong co-occurrence cluster, suggesting a consistent “jaundice-like” signature in the dataset.
- **Nausea → vomiting** and **abdominal pain → vomiting** represent a gastrointestinal cluster that appears frequently across the transactions.
- **High fever → fatigue** reflects the presence of general systemic symptoms that commonly travel together.

**Standout rules by lift (highly specific co-occurrence):**  
Some patterns are less common overall but are extremely “tight” when they occur. High-lift rules signal these specialized pairings:

- *Example family:* rules involving **mood swings ↔ abnormal menstruation** show very high lift, indicating an unusually strong association compared to baseline symptom rates.

---

### B) Symptoms → Disease Rules (Class Association Rules)

The heterogeneous-item design enables class association rules where the consequent is always a disease label. Under the current settings, these rules tend to represent **high-coverage disease signatures**, because `min_support_sd = 0.02` requires the symptom(s) to appear in at least ~99 of the 120 records for that disease (in many cases, the symptom appears in all 120).

**Examples (single-symptom “signature” rules):**

| Symptom | Disease | Support | Confidence | Lift |
|---|---|---:|---:|---:|
| increased appetite | Diabetes | 0.0244 | 1.0000 | 41.0 |
| polyuria | Diabetes | 0.0244 | 1.0000 | 41.0 |
| pain behind the eyes | Dengue | 0.0244 | 1.0000 | 41.0 |
| receiving blood transfusion | Hepatitis B | 0.0244 | 1.0000 | 41.0 |
| blood in sputum | Tuberculosis | 0.0244 | 1.0000 | 41.0 |

**Examples (two-symptom CAR rules):**
- `{increased appetite, polyuria} → Diabetes`  
- `{back pain, pain behind the eyes} → Dengue`  

These two-symptom rules are useful because they demonstrate how adding one extra symptom can produce a highly interpretable “evidence bundle” that aligns with a disease class.

**Rule-quality view (support vs confidence; bubble size = leverage):**  
![Symptoms → Disease Rules: Support vs Confidence (Bubble Size = Leverage)](assets/support_vs_conf.png)

**How to read this plot:**
- Points toward the upper-right indicate rules that are both common (higher support) and reliable (higher confidence).
- Bubble size emphasizes leverage, helping you prioritize rules that improve meaningfully beyond chance.
- In this dataset, many strong class rules cluster near support ≈ 0.0244 because diseases have the same row count.

---

### C) Cross-Pattern Structure (Similarity + Network)

**1) Symptom similarity (Jaccard heatmap)**  
The heatmap supports the symptom-cluster story by showing which frequent symptoms tend to share similar co-occurrence partners across transactions.

![Symptom Similarity Heatmap (Top 20 by Frequency) — Jaccard Similarity](assets/jaccard_similarity.png)

Why this matters:
- Rules explain local “if–then” relationships.
- Similarity structure explains global behavior: symptoms that repeatedly appear with similar symptom sets will form visible clusters.

**2) Bipartite symptom–disease network (strong rules)**  
This network is optimized for presentation: it quickly shows which symptoms connect strongly to which diseases under the strongest single-symptom rules.

![Bipartite Network of Strong Symptom → Disease Rules (Top 35)](assets/Bipartite_Network.png)

Why this is powerful in a defense/presentation:
- It communicates results visually in seconds (especially to non-technical viewers).
- It highlights whether diseases are linked by shared symptoms (many connections) or dominated by distinctive signature symptoms (few, strong links).

---

## Discussion

### Key Insights

**1) The dataset contains both “general” and “signature” symptoms—and ARM separates them clearly.**  
EDA shows that some symptoms occur very frequently across many diseases (e.g., fever-like or fatigue-like symptoms). These items have high support but often lower lift because they are common everywhere. In contrast, signature symptoms (rare across the full dataset but frequent within a disease) generate rules with very high confidence and lift, making them excellent for explainable pattern discovery.

**2) Symptom → Symptom rules reveal interpretable clusters that match real-world intuition, but remain dataset-grounded.**  
The strongest symptom clusters in the results are not random. For example:
- jaundice-related patterns (yellowing of eyes, yellowish skin, appetite loss),
- gastrointestinal patterns (nausea, vomiting, abdominal pain),
- systemic patterns (high fever with fatigue),
form cohesive structures. This is important because it demonstrates that ARM is discovering consistent structure rather than noise.

**3) The CAR approach (Symptoms → Disease) is effective because of how the dataset is structured.**  
In this dataset, each disease class has 120 records, and many diseases include symptoms that appear in nearly all of those 120 records. With `min_support_sd = 0.02`, the project intentionally focuses on high-coverage disease rules—meaning rules that represent the disease class strongly across its examples. This is why many rules become confidence = 1.0 and support ≈ 0.0244 (120/4920).

**4) Visual evidence strengthens the credibility of the mined rules.**  
The project is not “tables only.” The visuals provide three layers of evidence:
- EDA plots explain why multi-symptom mining is necessary (symptoms overlap and basket size is moderate).
- Similarity heatmaps show cluster structure beyond isolated rules.
- Network graphs communicate symptom–disease connectivity in a way that is easy to present and defend.

### Why Some Rules Look “Too Perfect”
Because the dataset is perfectly balanced (each disease has 120 rows), the baseline probability of any disease is:

- `P(disease) = 120 / 4920 ≈ 0.0244`

So if a rule has **confidence = 1.0** for a disease:

- `lift = 1.0 / 0.0244 ≈ 41`

This is mathematically expected and does not automatically mean the rule is “clinically perfect.” It means: *within this dataset, this symptom combination is highly aligned with that disease label.*  
That is why leverage is useful here: it reports the **absolute improvement beyond chance**, not just a ratio.

### Limitations (Project-Specific and Important)
- **Dataset-driven determinism:** Some diseases have symptom tokens that are nearly exclusive and extremely frequent within the class, producing many confidence=1 rules. This is excellent for explainability in a data-mining project, but it can overstate how “diagnostic” a symptom would be in real practice.
- **Threshold sensitivity:**  
  - Higher min support produces fewer but stronger “core” rules (current setup).  
  - Lower min support would reveal more nuanced disease rules (possibly including Heart attack and Hepatitis D), but would also increase rule volume and the risk of weak/noisy rules.
- **Binary symptom modeling:** Presence/absence ignores severity, timing, and co-morbid context; the dataset is treated as sets, not sequences or temporal signals.
- **No external validation:** Rules are validated against the dataset through counts and metrics, but not against external medical sources (outside the project scope).

### Future Improvements (High-Impact Next Steps)
- **Parameter sweep + reporting:** Show how rule coverage and quality change as min support varies (e.g., 0.01–0.03). This is a strong final-project enhancement because it demonstrates methodological understanding.
- **Rule redundancy pruning:** Remove rules that are logically redundant or dominated by stronger variants to produce a cleaner final rule list.
- **Coverage analysis per disease:** Report how many records in each disease are “covered” by top-k rules (helps explain why some diseases have fewer strong rules).
- **Rule-based classifier evaluation:** Use the CARs as an interpretable classifier and report accuracy/coverage on a train/test split (still explainable, but more rigorous).

---

## Conclusion

This project delivers a complete, presentation-ready Association Rule Mining pipeline for disease–symptom data. By combining:
- rigorous preprocessing,
- Apriori-based frequent itemset mining (bounded to k ≤ 3),
- rule generation for both co-occurrence and class association rules,
- and evidence-driven visuals,

the project demonstrates how ARM can extract interpretable structure from a symptom dataset and communicate results clearly with defensible metrics.

---

## Demo

Add your video link here:
