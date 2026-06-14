# Multi-Omics Survival Prediction in NSCLC  
### Early vs. Late Fusion of miRNA, mRNA, and DNA Methylation  

---

##  Overview  
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

The complete analysis is contained in **`YOUR_NOTEBOOK.ipynb`**.  
You can view it directly on GitHub, or open it in Google Colab / Jupyter.

The notebook covers:  
- Data upload and preprocessing (variance filtering, median imputation)  
- Supervised feature selection (ANOVA F‑test, k tuned by cross‑validation)  
- Early fusion classification (Random Forest, Logistic Regression)  
- Early fusion survival analysis (Kaplan‑Meier, Cox PH, Random Survival Forest)  
- Late fusion: per‑omic modelling and prediction averaging  
- Comprehensive results comparison and visualisation  

---

##  Data  

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

## Interpretation  

- **Methylation** is the strongest individual omic layer for survival prediction.  
  The CpG probe `cg05496203` alone produces a highly significant survival split.  
- **Early fusion** gives a slight edge in AUC because it can model cross‑omic interactions, but mRNA features dominate the model (62% of selected features) simply due to their larger number.  
- **Late fusion** ensures equal representation for each omic, but miRNA’s near‑random performance (AUC ~0.49) drags down the average.  
- The **survival models** (Cox, RSF) confirm the risk signal is real and provide individual survival curves – a richer output than a binary classifier.  

Despite the significant survival signal, overall discriminative power is **modest** (AUC ~0.58, C‑index ~0.61), reflecting the inherent difficulty of survival prediction from omics data alone.

---

##  Figures  

All output figures are located in the **`figures/`** folder.  

| Figure | Description |
|--------|-------------|
| `figures/early_k_selection.png` | AUC vs. number of features (grid search) |
| `figures/early_roc.png` | ROC curves – RF vs. LR |
| `figures/early_feature_importance.png` | Gini and permutation importance |
| `figures/early_km.png` | Kaplan‑Meier curves by top feature expression |
| `figures/early_cox.png` | Cox hazard ratios with confidence intervals |
| `figures/early_rsf_survival.png` | RSF predicted and observed survival curves |
| `figures/early_rsf_importance.png` | RSF permutation importance |

Corresponding `late_*` figures are generated after running the late fusion section and saved in the same folder.

---

##  How to Run  

1. Clone the repository or download `YOUR_NOTEBOOK.ipynb`.  
2. Open it in [Google Colab](https://colab.research.google.com) (File → Upload notebook) or local Jupyter.  
3. Run the first code cell to install required packages (`lifelines`, `scikit-survival`).  
4. When prompted, upload the four CSV files (clinical, miRNA, mRNA, methylation).  
5. Execute all cells sequentially — the notebook is self‑contained and fully reproducible.

---

## Dependencies  
All required libraries are installed in the first cell. The main ones are:  
- `pandas`, `numpy`, `matplotlib`, `seaborn`  
- `scikit-learn` (RandomForest, LogisticRegression, preprocessing, metrics)  
- `lifelines` (KaplanMeierFitter, CoxPHFitter)  
- `scikit-survival` (RandomSurvivalForest, concordance index)  

---

## 🏛️ Affiliations  

**[Sarah Salah]**  
*Department of Medical Biochemistry, Faculty of Medicine, Alexandria University, Alexandria, Egypt*  
*Email: s_salaheldeen20@alexmed.edu.eg*  
**[Name]**  
*Department of *  
*Email:*  
**[Name]**  
*Department of *  
*Email:*   
**[Name]**  
*Department of *  
*Email:*  



---

##  How to Cite  

If you use this code, pipeline, or results in your own work, please cite this repository as:

```bibtex
@misc{yourname2025multiomics,
  author = {Your Name},
  title = {Multi-Omics Survival Prediction in NSCLC: Early vs. Late Fusion},
  year = {2025},
  publisher = {GitHub},
  journal = {GitHub Repository},
  howpublished = {\url{https://github.com/YOUR_USERNAME/YOUR_REPO}},
  note = {Accessed: YYYY-MM-DD}
}
