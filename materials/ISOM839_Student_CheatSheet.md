# 🌲 Tree-Based Models in Prescriptive Analytics: Student Cheat Sheet
## ISOM 839 - Quick Reference Guide

---

## 🎯 THE BIG PICTURE
```
Better Predictions → Better Decisions → Better Business Outcomes
```

| Model | Prediction R² | Optimal Revenue | When to Use |
|-------|--------------|----------------|-------------|
| **Linear Regression** | ~0.87 | $X.XXM | Need speed & explainability |
| **Random Forest** | ~0.90 | $Y.YYM | Good balance of accuracy & interpretability |
| **Gradient Boosting** | ~0.92 | $Z.ZZM | Maximum accuracy is critical |

---

## 📊 MODEL COMPARISON AT A GLANCE

### Decision Tree (Single) 🌳
- **How it works:** Asks yes/no questions, splits data recursively
- **Pros:** Easy to interpret, handles non-linearity
- **Cons:** High variance, overfits easily, unstable
- **Use case:** Quick exploration, baseline model

### Random Forest 🌲🌲🌲
- **How it works:** Build 100+ trees independently, average predictions
- **Key features:** 
  - Bootstrap sampling (random data subsets)
  - Random feature selection (decorrelates trees)
  - Parallel training (faster)
- **Pros:** Reduces overfitting, stable, good feature importance
- **Cons:** Slower than single tree, harder to interpret than linear
- **Use case:** Non-linear problems, need reliability & interpretability
- **Hyperparameters:**
  ```python
  RandomForestRegressor(
      n_estimators=100,    # More trees = more stable
      max_depth=None,      # How deep trees can grow
      max_features='sqrt'  # Features per split
  )
  ```

### Gradient Boosting 🚀
- **How it works:** Build trees sequentially, each corrects previous errors
- **Key concept:** "Learning from mistakes"
  1. Start with simple prediction
  2. Calculate errors (residuals)
  3. Build tree to predict errors
  4. Update: prediction += learning_rate × error_prediction
  5. Repeat
