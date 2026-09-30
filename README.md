# Heart Disease Screening Aid

**Team:** _add names_, _add names_, _add names_
**Brief:** DS-05 (Advanced)  ·  **Dataset:** UCI Machine Learning Repository, Heart Disease (Cleveland), 303 patients × 14 columns

## The question
A community clinic wants a screening aid that flags patients who should be referred to a cardiologist (it does not diagnose). **Can routine clinical measurements identify patients likely to have heart disease, with high enough recall to be a safe screening tool?** Target: `target` (positive class = disease). Success metric: **recall ≥ 0.85**, because a missed sick patient is far more dangerous than an extra referral.

## Key results
- **Logistic Regression** is the best model: ROC AUC **0.94** (decision tree 0.83; simple clinical rule recall only 0.80).
- Threshold tuned on training data to **0.32**: test recall **0.94** (33 of 35 sick patients referred, 2 missed), precision 0.72.
- Trade-off shown at 6 thresholds: 0.5 → 5 missed / 6 healthy referred; 0.32 → 2 missed / 13 referred; 0.1 → 0 missed / 21 referred.
- Top 3 warning signs: **narrowed major vessels (ca)**, **no typical chest pain (cp, 73% disease)**, **abnormal thallium scan (thal)**.
- The 2 missed patients looked healthy on those signals but had high ST depression → extra safety rule: refer if oldpeak ≥ 2.
- 5-fold CV recall **0.85 ± 0.10**: the target is met on average, with real uncertainty on 303 patients.

![Recall vs precision as the threshold is lowered](reports/figures/11_threshold_tradeoff.png)
*Recall vs precision as the threshold is lowered*

![Confusion matrices at the tuned thresholds](reports/figures/10_confusion_matrices.png)
*Confusion matrices at the tuned thresholds*

## Approach
1. **Cleaning:** '?' in `ca` (4) and `thal` (2) read as missing and filled with the most frequent value inside the pipeline (training data only); cp, thal, slope, restecg one-hot encoded as categories; medically plausible outliers kept. No rows dropped.
2. **EDA:** 9 charts (class balance, age, max heart rate, chest-pain type, cholesterol, exercise angina, thallium scan, vessels, sex), each with a written insight.
3. **Model:** stratified 75/25 split (`random_state=42`); baseline = simple clinical rule; Logistic Regression and a depth-tuned decision tree; decision threshold chosen on out-of-fold training predictions to reach recall ≥ 0.85; confusion matrices, precision-recall trade-off, PR curve, error analysis, permutation importance, 5-fold CV.

## How to run
```bash
pip install -r requirements.txt
jupyter notebook notebooks/analysis.ipynb
```
Open `notebooks/analysis.ipynb` and choose **Run All** (Kernel → Restart & Run All). The notebook reads `data/heart_disease.csv` and saves the charts to `reports/figures/`.

**Google Colab:** open the notebook in Colab and choose Runtime → Run all; a **Choose Files** button appears, so upload `data/heart_disease.csv`.

## Repository structure
```
ds-05-heart-disease-screening/
├── README.md
├── data/
│   └── heart_disease.csv
├── notebooks/
│   └── analysis.ipynb      # full notebook, outputs visible
├── reports/
│   ├── figures/            # key charts saved with plt.savefig()
│   └── presentation.pdf    # demo-day slides
└── requirements.txt
```

## Limitations
- Only 303 patients from one US hospital in the 1980s; must be re-validated on local patients.
- The strongest features need fluoroscopy and a thallium scan, which a community clinic may not have.
- About two-thirds of patients are men; recall must be checked separately for women.
- **Ethics:** never use it to diagnose, rule out disease or deny anyone care; a doctor always decides.

## AI usage
Claude (Anthropic) was used as an assistant to draft notebook code, chart code and documentation text, and to suggest checks (for example data-quality and leakage checks). Every team member re-ran the notebook, checked each result against the outputs, and can explain every cell and decision in their own words. The team is responsible for the final content.
