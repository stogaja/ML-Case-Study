# Explanation Notes for Presentation

## Detailed Reasoning Behind Key Decisions

### 1. Problem Framing

**Why F1-Score as Primary Metric?**
- Class imbalance (70.5% Lost, 29.5% Won) makes accuracy misleading
- A model that always predicts "Lost" would achieve 70.5% accuracy but be useless
- F1-Score balances precision and recall, penalizing models that ignore the minority class
- Business stakeholders need both: high precision (reliable "Won" predictions) and high recall (don't miss opportunities)

**Why Not ROC-AUC?**
- ROC-AUC is excellent for model comparison but less interpretable for business users
- F1-Score directly answers: "How good are my predictions?"
- We report both, but F1-Score is primary

---

### 2. Data Leakage Prevention

**RD Weight Exclusion - Detailed Reasoning:**
- Data dictionary explicitly states: "Internal weighting factor used to estimate the win probability"
- This is NOT a feature - it's already a prediction tool
- Using it would be like using the answer key to predict the answer
- Even if correlation is not perfect, it contains information that wouldn't be available at prediction time
- **Decision**: Exclude immediately, no further investigation needed

**Stage — CONFIRMED LEAKAGE (exclude):**
- Exploration revealed Stage contains **"Won"** as a category value
- Win rate by Stage: Won 99.4%, Closing 43.8%, Develop/Prepare/Propose 0%
- The target (Status) is literally embedded in the feature — Stage = "Won" means outcome is known
- **Decision**: EXCLUDE — add to COLS_TO_EXCLUDE in feature engineering

**Margin & Turnover Investigation:**
- Both missing **only for Lost** (9 missing Lost, 0 missing Won) — strong leakage signal
- Suggests these may be filled only after winning
- **Decision**: Keep for now (low correlation with Status), but consider excluding or using only missingness indicator if concerned

**General Leakage Detection Strategy:**
1. Read data dictionary carefully
2. Check correlation with target (>0.7 is suspicious)
3. Check missingness patterns (missing only for one class = suspicious)
4. Apply business logic: "Would I know this before the decision?"

---

### 3. Feature Engineering Choices

**Target Encoding for High-Cardinality IDs:**
- Problem: One-hot encoding RegionID, MainCountryID, MainDonorID would create 100+ binary features
- Solution: Target encoding (mean target value per category)
- Why it works: Captures relationship between ID and outcome without high dimensionality
- Smoothing: Blended with global mean to prevent overfitting on rare categories
- **Trade-off**: Less interpretable than one-hot, but prevents curse of dimensionality

**Date Feature Extraction:**
- Avoided absolute dates (could cause temporal leakage if test set is from future)
- Extracted: year, month, quarter, day of week
- Why: Captures seasonal patterns, trends, but avoids absolute time
- Example: "Q4 opportunities" might have different win rates than "Q1 opportunities"

**Missing Value Indicators:**
- Missingness itself can be informative
- Example: Missing Co-Financing might correlate with Lost opportunities
- Created binary indicators: "Has_CoFinancing" (1 if present, 0 if missing)
- Then imputed missing values with median
- **Why both?** Indicator captures pattern, imputation allows model to use actual values when present

**Text Field Processing:**
- Working Areas and Sustainability Criteria are multi-label (comma/semicolon separated)
- Created count features: number of areas/criteria
- Why not full NLP? Time constraint + small dataset = simple approach better
- Could improve: Extract specific areas/criteria as binary features if time permits

---

### 4. Model Selection Rationale

**Logistic Regression (Baseline):**
- Pros: Highly interpretable (coefficients), fast, good baseline
- Cons: Assumes linear relationships
- Used: StandardScaler (required for LR), class_weight='balanced'

**Random Forest:**
- Pros: Handles non-linear relationships, feature importance, robust to outliers
- Cons: Less interpretable than LR, can overfit
- Used: Limited max_depth=10, min_samples_split=20 to prevent overfitting
- Feature importance: Shows which features matter most

**Gradient Boosting:**
- Pros: Often best performance, handles complex patterns
- Cons: More prone to overfitting, less interpretable
- Used: Limited depth=5, learning_rate=0.1, min_samples_split=20
- **Observed**: Severe overfitting — Train F1 0.99 vs CV F1 0.78. Random Forest (CV 0.80) generalizes better

**Model Selection Criteria:**
1. Cross-validation F1-Score (primary)
2. Training performance (secondary)
3. Interpretability (important for business)
4. Overfitting risk (monitor train vs CV gap)

**Why Not Deep Learning?**
- Small dataset (1,226 samples) → high overfitting risk
- Time constraint (3 hours) → simpler models faster to tune
- Interpretability requirement → tree-based models better

---

### 5. Evaluation & Interpretation

**Confusion Matrix Interpretation:**
- True Positives (TP): Correctly predicted Won
- True Negatives (TN): Correctly predicted Lost
- False Positives (FP): Predicted Won but actually Lost (wasted resources)
- False Negatives (FN): Predicted Lost but actually Won (missed opportunity)
- Business impact: Both errors costly, but understanding breakdown helps prioritize

**Feature Importance Interpretation:**
- For Random Forest/Gradient Boosting: Shows which features contribute most to predictions
- For Logistic Regression: Coefficients show direction and magnitude
- Top features: Translate to business insights
  - Example: "MainDonorID_encoded high importance" → "Certain donors have higher win rates"

**Precision vs Recall Trade-off:**
- High Precision: Few false positives → reliable "Won" predictions
- High Recall: Few false negatives → catch most opportunities
- F1-Score balances both
- Can adjust threshold if business prefers precision or recall

---

### 6. Limitations & Future Work

**Current Limitations:**
1. Small dataset → limited model complexity
2. Class imbalance → requires careful handling
3. Time constraint → limited hyperparameter tuning
4. No external data → only using provided features

**Potential Improvements:**
1. **More Data**: Collect more historical opportunities
2. **Feature Engineering**: 
   - Interaction features (e.g., Turnover × Duration)
   - Domain-specific features (e.g., donor-country combinations)
3. **Hyperparameter Tuning**: Grid search or Bayesian optimization
4. **Ensemble Methods**: Combine multiple models
5. **External Data**: Market trends, economic indicators
6. **Text Mining**: Extract specific working areas/criteria as features

**Production Considerations:**
- Model monitoring: Track prediction accuracy over time
- Retraining: Schedule regular retraining as new data arrives
- Feature drift: Monitor if feature distributions change
- A/B testing: Test model impact on business decisions

---

## Where AI Assisted

**AI Tools Used:**
- Code generation for feature engineering pipeline
- Model comparison and evaluation code
- Documentation and explanation generation

**Human Decisions Made:**
- Leakage detection (RD Weight exclusion; Stage exclusion — contains "Won")
- Feature engineering strategy (target encoding, date features)
- Model selection criteria (F1-Score, interpretability)
- Business interpretation of results

**Collaboration Approach:**
- AI provided code structure and implementation
- Human provided domain knowledge and business context
- Together: Production-ready solution with clear reasoning
