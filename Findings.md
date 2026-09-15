# 📊 Project Findings — E-Commerce Return Risk Analytics

## 🎯 Overview

This project investigated whether e-commerce return risk can be prioritised more effectively by considering **financial impact alongside return probability**.

Traditional return-risk approaches primarily answer:

> **How likely is a transaction to be returned?**

The Expected Return Loss (ERL) framework instead addresses:

> **How much financial exposure does a transaction create when both the probability and potential cost of a return are considered?**

The analysis was conducted across **85,187 transactions** and compared ERL-based prioritisation with ranking transactions using return probability alone.

---

# 💰 Expected Return Loss

Expected Return Loss was calculated at transaction level as:

### ERLᵢ = P(returnᵢ) × Σᵣ [P(reasonᵣ | i) × C(reasonᵣ)]

This combines three components:

1. probability that the transaction is returned
2. probability of each potential return reason
3. financial cost associated with each reason

Five return reasons were considered:

- `size_fit`
- `defect`
- `expectation_mismatch`
- `wrong_item`
- `changed_mind`

The resulting ERL score therefore represents **expected financial exposure rather than return likelihood alone**.

---

# 🔀 Finding 1 — Financial Risk Changes Transaction Prioritisation

One of the clearest results was the difference between ranking transactions by return probability and ranking them by ERL.

### Key Result

**80,968 of 85,187 transactions (95.0%) moved by more than 1,000 ranking positions** when financial impact was incorporated.

```text
Return Probability Ranking
          │
          │ + Financial Severity
          ▼
       ERL Ranking

95% of transactions
shifted >1,000 positions
```

### 💡 Interpretation

Return probability alone does not identify the same transactions as a financial-risk framework.

A transaction with moderate return probability can become highly important when the potential cost of the return is substantial.

Conversely, a transaction with relatively high return probability may represent less financial exposure when the expected processing cost is low.

---

# 📈 Finding 2 — ERL Captures More Financial Exposure

The transactions were ranked from highest to lowest risk using two approaches:

- Expected Return Loss
- Return probability only

The percentage of total financial exposure captured at different targeting thresholds was then compared.

![Loss Capture Curve](images/ERL_loss_capture_curve.png)

### Top 10% of Transactions

| Ranking Method | Financial Exposure Captured |
|---|---:|
| **ERL** | **16.6%** |
| Probability only | 12.5% |

ERL therefore captured an additional:

### **4.1 percentage points of financial exposure**

within the same 10% targeting capacity.

### 💡 Interpretation

If a retailer has limited resources and can only investigate or intervene on a small proportion of transactions, prioritising by ERL concentrates more financial exposure within the selected group.

This is particularly relevant for operational environments where intervention capacity is constrained.

---

# 📐 Finding 3 — Greater Concentration of Financial Risk

The concentration of financial exposure was also evaluated using the **Gini coefficient**.

| Ranking | Gini |
|---|---:|
| **ERL** | **0.1601** |
| Probability only | 0.1058 |

This represents a relative improvement of approximately:

### **1.51×**

### 💡 Interpretation

The higher Gini coefficient indicates that ERL produces a stronger concentration of financial exposure toward the highest-ranked transactions.

This supports the loss-capture analysis and suggests that incorporating financial severity improves prioritisation when the objective is identifying costly return exposure.

---

# 🚦 Finding 4 — ERL Creates Distinct Financial Risk Tiers

Transactions were segmented into five risk categories based on ERL.

| Risk Tier | Transactions | Average ERL | Total ERL |
|---|---:|---:|---:|
| 🟢 Low | 42,675 | £1.74 | £74,114.90 |
| 🟡 Medium | 21,528 | £2.28 | £49,060.56 |
| 🟠 High | 12,589 | £2.66 | £33,447.63 |
| 🔴 Very High | 4,250 | £3.14 | £13,340.58 |
| 🚨 Critical | 4,145 | £4.20 | £17,394.31 |

Average ERL increased from:

**£1.74 → £4.20**

between the Low and Critical tiers.

However, observed return rates remained relatively similar across the tiers.

### 💡 Interpretation

This provides an important distinction:

> **Financial risk and return probability are not the same thing.**

The Critical tier is not simply a collection of transactions that are much more likely to be returned.

Instead, these transactions represent greater **expected financial consequences**.

---

# 🔍 Finding 5 — Return Reasons Drive Different Financial Exposure

Expected losses were decomposed by return reason and product type.

![ERL Reason Breakdown](images/ERL_reason_breakdown.png)

Defect-related exposure was the dominant financial driver across many product categories, contributing approximately **50–67% of total ERL** for several product types.

However, the distribution was not uniform across categories.

For example:

- **Outerwear & Coats** showed approximately 65% defect-related exposure.
- **Blazers and Jackets** showed more than 60% size/fit-related exposure.
- **Plus-size products** also showed more than 60% size/fit-related exposure.