- **Pros:** Often highest accuracy, captures subtle patterns
- **Cons:** Slower training, sequential (can't parallelize), prone to overfitting
- **Use case:** Need maximum accuracy, have time to tune
- **Hyperparameters:**
  ```python
  GradientBoostingRegressor(
      n_estimators=100,      # Number of boosting rounds
      learning_rate=0.1,     # Smaller = more conservative
      max_depth=3-6          # Shallow trees work best
  )
  ```

---

## 🔧 IMPLEMENTATION CHECKLIST

### Step 1: Prepare Data
```python
# Same preprocessing for all models!
feat_transform = make_column_transformer(
    (OneHotEncoder(drop="first"), ["region"]),
    (StandardScaler(), ["price", "year"]),
    ("passthrough", ["peak"])
)
```

### Step 2: Build Model Pipeline
```python
model = make_pipeline(
    feat_transform,
    RandomForestRegressor(n_estimators=100, random_state=42)
)
model.fit(X_train, y_train)
```

### Step 3: Evaluate Prediction
```python
y_pred = model.predict(X_test)
print(f"R²: {r2_score(y_test, y_pred):.4f}")
print(f"MAE: {mean_absolute_error(y_test, y_pred):.4f}")
```

### Step 4: Integrate with Gurobi
```python
from gurobi_ml import add_predictor_constr

# In your optimization model:
pred_constr = add_predictor_constr(m, model, features, demand_vars)
pred_constr.print_stats()  # Check complexity
```

### Step 5: Solve & Compare
```python
m.Params.NonConvex = 2  # Required for quadratic objective!
m.optimize()
revenue = m.ObjVal
```

---

## ⚖️ THE DECISION FRAMEWORK

### Choose **Linear Regression** if:
- ✅ Relationships are approximately linear
- ✅ Need to explain to executives ("$0.10 price increase → X% demand decrease")
- ✅ Real-time decisions required (< 1 second solve)
- ✅ Limited ML expertise on team
- ✅ Model stability critical

### Choose **Random Forest** if:
- ✅ Clear non-linear relationships
- ✅ Moderate computational resources
- ✅ Need feature importance insights
- ✅ Stakeholders accept some "black box"
- ✅ Medium-scale problem (10-100 decisions)

### Choose **Gradient Boosting** if:
- ✅ Maximum accuracy is paramount
- ✅ Infrequent optimization runs (monthly/quarterly)
- ✅ Strong ML expertise available
- ✅ Business impact of small improvements is huge
- ✅ Can afford longer solve times

---

## 💰 BUSINESS CASE TEMPLATE

```python
# Calculate ROI
weekly_improvement = revenue_new_model - revenue_baseline
annual_improvement = weekly_improvement * 52
implementation_cost = 0.05  # $50K
annual_maintenance = 0.05   # $50K/year

roi = (annual_improvement - implementation_cost - annual_maintenance) / implementation_cost * 100
payback_months = implementation_cost / (annual_improvement / 12)

print(f"ROI: {roi:.1f}%")
print(f"Payback: {payback_months:.1f} months")
```

**Decision rule:** If ROI > 200% and payback < 6 months → Recommend implementation

---

## 🎓 KEY TAKEAWAYS

1. **Better predictions ≠ Better decisions** (context matters!)
2. **Production-ready > Kaggle-winner** (maintenance & trust matter)
3. **Always connect ML metrics to business outcomes** (R² → Revenue)
4. **The "good enough" principle** (perfect is the enemy of good)
5. **Explainability is a feature** (stakeholders need to trust the model)

---

## 📈 FEATURE IMPORTANCE INTERPRETATION

```python
# Extract from trained Random Forest
rf_model = pipeline.named_steps['randomforestregressor']
importance = pd.DataFrame({
    'Feature': feature_names,
    'Importance': rf_model.feature_importances_
}).sort_values('Importance', ascending=False)
```

**What it means:**
- **High importance:** Model relies heavily on this feature
- **Low importance:** Feature doesn't contribute much to predictions
- **Business value:** Focus data collection & quality on important features

---

## 🚨 COMMON PITFALLS

### Pitfall 1: Overfitting
**Symptom:** Great training performance, poor test performance  
**Fix:** Use cross-validation, reduce model complexity, get more data

### Pitfall 2: Extrapolation
**Symptom:** Optimization suggests unrealistic prices  
**Fix:** Set realistic bounds, ensure optimal solution within training range

### Pitfall 3: Ignoring Solve Time
**Symptom:** Model takes 10 minutes to optimize  
**Fix:** Reduce trees, use simpler model, or optimize less frequently

### Pitfall 4: Metric Myopia
**Symptom:** Focused only on R², ignoring business context  
**Fix:** Always calculate business impact (revenue, cost, etc.)

---

## 🔍 DEBUGGING CHECKLIST

**Model won't train:**
- [ ] Check for missing values (df.isnull().sum())
- [ ] Verify data types (df.dtypes)
- [ ] Check for infinite values (df.isin([np.inf, -np.inf]).sum())

**Optimization fails:**
- [ ] NonConvex = 2 set?
- [ ] Constraints feasible? (m.computeIIS())
- [ ] Bounds reasonable?

**Poor predictions:**
- [ ] Trained on right data? (check X.shape, y.shape)
- [ ] Features in right order? (match training order!)
- [ ] Hyperparameters sensible? (not max_depth=1)

**Business results don't make sense:**
- [ ] Units consistent? (millions vs. thousands)
- [ ] Costs included correctly?
- [ ] Constraints reflect reality?

---

## 📚 QUICK REFERENCE: sklearn Commands

```python
# Train-test split
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Model training
from sklearn.ensemble import RandomForestRegressor, GradientBoostingRegressor
model.fit(X_train, y_train)

# Prediction
y_pred = model.predict(X_test)

# Evaluation
from sklearn.metrics import r2_score, mean_absolute_error
r2 = r2_score(y_test, y_pred)
mae = mean_absolute_error(y_test, y_pred)

# Feature importance
importances = model.feature_importances_
```

---

## 🎯 EXAM PREP: KEY CONCEPTS

**You should be able to:**
- ✅ Explain difference between Random Forest and Gradient Boosting
- ✅ Justify model choice given business context
- ✅ Calculate ROI for a model implementation
- ✅ Interpret feature importance for business insights
- ✅ Debug common optimization issues
- ✅ Connect prediction metrics to business outcomes

**Concept check:**
1. **Q:** Why might you use Linear Regression over Random Forest?  
   **A:** Speed, explainability, maintenance, "good enough" accuracy

2. **Q:** What does n_estimators control in Random Forest?  
   **A:** Number of trees (more = more stable, but slower)

3. **Q:** How does Gradient Boosting differ from Random Forest?  
   **A:** Sequential (learns from errors) vs. Parallel (independent)

4. **Q:** What is feature importance used for?  
   **A:** Understand which variables drive predictions; prioritize data quality

5. **Q:** When is high prediction accuracy NOT the goal?  
   **A:** When stakeholders need explainability, speed matters, maintenance exceeds benefit

---

## 💡 PRO TIPS

1. **Start simple:** Try Linear Regression first, add complexity only if justified
2. **Validate properly:** Always use hold-out test set or cross-validation
3. **Monitor in production:** Track prediction error and business outcomes over time
4. **Communicate clearly:** Translate R² to revenue for stakeholders
5. **Document decisions:** Why you chose this model, these hyperparameters
6. **Plan for maintenance:** Models drift, retrain regularly
7. **Consider constraints:** Add business rules to optimization (min/max prices, etc.)
8. **Sensitivity analysis:** Test "what if" scenarios before deployment

---

## 📞 GETTING HELP

**Gurobi ML Documentation:**  
https://gurobi-machinelearning.readthedocs.io/

**Scikit-learn Ensemble Guide:**  
https://scikit-learn.org/stable/modules/ensemble.html

**Stack Overflow Tag:**  
`[scikit-learn] [gurobi]` or `[random-forest]`

**Office Hours:**  
[Your schedule here]

---

**Remember:** The goal is not to build the fanciest model, but to make the best decision! 🎯

---

*Last updated: November 2025*  
*ISOM 839: Prescriptive Analytics*
