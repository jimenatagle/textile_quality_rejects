# Predicting & Explaining Textile Rejects  
**Zero-inflated continuous loss modeling for quality risk analysis**

## Overview
In textile manufacturing, quality losses are typically **rare but costly**: most production runs have no rejects, while a small fraction can result in significant fabric loss. This project develops an **interpretable, decision-oriented modeling pipeline** to analyze textile rejects and support **quality risk prioritization**, rather than focusing solely on point prediction.

The project emphasizes:
- correct statistical treatment of zero-heavy, skewed data  
- separation of defect *occurrence* and *severity* mechanisms  
- actionable insights for process improvement and control  

---

## Problem statement
- **Target:** `Reject Qty` (kg of rejected fabric per production context)
- **Key properties:**
  - zero-inflated (~96% zeros)
  - continuous and right-skewed when nonzero
  - rare but high-impact events
- **Goal:**  
  Identify which **controllable process parameters** and **non-controllable design factors** are most associated with reject risk and loss severity.

---

## Dataset
- Source: *Textile Knitting Dataset* (Kaggle)
- Each row represents a production record with:
  - machine configuration
  - fabric structure and composition
  - operational context (shifts)
- Missing values and structural zeros are treated explicitly based on process logic.

---

## Modeling approach

### 1. Baseline and comparison models
Several generalized linear models (GLMs) were evaluated to match different assumptions:
- Linear regression (baseline reference)
- Poisson and Negative Binomial (count-based GLMs)
- Gamma regression (positive continuous outcomes)
- Tweedie regression (zero + positive continuous mixture)

### 2. Two-stage (hurdle) model
To reflect the underlying data-generating process, a **two-stage model** was adopted:
1. **Logistic regression** to model the probability of any reject occurring  
2. **Gamma regression** to model reject severity, conditional on occurrence  

This separation improves interpretability and aligns with real operational decisions:
- *prevention* vs *loss containment*

### 3. Nonlinear benchmark
- XGBoost regression was used as a benchmark to test whether nonlinear interactions or threshold effects could extract additional signal.
- The goal was diagnostic, not model replacement.

---

## Model comparison summary

| Model | Test RMSE ↓ | Tail detection (large rejects) | Risk ranking ability | Interpretability |
|------|-------------|--------------------------------|----------------------|------------------|
| Tweedie GLM | ~1.19 | ❌ None detected | ✅ Good | ✅✅ High |
| Two-stage GLM (Logistic + Gamma) | ~1.18 | ❌ None detected | ✅✅ Very good | ✅✅ High |
| XGBoost Regressor | ~1.27 | ⚠️ Very weak (≈3% recall) | ⚠️ Moderate | ❌ Low |

---

## Key findings & actionable insights

### Drivers of reject occurrence
- **Second fabric loop:** ~2.2× higher odds of a reject  
- **Additional yarn loops:** ~1.7× higher odds  
- **Machine characteristics:** diameter and brand influence reject probability  

Reject occurrence is primarily driven by **fabric structural complexity**, with machine setup acting as a secondary lever.

### Drivers of reject severity
- **Fabric constructions** (Pique, LS/J, Interlock): ~1.9×–2.6× larger losses  
- **Material composition** (polyester and blends): ~1.5×–1.7× vs cotton  
- **Larger machine diameters:** higher loss magnitude once a reject occurs  

Severity depends more on **design and scale effects** than on occurrence triggers.

### Operational takeaway
- Extreme losses are difficult to predict precisely and appear to be driven by **latent factors** not captured in standard process data.
- The primary value of modeling lies in:
  - **risk identification**
  - **process understanding**
  - **prioritization of inspections and controls**

The two-stage GLM provides the best balance between performance, stability, and interpretability for this purpose.

---

## Project structure
- `notebook.ipynb`: full analysis, modeling, and interpretation  
- `README.md`: project overview and conclusions  

---

## Tools & methods
- Python, pandas, NumPy  
- statsmodels (GLMs)  
- scikit-learn  
- XGBoost  
- Probabilistic modeling, risk analysis, decision-oriented evaluation 