### 💡 Interpretation

Different product categories may require different return-reduction strategies.

A category dominated by defect exposure may benefit from interventions related to:

```text
Product Quality
      ↓
Supplier Quality Control
      ↓
Packaging / Fulfilment
```

while a category dominated by size/fit exposure may require:

```text
Sizing Information
      ↓
Fit Guidance
      ↓
Product Information
```

ERL therefore provides information not only about **which transactions matter**, but also about **why financial exposure occurs**.

---

# 💷 Finding 6 — Transaction Value Can Change Risk Priority

Defect-related cost was modelled as:

```text
£15 + (20% × Order Value)
```

This means that financial exposure increases with transaction value.

One example from the analysis involved a **£399.95 Sleep and Lounge transaction**.

Its return probability was only approximately:

**10.8%**

Yet the transaction ranked first by ERL because its potential defect-related exposure was substantially higher.

### 💡 Interpretation

This example demonstrates why probability-only ranking can miss financially important transactions.

```text
Moderate Probability
        +
High Financial Severity
        =
High Expected Financial Risk
```

---

# 🧪 Finding 7 — ERL Is Not a Return Classifier

The project also evaluated the distinction between ERL and return prediction.

![ML Validation](images/ERL_ml_validation.png)

The validation analysis produced:

| Model | AUC |
|---|---:|
| Return Probability | **0.5054** |
| ERL Score | **0.5118** |

Both values are close to random classification performance.

### 💡 Interpretation

ERL should therefore **not be interpreted as an improved classifier for predicting whether an individual transaction will be returned**.

Its purpose is different.

```text
Return Probability
→ Likelihood of return

ERL
→ Expected financial exposure
```

The value of ERL in this project comes from **financial prioritisation**, rather than improved return classification.

---

# ⚠️ Key Limitations

Several limitations are important when interpreting the findings.

### Synthetic Transaction Data

The TheLook dataset is synthetic and produced very similar observed return rates across product categories.

This limited the amount of meaningful variation available to the upstream return-probability model.

### Limited Probability Model Performance

The ST1 models did not achieve the project's predefined **AUC-ROC threshold of 0.80**.

A scenario-based return score anchored to category return averages was therefore used downstream.

The resulting probability range was relatively narrow.

### Return Reason Classification

The ST2 reason-classification model achieved a macro F1 score of approximately **0.737**, but performance varied by return reason.

`changed_mind` and `wrong_item` had particularly limited usable training signal.

### Cost Assumptions

Return-processing costs were derived from published research rather than retailer-specific accounting records.

The resulting ERL values should therefore be interpreted as **analytical estimates rather than actual retailer losses**.

---

# 🚀 Business Implications

The findings suggest that return-management decisions can benefit from separating two questions:

### 1️⃣ Which transactions are likely to be returned?

and

### 2️⃣ Which transactions create the greatest expected financial exposure?

These questions are related but not equivalent.

A financially informed ranking can potentially help businesses allocate limited intervention resources toward transactions where returns would be more costly.

Potential applications include:

- targeted return-prevention interventions
- product-category monitoring
- supplier quality investigation
- size/fit optimisation
- inventory decisions
- financial risk dashboards
- prioritisation of high-exposure transactions

---

# 🔮 Future Development

A production version of the framework would benefit from:

- real retailer transaction data
- clearly defined historical return windows
- stronger transaction-level return models
- retailer-specific processing costs
- more reliable labels for return reasons
- actual intervention outcomes
- controlled intervention experiments
- automated ERL monitoring
- Power BI or Tableau integration

A future implementation could expose ERL through an interactive dashboard where commercial teams can monitor:

```text
Total Expected Loss
        │
        ├── Product Category
        ├── Return Reason
        ├── Risk Tier
        ├── Transaction
        └── Intervention Opportunity
```

---

# 🎯 Overall Conclusion

The central finding of the project is that:

> **Return likelihood alone does not fully represent the financial importance of a potential return.**

Across **85,187 transactions**, introducing financial severity materially changed transaction prioritisation, with **95% of transactions moving by more than 1,000 ranking positions**.

ERL also captured **16.6% of total financial exposure within the top 10% of transactions**, compared with **12.5% for probability-only ranking**, while producing a **1.51× higher Gini coefficient**.

The project therefore demonstrates how combining **probability, reason and financial severity** can transform a conventional risk score into a more business-focused financial prioritisation framework.

---

## 🎓 Academic Context

This analysis was developed as part of the **COMP1884 Group Project** within the MSc Data Science and Its Applications programme at the University of Greenwich.

My primary responsibility was **ST3 — Expected Return Loss Construction**, including methodology, Python implementation, data integration, financial risk analysis, ranking evaluation and visualisation.

---

## 👩🏼‍💻 Author

**Julia Legner**  
MSc Data Science and Its Applications  
University of Greenwich
