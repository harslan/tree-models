# ISOM 839: Tree-Based Models in Prescriptive Analytics
## Instructor Guide & Quick Reference

---

## 📋 LECTURE TIMING GUIDE (120 minutes total)

### Part 1: Foundation (7:40 - 8:25) [45 minutes]

**7:40-7:50 (10 min) - Opening & Motivation**
- Start with the central question: "Does better prediction = better decisions?"
- Show the prescriptive analytics pipeline diagram
- Preview tonight's journey: We'll test this with real optimization results
- **Teaching tip:** Poll students: "Who thinks a 5% improvement in prediction accuracy matters?" Build suspense!

**7:50-8:05 (15 min) - Decision Trees Review**
- Quick concept review (don't dwell too long - they should know this)
- Focus on LIMITATIONS: variance, overfitting, instability
- **Key point:** "Would you trust ONE expert or 100 experts?"
- Run the single decision tree code
- **Teaching tip:** Have students predict the R² before revealing it

**8:05-8:25 (20 min) - Random Forest Deep Dive**
- Explain bootstrap aggregating (bagging) with visual analogy
- Random feature selection - why it matters
- Mathematical intuition: error ∝ 1/√n
- Run the Random Forest code
- **Discussion prompt:** "The improvement looks small (0.85 → 0.90). Is it meaningful?"
- Feature importance visualization
- **Teaching tip:** Ask "What surprises you about these feature importances?"

**8:25-8:30 (5 min) - BREAK**
- Encourage students to think about: "When would you NOT use the most accurate model?"

---

### Part 2: Advanced Models + Integration (8:30 - 9:15) [45 minutes]

**8:30-8:45 (15 min) - Gradient Boosting**
- Core difference from RF: sequential vs. parallel
- The "learning from mistakes" philosophy
- Walk through the iterative algorithm step-by-step
- Hyperparameter discussion: learning_rate trade-offs
- Run the GB code
- **Teaching tip:** Use sports analogy - "First draft vs. multiple revisions"

**8:45-9:05 (20 min) - THE BIG COMPARISON**
- This is the HIGHLIGHT of the lecture - give it time!
- Run all three optimizations (LR, RF, GB)
- Build suspense before revealing revenue results
- **Critical moment:** Show the revenue comparison table
- **Discussion:** "Wait... LR gives 95% of optimal revenue with 10% of the complexity. What would YOU choose?"
- Show the optimal price differences by region
- **Teaching tip:** Have students debate in pairs for 2-3 minutes

**9:05-9:15 (10 min) - The Business Case**
- Walk through ROI calculation
- Emphasize: This is how you sell technical decisions to executives
- The "good enough" principle
- **Discussion:** When is 85% accuracy better than 95% accuracy?

---

### Part 3: Wrap-up & Discussion (9:15 - 9:40) [25 minutes]

**9:15-9:25 (10 min) - Best Practices Framework**
- Present the decision matrix (when to use what)
- Real-world trade-offs: accuracy vs. speed vs. explainability
- **Key message:** "Best model ≠ Most complex model"
- Production considerations: maintenance, drift, stakeholder trust

**9:25-9:40 (15 min) - Q&A and Critical Thinking**
- Open floor for questions
- If time: Discuss 2-3 of the critical thinking scenarios
  - Recommended: Model Selection (Q1) and Explainability (Q2)
- Close with the key takeaway: "Better decisions > Better predictions"

---

## 🎯 LEARNING OBJECTIVES MAPPING

| Learning Objective | Where Addressed | Assessment Method |
|-------------------|----------------|-------------------|
| Understand why ML choice matters for optimization | Part 1 intro + Part 2 comparison | Revenue comparison discussion |
| Implement RF and GBM in Gurobi ML | Part 1 & 2 code cells | Live coding / homework |
| Evaluate prediction-optimization trade-offs | Part 2 business case + Part 3 framework | Critical thinking questions |
| See full pipeline: data → decision | Throughout entire lecture | Business case calculation |

---

## 💡 KEY TEACHING MOMENTS

### Moment 1: The Reveal (8:50 PM)
**Context:** Just finished running all three optimizations

**What to say:**
> "Alright, the moment of truth. We spent all this effort on complex ML models. Did it matter? Let's look at the revenue..."

**Why it works:** Builds tension, then pays off with clear business impact

**Follow-up:** Immediately show the sensitivity analysis - even at 50% of predicted improvement, it's still worth it!

---

### Moment 2: The Counterintuitive Insight (9:05 PM)
**Context:** Students see GB gives highest revenue

**What to say:**
> "Wait - before you all run to implement Gradient Boosting, let me ask you something. What if I told you that in production, we actually use Linear Regression for this problem? Why might that be?"

**Why it works:** Forces students to think beyond accuracy metrics

**Expected student responses:**
- Speed
- Explainability  
- Maintenance
- Stakeholder trust

**Your response:** "Exactly! Context matters more than metrics."

---

### Moment 3: The Ethical Question (9:10 PM)
**Context:** Showing optimal prices vary by region

**What to say:**
> "Look at these prices - Northeast pays $1.85, Midsouth pays $1.45. The model learned this maximizes profit. Is this fair? What if these regions correlate with income or demographics?"

**Why it works:** Connects technical decisions to real-world implications

**Follow-up:** "As data scientists, we have responsibility for HOW our models are used, not just THAT they're accurate."

---

## 📊 EXPECTED STUDENT QUESTIONS & ANSWERS

### Q: "Why not always use Gradient Boosting if it's most accurate?"
**A:** Great question! Three reasons:
1. Diminishing returns (95% → 96% accuracy might only yield 1% more revenue)
2. Computational cost (can't run in real-time)
3. Explainability (try explaining 100 sequential trees to your CFO!)

### Q: "How many trees should we use in Random Forest?"
**A:** Start with 100, it's a good default. More trees = more stable predictions but longer training. You'll see diminishing returns after ~200-300 trees. Always validate on held-out data!

### Q: "Can we use neural networks instead?"
**A:** Technically yes, Gurobi ML supports them. But:
- Tree-based models usually outperform on tabular data
- Trees are more interpretable
- NNs require more hyperparameter tuning
For this avocado problem, GBM is likely your best bet.

### Q: "What if my optimization becomes too slow with 100 trees?"
**A:** Several options:
1. Use fewer, deeper trees (trade-off)
2. Use Linear Regression in production, GBM for offline validation
3. Pre-compute predictions for discrete price points, use lookup table
4. Parallel computing / faster hardware

### Q: "How do you handle seasonality and trends?"
**A:** Notice we have 'year' and 'peak' as features. The models learn these patterns automatically. For more complex seasonality, you could add:
- Fourier features (sin/cos terms)
- Month dummies
- Rolling averages
But don't overengineer - let the trees find patterns!

### Q: "What about prediction uncertainty?"
**A:** Excellent question - we're using point predictions, but reality is uncertain. Advanced approaches:
- Random Forest: use prediction variance across trees
- Quantile regression: predict 25th, 50th, 75th percentiles  
- Stochastic optimization: optimize under uncertainty
- Robust optimization: optimize for worst-case

### Q: "How often should I retrain?"
**A:** Monitor these metrics in production:
- Prediction error trend (is MAE increasing?)
- Distribution shift (are feature distributions changing?)
- Business outcomes (is actual revenue matching predicted?)

Rule of thumb:
- Retrain monthly if data changes gradually
- Retrain weekly if market is volatile
- Retrain immediately if you detect significant drift

---

## 🎓 COMMON MISCONCEPTIONS TO ADDRESS

### Misconception 1: "More complex model = Better business outcome"
**Reality:** Not always! Consider:
- Implementation cost
- Maintenance burden
- Explainability needs
- Diminishing returns

**How to address:** Use the ROI calculation to show that business value is multidimensional

---

### Misconception 2: "Random Forest and Gradient Boosting are basically the same"
**Reality:** Fundamentally different approaches!
- RF: Parallel, independent trees → reduce variance
- GB: Sequential, dependent trees → reduce bias

**How to address:** Use the "committee of experts" vs. "iterative improvement" analogies

---

### Misconception 3: "R² improvement from 0.90 to 0.92 is small"
**Reality:** In prescriptive analytics, small accuracy improvements can mean millions in revenue!

**How to address:** Always connect ML metrics to business outcomes. Show the revenue improvement calculation.

---

### Misconception 4: "Gurobi ML is just for tree models"
**Reality:** Supports many sklearn models: linear, trees, neural nets, SVMs, etc.

**How to address:** Mention briefly, but emphasize trees work best for tabular business data

---

## 🔧 TROUBLESHOOTING GUIDE

### Issue 1: Gurobi optimization fails or runs forever
**Possible causes:**
- NonConvex parameter not set
- Conflicting constraints
- Too many trees (1000+)

**Solutions:**
- Check m.Params.NonConvex = 2
- Reduce number of trees
- Simplify model (fewer constraints)

---

### Issue 2: Random Forest doesn't improve over Decision Tree
**Possible causes:**
- Not enough trees (try n_estimators=100)
- Max_depth too restrictive
- Data is actually linear

**Solutions:**
- Increase n_estimators
- Try max_depth=None
- Check feature importance - are splits meaningful?

---

### Issue 3: Students confused about feature importance
**Common confusion:** "Why is 'price' not the most important feature?"

**Explanation:** 
- Feature importance measures VARIANCE explained
- Price might have consistent effect across all observations
- Regional differences create more variance
- This is OK! Different measures for different purposes.

---

### Issue 4: Optimization gives weird prices (e.g., $0.50 or $3.00)
**Possible causes:**
- Bounds too loose (a_min, a_max)
- Model extrapolating outside training data range
- Optimization exploiting model errors

**Solutions:**
- Set realistic price bounds
- Check: are optimal prices within training data range?
- Add validation: retrain on full dataset

---

## 📝 HOMEWORK / FOLLOW-UP SUGGESTIONS

### Option 1: Model Comparison Report
**Task:** Compare all three models (LR, RF, GB) on a new dataset of your choice
**Requirements:**
- Prediction metrics (R², MAE)
- Optimization results (revenue, prices, allocations)
- Business case analysis (ROI calculation)
- Recommendation with justification

---

### Option 2: Hyperparameter Tuning
**Task:** Use GridSearchCV to find optimal hyperparameters for Random Forest
**Requirements:**
- Test at least 3 values each for n_estimators, max_depth, min_samples_split
- Use 5-fold cross-validation
- Report: Does tuning improve optimization results?
- Analyze: Is the computational cost worth the improvement?

---

### Option 3: Real-World Application
**Task:** Apply this framework to a problem in your industry
**Examples:**
- Retail: product pricing across stores
- Manufacturing: production planning with uncertain demand
- Finance: portfolio allocation with predicted returns
- Healthcare: resource allocation with predicted patient volume

**Requirements:**
- Clearly define decision variables and objective
- Train at least 2 prediction models
- Solve optimization with each model
- Make a business recommendation

---

### Option 4: Ethical Analysis
**Task:** Write a 2-page memo on ethical considerations in prescriptive analytics
**Topics to address:**
- Differential pricing (price discrimination)
- Algorithmic fairness
- Model transparency vs. accuracy trade-off
- Responsibility for model decisions
- When should you NOT deploy an optimization model?

---

## 🎯 KEY QUOTES TO EMPHASIZE

> "Better decisions matter more than better predictions."

> "The best model is the one that stakeholders understand and trust."

> "Production-ready beats Kaggle-winner every time."

> "Good enough fast is better than perfect slow."

> "Always connect metrics to money."

---

## 📚 SUPPLEMENTARY MATERIALS

### For Students Who Want More Depth:

**Mathematical details:**
- Breiman's original Random Forest paper (2001)
- Friedman's Gradient Boosting paper (2001)
- "The Elements of Statistical Learning" Ch. 15

**Practical guides:**
- Gurobi ML documentation: https://gurobi-machinelearning.readthedocs.io/
- Scikit-learn ensemble module: https://scikit-learn.org/stable/modules/ensemble.html

**Advanced topics:**
- XGBoost and LightGBM
- SHAP values for model interpretability
- Stochastic optimization under uncertainty

---

## ⚡ QUICK DECISION TREE (For Live Questions)

```
Student asks: "Which model should I use for [specific problem]?"
    |
    ├─> Real-time decisions needed? → Linear Regression
    ├─> Maximum accuracy critical? → Gradient Boosting  
    ├─> Need to explain to non-technical stakeholders? → Linear Regression or Random Forest
    ├─> Large-scale problem (1000s of decisions)? → Linear Regression
    └─> Moderate scale, non-linear, good computational resources? → Random Forest
```

---

## 🎬 CLOSING REMARKS (9:38 PM)

**What to say:**
> "Tonight we've seen that the journey from data to decisions involves many trade-offs. There's no 'best' model - only the right model for your specific context. As you work on prescriptive analytics problems, always ask yourself three questions:
> 
> 1. Does this model improve business outcomes?
> 2. Can I implement and maintain this in production?  
> 3. Will stakeholders trust and act on this model's recommendations?
>
> If you can answer 'yes' to all three, you've found your optimal model.
>
> See you next week!"

---

## 📊 POST-LECTURE REFLECTION

After class, consider:
- Which concepts seemed to click immediately?
- Where did students struggle?
- Which discussions were most engaging?
- What timing adjustments needed?
- Any technical issues to prevent next time?

---

## 🏆 SUCCESS METRICS

By the end of this lecture, students should be able to:
- [ ] Explain the difference between Random Forest and Gradient Boosting
- [ ] Implement both models using sklearn pipelines
- [ ] Integrate models with Gurobi optimization  
- [ ] Calculate ROI for a model implementation
- [ ] Articulate trade-offs in model selection
- [ ] Defend a model choice to a skeptical stakeholder

---

*"The goal of prescriptive analytics is not to build the fanciest model, but to make the best decision."*

---

## 🎯 EMERGENCY BACKUP PLAN

**If technology fails:**
- Have static screenshots of key results pre-loaded
- Printed handouts of comparison table
- Whiteboard discussion of concepts
- Focus on framework and critical thinking

**If running behind schedule:**
- Skip hyperparameter details
- Compress Gradient Boosting to 10 min
- Focus on the comparison (Part 2) - that's the money shot

**If running ahead:**
- Extended Q&A
- Live coding: students suggest hyperparameters to test
- Deeper dive into one of the critical thinking scenarios

---

END OF TEACHING GUIDE
