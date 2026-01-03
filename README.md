# Explainable Disease–Symptom Pattern Mining with Apriori Association Rules
**CSC172 Data Mining and Analysis Final Project** *Mindanao State University - Iligan Institute of Technology* **Student:** Joshua Radz T. Adlaon, 2022-2534
**Semester:** AY 2025-2026 Sem 1

## Abstract

Symptom–disease datasets represent each case as a **set of observed symptoms** paired with a **disease label**. While this format is simple, it creates a real interpretability challenge: symptoms often overlap across multiple diseases, and a single symptom rarely provides enough evidence on its own. In many projects, the typical response is to build a predictive model, but predictions alone are not always satisfying for stakeholders—especially when the goal is to explain *why* certain outcomes are suggested, or when the audience is non-technical. This is where Association Rule Mining (ARM) becomes valuable: it produces **transparent “if–then” rules** that can be inspected, validated, and discussed as patterns rather than hidden model weights.

This project applies a canonical **Apriori-style mining strategy** to a Kaggle disease–symptom dataset (`dataset.csv`), treating each row as a “basket” of symptoms. The project mines two complementary knowledge views. First, it discovers **Symptom → Symptom** rules to reveal stable co-occurrence patterns—symptom clusters that frequently appear together and can be interpreted as recognizable “signatures” within the dataset. Second, it extends the transaction space with **heterogeneous items** by representing the disease label as a special item (`disease::<DiseaseName>`), enabling the mining of **Class Association Rules (CARs)** in the form **Symptoms → Disease**. This approach stays fully within the ARM framework while directly producing explainable disease-linked rules.

The project emphasizes presentation-ready outputs: a clean preprocessing pipeline, well-defined transaction construction, rule tables ranked by standard metrics (**support, confidence, lift, leverage**), and visuals that summarize key dataset structure and rule behavior. Exploratory Data Analysis (EDA) is used to motivate parameter choices and to interpret why certain rule patterns emerge—especially in a dataset where classes are perfectly balanced. Rather than claiming clinical validity, the project’s contribution is a clear, defensible, and explainable ARM workflow that demonstrates how frequent itemsets and association rules can extract meaningful structure from symptom data in a way that is easy to present and verify.

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

Symptom–disease data is typically recorded as a list of symptoms tied to a disease label, which makes it easy to store but challenging to interpret. In practical analysis, several issues appear immediately:

1. **Symptoms overlap heavily across conditions.** Many symptoms (e.g., fatigue, nausea, fever) occur in multiple diseases, so looking at symptoms individually rarely produces a clear explanation. What matters more is the **combination** of symptoms and which symptom groups frequently appear together.
2. **Pattern discovery is needed before prediction.** Even if a predictive model can output a disease label, users often want to understand the underlying structure of the dataset first:  
   *Which symptoms form consistent clusters? Which symptom pairs tend to co-occur? Which symptom sets strongly align with a particular disease label?*
3. **Black-box prediction is not enough for explainability.** If the goal is to communicate insights to a broader audience (e.g., class evaluation, project presentation, or non-technical stakeholders), opaque predictions are not very useful unless they are supported by transparent evidence. Explainability requires outputs that can be read, checked, and defended.
4. **Noisy symptom text can distort mining results.** Small formatting inconsistencies (underscores, extra spaces, duplicates, missing values) can fragment the item space—causing a single symptom to be treated as multiple different items and weakening the quality of mined patterns.

This project addresses these challenges by applying **Association Rule Mining (ARM)** to solve an explainability-focused problem:

> **How can we extract clear, human-readable symptom patterns that (a) reveal reliable symptom co-occurrence structure and (b) produce interpretable symptom → disease associations, using a defensible Apriori-style mining approach?**

To answer this, the project treats each record as a **transaction (basket)** and mines:
- **Symptom → Symptom rules** that expose co-occurrence signatures, and  
- **Symptoms → Disease rules (Class Association Rules)** by representing the disease label as a special item (`disease::<DiseaseName>`), enabling direct discovery of disease-linked patterns within the ARM framework.

