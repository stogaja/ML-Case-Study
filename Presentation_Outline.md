# Presentation Outline: ML Case Study - Predicting Project Opportunity Status

**Time**: 15 minutes presentation + 15 minutes discussion  
**Slides**: 4-5 slides maximum

---

## Slide 1: Problem Statement & Approach (3-4 min)

### Content:
- **Problem**: Binary classification to predict Won/Lost status for project opportunities
- **Dataset**: 1,226 training samples, 164 test samples
- **Challenge**: Class imbalance (70.5% Lost, 29.5% Won)
- **Approach Overview**:
  1. Data exploration & leakage detection
  2. Feature engineering
  3. Model comparison (Logistic Regression, Random Forest, Gradient Boosting)
  4. Evaluation & interpretation

### Key Points:
- Emphasize production-style thinking (avoiding leakage, interpretability)
- Highlight class imbalance challenge
- Mention evaluation metrics chosen (F1-Score, Precision, Recall)

---

## Slide 2: Data Exploration & Leakage Prevention (3-4 min)

### Content:
- **Critical Findings — Leakage**:
  - **RD Weight**: Excluded (correlation 0.71 with Status; explicitly a "win probability estimate")
  - **Stage**: Excluded — contains "Won" as a category (99.4% win rate when Stage=Won); target embedded in feature
  - **Margin/Turnover**: Missing only for Lost — potential leakage; kept but monitored
- **Class Distribution**: 70.5% Lost / 29.5% Won → Used `class_weight='balanced'`
- **Missing Values**: Created indicator features for informative missingness
- **Temporal Check**: Verified date ranges (no temporal leakage)

### Key Points:
- Demonstrate careful analysis of data dictionary
- Show understanding of leakage risks
- Explain why RD Weight was excluded

---

## Slide 3: Feature Engineering Strategy (3-4 min)

### Content:
- **Date Features**: Extracted year, month, quarter (avoided absolute dates)
- **Categorical Encoding**:
  - One-hot encoding for low cardinality (<10 unique values)
  - Target encoding for high-cardinality IDs (RegionID, MainCountryID, MainDonorID)
- **Text Fields**: Created count features for Working Areas and Sustainability Criteria
- **Missing Values**: Median imputation + indicator features
- **Final Feature Count**: 16 features (after train/test column alignment)

### Key Points:
- Justify encoding choices (target encoding prevents high dimensionality)
- Explain why missingness indicators were created
- Show production-ready thinking

---

## Slide 4: Model Selection & Results (3-4 min)

### Content:
- **Models Compared**:
  - Logistic Regression (baseline, interpretable)
  - Random Forest (good performance, feature importance)
  - Gradient Boosting (strong performance)
- **Best Model**: Random Forest with CV F1-Score: 0.80
- **Performance Metrics** (Random Forest):
  - CV F1-Score: 0.80 (±0.05)
  - Train F1-Score: 0.85
  - Precision: 0.79, Recall: 0.93
  - Confusion Matrix visualization
- **Top Features**: MainDonorID_encoded, Working Areas_encoded, Working Areas_count, Working Areas_present, Start_year

### Key Points:
- Show model comparison table
- Explain why chosen model was selected
- Highlight interpretability (feature importance)
- Show confusion matrix for business understanding

---

## Slide 5: Key Insights & Recommendations (2-3 min)

### Content:
- **Key Insights**:
  - Donor matters: MainDonorID is top feature — certain donors have higher win rates
  - Working Areas presence strongly predictive: 67.7% missing for Lost vs 15.2% for Won — documenting working areas correlates with winning
  - Start year and contract value (Turnover, Margin) contribute to predictions
- **Limitations**:
  - Gradient Boosting overfit (Train F1 0.99 vs CV 0.78) — Random Forest chosen for better generalization
  - Test set dates appear malformed (1970) — temporal features may not generalize
- **Next Steps**:
  - Exclude Stage from features (add to COLS_TO_EXCLUDE)
  - Collect more data; consider excluding Turnover/Margin if leakage confirmed

### Key Points:
- Translate technical findings to business language
- Acknowledge limitations honestly
- Provide actionable recommendations

---

## Discussion Points (Be Prepared For):

1. **Why exclude RD Weight and Stage?**
   - RD Weight: Explicitly a "win probability estimate"; correlation 0.71 with Status
   - Stage: Contains "Won" as a category — the target is embedded in the feature (99.4% win rate when Stage=Won)

2. **Why F1-Score over Accuracy?**
   - Class imbalance makes accuracy misleading
   - F1-Score balances precision and recall

3. **Why target encoding for IDs?**
   - Prevents high dimensionality from one-hot encoding
   - Captures relationship between ID and target

4. **How would you improve the model?**
   - More data
   - Feature engineering (e.g., interaction features)
   - Hyperparameter tuning
   - Ensemble methods

5. **Production considerations?**
   - Model monitoring
   - Retraining schedule
   - Feature drift detection
   - A/B testing framework
