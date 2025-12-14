# Association Rule Mining — Medical Symptom Co-Occurrence

## Project Objective
To discover **frequently co-occurring symptoms** using Association Rule Mining.
This project focuses on **pattern discovery**, not disease diagnosis.

## Why this matters
Understanding symptom co-occurrence can:
- Support early clinical triage
- Improve symptom checkers
- Reveal common symptom clusters

## Dataset
Kaggle: Disease Symptom Prediction Dataset
- `dataset.csv` → used for ARM (patient cases)
- Other CSVs → interpretation & context only

## Project Flow
1. Data Cleaning & Standardization
2. Transaction Construction
3. Frequent Itemset Mining (Apriori)
4. Association Rule Generation
5. Rule Filtering (confidence & lift)
6. Interpretation

## How to run
Run notebooks in order:
1. `01_data_cleaning_standardization.ipynb`
2. `02_association_rule_mining.ipynb`