Rather than producing a single prediction, the project produces a library of **transparent rules** with measurable strength—support, confidence, lift, and leverage—so patterns can be explained, ranked, and validated directly against the dataset.

### Objectives

- Build an end-to-end ARM workflow that converts the raw disease–symptom table into a high-quality transaction database suitable for frequent pattern mining.
- Perform systematic **data cleaning and symptom standardization** to ensure item consistency (e.g., whitespace normalization, underscore handling, duplicate removal, empty symptom handling), preventing false item fragmentation.
- Construct two transaction representations to support two complementary analyses:
  - **Symptoms-only transactions** for mining symptom co-occurrence structure (Symptom → Symptom),
  - **Heterogeneous transactions** by appending `disease::<DiseaseName>` to enable **Class Association Rules** (Symptoms → Disease).
- Apply a canonical **Apriori-style frequent itemset mining strategy** (up to 3-itemsets) to balance interpretability, runtime feasibility, and presentation clarity.
- Generate association rules from frequent itemsets and evaluate them using:
  - **Support** (pattern prevalence),
  - **Confidence** (conditional reliability),
  - **Lift** (strength beyond chance),
  - **Leverage** (absolute gain beyond chance; useful when lift is inflated by balanced class prevalence).
- Use **Exploratory Data Analysis** to characterize symptom frequency, transaction sizes, and class distribution, and to justify parameter choices (e.g., minimum support thresholds).
- Produce presentation-ready outputs:
  - ranked rule tables for both rule categories,
  - visual summaries that clearly communicate dataset structure and rule behavior (frequency plots, transaction-length distribution, similarity heatmap, and symptom–disease network visualization).
- Deliver a final project artifact that emphasizes **explainability, transparency, and defensible interpretation**, aligned with the goals of association rule mining.

### Scope and Limitations

**Scope:**
- Mine **frequent symptom combinations** and generate **association rules** using an Apriori-style approach.
- Produce two rule categories aligned with the project goals:
  - **Symptom → Symptom** rules to reveal reliable co-occurrence patterns.
  - **Symptoms → Disease** rules (Class Association Rules) using a heterogeneous item representation (`disease::<DiseaseName>`).
- Evaluate rules using **support, confidence, lift, and leverage**, and present findings through clear visualizations (frequency charts, transaction-size distribution, Jaccard similarity heatmap, and symptom–disease network graph).
- Focus on **interpretability and presentation-ready insights**, emphasizing rules that are easy to explain and verify directly from the dataset.

**Limitations:**
- The dataset is **perfectly class-balanced** (equal rows per disease), which can cause **lift values to appear very large** for high-confidence class rules; this is addressed by also reporting **leverage**.
- Association rules capture **co-occurrence patterns**, not causation; a strong rule does not prove a medical relationship.
- The mined rules reflect **patterns specific to this dataset** and may not generalize to real-world clinical populations or symptom reporting behavior.
- Symptom tokens are treated as **binary presence/absence** (no severity, timing, duration, or patient context), which limits the realism of the patterns.
- Rule quality depends on preprocessing; despite standardization, any remaining inconsistencies in symptom naming can still affect results.


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

### 1) Data Cleaning and Standardization
Notebook: `data_cleaning_standardization.ipynb`

Key preprocessing steps (ARM-critical):
- Trim whitespace and remove accidental formatting artifacts
- Standardize symptom tokens (e.g., converting underscores to spaces for readability)
- Remove empty symptom slots and duplicates within a transaction
- Ensure disease labels are clean/consistent (important for CAR mining)

### 2) Transaction Construction
Notebook: `arm.ipynb`

This project constructs **two transaction views**:

1. **Symptoms-only transactions**  
   Each row becomes a set of symptoms:  
   `{symptom_1, symptom_2, …}`  
   Used to mine Symptom → Symptom rules.

