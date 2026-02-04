# Swisscontact ML Case Study: Predicting Project Opportunity Status

## Overview

This repository contains a complete solution for predicting whether a project opportunity will be Won or Lost using historical data.

**Problem Type**: Binary Classification  
**Target**: Status (Won/Lost)  
**Dataset**: 1,226 training samples, 164 test samples  
**Time Limit**: 3 hours

---

## Repository Structure

```
├── ml_case_study_solution.ipynb    # Main Jupyter notebook (complete solution)
├── ml_case_study_script.py          # Standalone Python script (reproducible)
├── predictions.csv                  # Final predictions (generated after running)
├── PHASE1_Problem_Framing.md        # Detailed Phase 1 guide
├── Presentation_Outline.md          # 4-5 slide presentation outline
├── Explanation_Notes.md            # Detailed reasoning behind decisions
└── README.md                        # This file
```

---

## Quick Start

### Option 1: Jupyter Notebook (Recommended)
1. Open `ml_case_study_solution.ipynb`
2. Run all cells sequentially
3. Predictions will be saved to `predictions.csv`

### Option 2: Python Script
```bash
python ml_case_study_script.py
```

**Requirements:**
```bash
pip install pandas numpy scikit-learn matplotlib seaborn openpyxl
```

---

## Solution Phases

### PHASE 1: Problem Framing ✅
- **Problem Type**: Binary classification
- **Primary Metric**: F1-Score (balanced precision/recall)
- **Key Risks Identified**:
  - Data leakage (RD Weight excluded)
  - Class imbalance (70.5% Lost, 29.5% Won)
  - Small dataset (1,226 samples)

### PHASE 2: Data Exploration ✅
- **Dataset Size**: 1,226 training, 164 test
- **Class Distribution**: 70.5% Lost, 29.5% Won
- **Missing Values**: Analyzed and handled with indicators
- **Leakage Detection**: RD Weight excluded (explicitly a prediction tool)
- **Date Ranges**: Checked for temporal leakage

### PHASE 3: Feature Engineering ✅
- **Date Features**: Extracted year, month, quarter
- **Categorical Encoding**: 
  - One-hot encoding for low cardinality (<10)
  - Target encoding for high-cardinality IDs
- **Text Fields**: Count features for Working Areas and Sustainability Criteria
- **Missing Values**: Median imputation + indicator features
- **Final Features**: [X] features after engineering

### PHASE 4: Modeling ✅
- **Baseline**: Logistic Regression (interpretable)
- **Model 2**: Random Forest (good performance, feature importance)
- **Model 3**: Gradient Boosting (strong performance)
- **Best Model**: Selected based on CV F1-Score
- **Cross-Validation**: 5-fold stratified CV

### PHASE 5: Evaluation ✅
- **Metrics**: F1-Score, Precision, Recall, Confusion Matrix
- **Feature Importance**: Top features identified and interpreted
- **Business Insights**: Translated technical findings to actionable recommendations

### PHASE 6: Final Deliverables ✅
- **predictions.csv**: Test set predictions (opportunityid, predicted_status)
- **Notebook**: Complete reproducible code
- **Presentation**: 4-5 slide outline
- **Documentation**: Explanation notes

---

## Key Decisions & Reasoning

### 1. Data Leakage Prevention
- **RD Weight**: Excluded because data dictionary states it's "used to estimate win probability" - this IS a prediction, not a feature
- **Stage & Margin**: Investigated but kept (monitored during modeling)

### 2. Feature Engineering
- **Target Encoding**: Used for high-cardinality IDs (RegionID, MainCountryID, etc.) to prevent high dimensionality
- **Missing Indicators**: Created binary features for informative missingness
- **Date Features**: Extracted temporal patterns without using absolute dates

### 3. Model Selection
- **Class Imbalance**: Used `class_weight='balanced'` in all models
- **Regularization**: Limited tree depth, min_samples_split to prevent overfitting
- **Evaluation**: Prioritized F1-Score over Accuracy (class imbalance)

### 4. Production Considerations
- **Interpretability**: Chose models with feature importance (Random Forest, Logistic Regression)
- **Reproducibility**: Set random seeds, documented all steps
- **Scalability**: Simple models that can be deployed easily

---

## Results Summary

**Best Model**: [Model Name]  
**CV F1-Score**: [X.XX]  
**Training F1-Score**: [X.XX]  
**Precision**: [X.XX]  
**Recall**: [X.XX]

**Top Features**: [List top 3-5 features]

---

## Presentation

See `Presentation_Outline.md` for the 4-5 slide structure:
1. Problem Statement & Approach
2. Data Exploration & Leakage Prevention
3. Feature Engineering Strategy
4. Model Selection & Results
5. Key Insights & Recommendations

---

## Where AI Assisted

**AI Contributions:**
- Code generation for feature engineering pipeline
- Model comparison and evaluation code
- Documentation and explanation generation

**Human Contributions:**
- Leakage detection (RD Weight exclusion)
- Feature engineering strategy decisions
- Model selection criteria
- Business interpretation

**Collaboration**: AI provided implementation, human provided domain knowledge and critical reasoning.

---

## Next Steps (If More Time Available)

1. **Hyperparameter Tuning**: Grid search or Bayesian optimization
2. **Feature Engineering**: Interaction features, domain-specific features
3. **Ensemble Methods**: Combine multiple models
4. **Text Mining**: Extract specific working areas/criteria
5. **External Data**: Market trends, economic indicators

---

## Contact & Questions

For questions about the solution, refer to:
- `Explanation_Notes.md` for detailed reasoning
- `PHASE1_Problem_Framing.md` for problem framing details
- Code comments in notebook/script for implementation details

---

**Note**: This solution was developed within a 3-hour time constraint, prioritizing:
- Avoiding data leakage
- Clear reasoning and explainability
- Production-ready code
- Business-relevant insights
