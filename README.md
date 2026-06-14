# Multi-Omics Survival Prediction in NSCLC  
### Early vs. Late Fusion of miRNA, mRNA, and DNA Methylation  

---

## Overview  
This project investigates whether integrating three molecular layers — **miRNA**, **mRNA**, and **DNA methylation** — can predict **overall survival (OS)** in patients with non‑small cell lung cancer (NSCLC).  

Two multi‑omics fusion strategies are compared:  

- **Early Fusion**: Concatenate all omics into one matrix before feature selection and modelling.  
- **Late Fusion**: Build separate models per omic, then average the predictions.  

The pipeline includes **classification** (alive vs. deceased) and **time‑to‑event survival analysis** (Kaplan‑Meier, Cox regression, Random Survival Forest).  

All analyses use **453 TCGA NSCLC patients** (LUAD + LUSC) with complete data across all three omic layers.

---

##  Research Questions  

1. Can multi‑omics features predict overall survival in NSCLC?  
2. Which omic layer contributes the most to the survival signal?  
3. Does the fusion strategy affect predictive performance or biomarker discovery?  
4. Are there candidate biomarkers that split patients into distinct risk groups?  
5. Do survival‑aware models (RSF) add value over simple classifiers?  

---

##  Notebook  

The complete analysis is contained in **`survival_analysis.ipynb`**.  
You can view it directly on GitHub, or open it in Google Colab / Jupyter.

The notebook covers:  
- Data upload and preprocessing (variance filtering, median imputation)  
- Supervised feature selection (ANOVA F‑test, k tuned by cross‑validation)  
- Early fusion classification (Random Forest, Logistic Regression)  
- Early fusion survival analysis (Kaplan‑Meier, Cox PH, Random Survival Forest)  
- Late fusion: per‑omic modelling and prediction averaging  
- Comprehensive results comparison and visualisation  

---

## Data  

**Required files (uploaded interactively via `files.upload()`):**  

1. **Clinical data** – `OS_MONTHS` (time) and `OS_STATUS` (0 = alive, 1 = deceased).  
2. **miRNA expression** – RPKM values.  
3. **mRNA expression** – FPKM values of protein‑coding genes.  
4. **DNA methylation** – Beta values.  

All files must have sample IDs in the first column.  
> ⚠️ The data are **not** stored in this repository due to size and TCGA access policies.  
> The notebook prompts you to upload them; instructions are provided inside.

---

##  Methods  

| Step | Technique | Purpose |
|------|-----------|---------|
| Preprocessing | VarianceThreshold (var ≤ 0.05), median imputation | Remove constant features, handle missing values |
| Feature Selection | SelectKBest (ANOVA F‑test), k tuned via GridSearchCV | Keep top *k* features most associated with OS_STATUS |
| Classification | Random Forest (balanced, shallow trees), Logistic Regression (L2, C=0.1) | Predict alive vs. deceased |
| Survival Analysis | Kaplan‑Meier, CoxPH (L2 penalised), Random Survival Forest | Model time‑to‑event and censoring |
| Fusion Comparison | Early (concatenated) vs. Late (averaged per‑omic predictions) | Assess effect of fusion strategy |
| Evaluation | Stratified 5‑fold CV, hold‑out test set (20%), C‑index, log‑rank test | Unbiased performance estimation |

All models use the same hyperparameters (fixed seeds for reproducibility) and strict train/test splitting to prevent data leakage.

---

##  Key Results  

### Classification (Test Set AUC)  
| Model | Early Fusion | Late Fusion |
|-------|-------------|------------|
| Random Forest | 0.566 | 0.564 |
| Logistic Regression | 0.581 | 0.528 |

### Survival Analysis  
| Metric | Value |
|--------|-------|
| RSF C‑index (early fusion) | 0.611 |
| Top feature (early fusion) | **cg05496203** (Methylation) |
| Hazard Ratio (Cox) | 2.66 (p < 0.005) |
| Kaplan‑Meier log‑rank p | < 0.0001 |
| Median OS – High expression | 21.4 months |
| Median OS – Low expression | 26.6 months |

### Omic Composition (Early Fusion, top 40 features)  
- miRNA: 12%  
- mRNA: 62%  
- Methylation: 25%  

---

##  Interpretation  

- **Methylation** is the strongest individual omic layer for survival prediction.  
  The CpG probe `cg05496203` alone produces a highly significant survival split.  
- **Early fusion** gives a slight edge in AUC because it can model cross‑omic interactions, but mRNA features dominate the model (62% of selected features) simply due to their larger number.  
- **Late fusion** ensures equal representation for each omic, but miRNA’s near‑random performance (AUC ~0.49) drags down the average.  
- The **survival models** (Cox, RSF) confirm the risk signal is real and provide individual survival curves – a richer output than a binary classifier.  

Despite the significant survival signal, overall discriminative power is **modest** (AUC ~0.58, C‑index ~0.61), reflecting the inherent difficulty of survival prediction from omics data alone.

---

##  Figures  
The notebook generates and saves the following figures as `.png` files:  
- `early_k_selection.png` – AUC vs. number of features  
- `early_roc.png` – ROC curves (RF vs. LR)  
- `early_feature_importance.png` – Gini & permutation importance  
- `early_km.png` – Kaplan‑Meier curves (top feature)  
- `early_cox.png` – Cox hazard ratios  
- `early_rsf_survival.png` – RSF predicted & observed survival  
- `early_rsf_importance.png` – RSF permutation importance  

(Plus corresponding `late_*` figures after running the late fusion section.)

---

##  How to Run  

1. Clone the repository or download `survival_analysis.ipynb`.  
2. Open it in [Google Colab](https://colab.research.google.com) (File → Upload notebook) or local Jupyter.  
3. Run the first code cell to install required packages (`lifelines`, `scikit-survival`).  
4. When prompted, upload the four CSV files (clinical, miRNA, mRNA, methylation).  
5. Execute all cells sequentially — the notebook is self‑contained and fully reproducible.

---

##  Dependencies  
All required libraries are installed in the first cell. The main ones are:  
- `pandas`, `numpy`, `matplotlib`, `seaborn`  
- `scikit-learn` (RandomForest, LogisticRegression, preprocessing, metrics)  
- `lifelines` (KaplanMeierFitter, CoxPHFitter)  
- `scikit-survival` (RandomSurvivalForest, concordance index)  

---

##  References  
- Khadirnaikar, S., et al. (2023). *Multi‑omics integration identifies novel NSCLC subgroups and an autoencoder‑based classifier.*  
- TCGA Research Network: [https://www.cancer.gov/tcga](https://www.cancer.gov/tcga)  

---

##  License  
This project is open‑source under the [MIT License](LICENSE).  
*(You may add a `LICENSE` file if desired.)*