2. **Heterogeneous transactions (Symptoms + Disease-as-Item)**  
   Each row becomes:  
   `{symptom_1, symptom_2, …, disease::<DiseaseName>}`  
   Used to mine **Class Association Rules** (Symptoms → Disease) in a unified ARM framework.

### 3) Apriori Mining Strategy
This project follows the canonical Apriori idea:
- Count **1-itemsets**, **2-itemsets**, and **3-itemsets**
- Apply **minimum support** thresholds to prune infrequent candidates
- Keep itemsets small enough to remain interpretable in presentation

Why up to 3-itemsets?
- Pairs and triples are easy to explain (“if these symptoms occur together…”)
- Higher-order itemsets can become difficult to present and may overfit dataset quirks

### 4) Rule Generation
Rules are generated from frequent itemsets:

- From a frequent 2-itemset `{A, B}`:
  - `A → B` and `B → A`
- From a frequent 3-itemset `{A, B, C}`:
  - `{A, B} → C`, `{A, C} → B`, `{B, C} → A`

For Class Association Rules:
- The consequent is constrained to be a **disease item**:  
  `{symptom(s)} → disease::<DiseaseName>`

### 5) Rule Quality Metrics
Rules are evaluated using:

- **Support**: how often X and Y occur together  
- **Confidence**: probability of Y given X  
- **Lift**: improvement over chance (confidence divided by base rate of Y)  
- **Leverage**: absolute improvement over chance  
  (useful when lift is inflated by balanced class distributions)

### 6) Visualization Design
To make the project “presentation-ready,” visuals are designed to answer specific questions:

- What symptoms dominate the dataset?
- How long are typical transactions?
- Which symptoms are structurally similar (occur with similar partners)?
- Which rules are both frequent and reliable?
- How do symptom → disease relationships connect as a network?

### Reproducibility
Run order:
1. `data_cleaning_standardization.ipynb`  
2. `arm.ipynb`

Export figures from `arm.ipynb` into an `images/` folder using the filenames referenced below.

---

## Exploratory Data Analysis

EDA focuses on understanding symptom prevalence and transaction structure before mining rules.

<!-- REQUIRED VISUAL (arm.ipynb): Title = "Disease Class Balance (Rows per Disease)" -->
![Disease Class Balance (Rows per Disease)](assets/disease_class_balance.png)

<!-- REQUIRED VISUAL (arm.ipynb): Title = "Top 15 Symptoms by Frequency" -->
![Top 15 Symptoms by Frequency](assets/top15_symptoms.png)

<!-- REQUIRED VISUAL (arm.ipynb): Title = "Distribution of Symptoms per Transaction" -->
![Distribution of Symptoms per Transaction](assets/distribution_of_symptoms.png)

Key EDA highlights (from the dataset):
- The dataset is **perfectly class-balanced** (120 rows per disease).
- Common symptoms (e.g., fatigue, vomiting, fever) appear in a large share of rows, which explains why some co-occurrence rules become strong.
- Average basket size is ~7–8 symptoms, making ARM feasible and interpretable.

---

## Results

### A) Symptom → Symptom Rules

These rules capture **stable co-occurrence patterns**. Examples (high confidence + meaningful leverage):

| Antecedent | Consequent | Support | Confidence | Lift | Leverage |
|---|---|---:|---:|---:|---:|
| yellowing of eyes | loss of appetite | 0.1598 | 0.9632 | 4.1138 | 0.1209 |
| yellowish skin | abdominal pain | 0.1549 | 0.8355 | 3.9833 | 0.1160 |
| nausea | vomiting | 0.1988 | 0.8534 | 2.1937 | 0.1082 |
| abdominal pain | vomiting | 0.1768 | 0.8430 | 2.1670 | 0.0952 |
| high fever | fatigue | 0.1988 | 0.7181 | 1.8286 | 0.0901 |

