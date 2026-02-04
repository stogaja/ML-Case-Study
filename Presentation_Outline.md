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
- **Critical Finding**: RD Weight excluded (explicitly described as "win probability estimate" - data leakage)
- **Investigated**: Stage and Margin (kept but monitored)
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
- **Final Feature Count**: [X] features

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
- **Best Model**: [Model Name] with CV F1-Score: [X.XX]
- **Performance Metrics**:
  - F1-Score: [X.XX]
  - Precision: [X.XX]
  - Recall: [X.XX]
  - Confusion Matrix visualization
- **Top Features**: [List top 3-5 most important features]

### Key Points:
- Show model comparison table
- Explain why chosen model was selected
- Highlight interpretability (feature importance)
- Show confusion matrix for business understanding

---

## Slide 5: Key Insights & Recommendations (2-3 min)

### Content:
- **Key Insights**:
  - [Top 3 insights from feature importance, e.g., "Consortium participation increases win probability"]
  - [Business-relevant finding]
  - [Actionable recommendation]
- **Limitations**:
  - Small dataset → used regularization to prevent overfitting
  - Class imbalance → used balanced class weights
- **Next Steps**:
  - Collect more data for improved performance
  - Monitor Stage and Margin features for potential leakage
  - Consider ensemble methods if more time available

### Key Points:
- Translate technical findings to business language
- Acknowledge limitations honestly
- Provide actionable recommendations

---

## Discussion Points (Be Prepared For):

1. **Why exclude RD Weight?**
   - It's explicitly described as a prediction tool, not a feature
   - High correlation would indicate leakage

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
