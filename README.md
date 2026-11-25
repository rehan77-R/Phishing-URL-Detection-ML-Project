# Beyond 99% Accuracy: Phishing URL Detection with Feature Dependence Analysis and Robustness Evaluation

**Course:** Introduction to Machine Learning  
**Instructor:** Muddassir Ahmed  
**Students:** M Rehan (SAP 70151032) and Talha Hanif (SAP 70139938)

## About the project
Many studies report 99% or higher scores for phishing URL detection. This project asks why the scores are so high and how far we can trust them.
We train five models and then test the result with four extra checks:

1. **Ablation:** remove `URLSimilarityIndex` and train again.
2. **URL-only vs page-only features:** can a phishing URL be found before the page is opened?
3. **Robustness:** random split vs a split by `Domain` (unseen websites).
4. **Threshold tuning and PR curve:** a lower threshold to miss fewer phishing URLs.

We also explain why a 99%+ score needs care (data leakage, imbalance, evaluation method).

## Dataset
PhiUSIIL Phishing URL Dataset, UCI id 967, CC BY 4.0.
https://archive.ics.uci.edu/dataset/967/phiusiil+phishing+url+dataset

The label is **inverted: 1 = legitimate, 0 = phishing**. Phishing is the positive class (`pos_label=0`) in all metrics.
See `Dataset/Dataset_Information.txt` for details.

## Folder structure
```
Code/            ML_Project.ipynb, requirements.txt
Report/          ML_Project_Report.pdf
Presentation/    ML_Project_Presentation.pptx
Dataset/         Dataset_Information.txt, PhiUSIIL_Phishing_URL_Dataset.csv
README.md
Links.txt
```

## How to run
**Google Colab:** upload `ML_Project.ipynb`, upload `PhiUSIIL_Phishing_URL_Dataset.csv` to the Colab file panel, then use Runtime > Run all.

**Windows or Linux:**
```
pip install -r Code/requirements.txt
```
Keep the CSV in the `Dataset/` folder, open `Code/ML_Project.ipynb` and run all cells. The notebook finds the CSV by itself and saves result tables in a `results/` folder.
Full run time is long because SVM and KNN are slow (Colab: about 1 to 2 hours, not measured exactly).

Python libraries: pandas, numpy, scikit-learn, matplotlib (versions in `requirements.txt`). Random seed: 42.

## Method in short
- Cleaning: 425 repeated URLs and 808 rows with identical features removed. 234,562 rows remain.
- Stratified 70/30 split (164,193 train, 70,369 test). The scaler is fitted on training data only, inside a Pipeline.
- Baseline: DummyClassifier (majority class).
- Models: Logistic Regression, Decision Tree, Random Forest, SVM, KNN. GridSearchCV with 3-fold CV and F1 of the phishing class. SVM and KNN are tuned on a 30,000-row sample of the training set.
- Metrics: Precision, Recall, F1, PR-AUC, ROC-AUC, confusion matrix.

## Main results (test set, phishing class)
| Model | F1 | Phishing missed | False alarms |
|---|---|---|---|
| Dummy (baseline) | 0.00000 | 29,914 | 0 |
| Logistic Regression | 0.99985 | 9 | 0 |
| Decision Tree | 0.99997 | 2 | 0 |
| Random Forest | 0.99997 | 2 | 0 |
| SVM | 0.99983 | 10 | 0 |
| KNN | 0.99836 | 81 | 17 |

**Ablation (without URLSimilarityIndex), F1 before to after:** Decision Tree 0.99997 to 0.99880, Logistic Regression 0.99985 to 0.99920, Random Forest 0.99997 to 0.99983, SVM 0.99983 to 0.99965, KNN 0.99836 to 0.99732.

**URL-only vs page-only (Random Forest F1):** 22 URL features 0.99992, 21 URL features without URLSimilarityIndex 0.99603, 28 page features 0.99789.

**Domain group split:** Decision Tree F1 1.00000, Logistic Regression F1 0.99989. The scores did not drop on unseen domains.

**Threshold (Random Forest, 21 URL features):** threshold 0.5 gives 158 missed and 79 false alarms. Threshold 0.3 (chosen on training data) gives 129 missed and 151 false alarms.

## Limitations
- All results come from one dataset and mostly one split.
- The URL/page feature grouping is based on feature names.
- We could not verify how `URLSimilarityIndex` is calculated.
- The group-split check used settings tuned on the random split.

## References
The full IEEE reference list is in the report.