Interpretation:
- Some symptom groups form **tight clusters** (e.g., jaundice-related symptoms: yellowing of eyes + yellowish skin + appetite issues).
- These clusters are exactly what ARM is good at: a compact set of rules you can explain clearly.

### B) Symptoms → Disease Rules (Class Association Rules)

Using the heterogeneous transaction approach, the project mines rules that directly connect symptoms to disease labels.

In this dataset, many “signature symptoms” appear exclusively under a single disease class, producing **confidence = 1.0** rules. Sample examples:

| Symptom | Disease | Support | Confidence | Lift |
|---|---|---:|---:|---:|
| increased appetite | Diabetes | 0.0244 | 1.0000 | 41.0 |
| polyuria | Diabetes | 0.0244 | 1.0000 | 41.0 |
| pain behind the eyes | Dengue | 0.0244 | 1.0000 | 41.0 |
| receiving blood transfusion | Hepatitis B | 0.0244 | 1.0000 | 41.0 |
| blood in sputum | Tuberculosis | 0.0244 | 1.0000 | 41.0 |

These rules are valuable for explainability because they are:
- simple to read,
- easy to validate against the dataset,
- strong baselines for interpretable decision logic.

**Required Visual from `arm.ipynb`: rule-quality view**


![Symptoms → Disease Rules: Support vs Confidence (Bubble Size = Leverage)](assets/support_vs_conf.png)

### C) Cross-Pattern Structure (Similarity + Network)

To show structure beyond isolated rules, the project visualizes similarity and connectivity.

**1) Symptom similarity (Jaccard heatmap)**  
This reveals which symptoms “travel together” across transactions, even if you don’t look at rules directly.


![Symptom Similarity Heatmap (Top 20 by Frequency) — Jaccard Similarity](assets/jaccard_similarity.png)

**2) Bipartite network of strong Symptom → Disease rules**  
This communicates relationships quickly in presentations: symptoms connect to diseases through strong edges.


![Bipartite Network of Strong Symptom → Disease Rules (Top 35)](assets/Bipartite_Network.png)

---

## Discussion

### Key Insights
- ARM produces **explainable symptom clusters** that can be discussed without black-box reasoning.
- Class Association Rules reveal **disease signatures**—symptoms (or symptom pairs) that strongly imply a disease label.
- Visual analytics (heatmap + network) make results easier to present than raw rule lists.

### Why Some Rules Look “Too Perfect”
This dataset is **perfectly balanced** (each disease is exactly 120/4920 ≈ 2.44%).  
So when a symptom uniquely identifies a disease and produces confidence = 1.0:

- The base probability of the disease is ~0.0244
- Lift becomes roughly **1 / 0.0244 ≈ 41**

That is mathematically correct for this dataset structure. To avoid misleading interpretation, the project includes **leverage** to show absolute improvement over chance.

### Limitations
- ARM captures correlation patterns, not causation.
- Dataset behavior may differ from real clinical populations.
- Some symptoms may be “dataset-specific signatures,” which can inflate deterministic rules.

### Future Improvements
- Compare Apriori with FP-Growth for scalability.
- Evaluate CARs as a simple rule-based classifier (train/test split).
- Add rule pruning (redundancy removal) and coverage analysis.
- Expand to higher-order itemsets only if interpretability remains strong.

---

## Conclusion

This project demonstrates an end-to-end, presentation-grade Association Rule Mining pipeline for a disease–symptom dataset. It delivers:

- Clean transaction creation from tabular symptom records
- Interpretable Symptom → Symptom association rules
- Class Association Rules (Symptoms → Disease) using heterogeneous items
- Strong, presentation-friendly visuals that explain frequency, structure, rule quality, and network relationships

Overall, the project emphasizes **explainability, clarity, and defensible metrics**, making it well-suited as a final ARM project deliverable.

---

## Demo

Add your video link here:

