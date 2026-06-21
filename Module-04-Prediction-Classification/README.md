# Module 04 — AI Builder: Prediction & Category Classification


---

## 🎯 Learning Objectives
- Build a prediction model from Dataverse data
- Build a text category classification model
- Use prebuilt sentiment analysis
- Integrate predictions into workflows

---

## 📖 Theory

### Prediction Models
Learns from historical data to predict outcomes for new records.

- **Binary prediction:** Will customer churn? (Yes/No)
- **Multi-class prediction:** Ticket priority (High/Medium/Low)

> **Critical insight:** Prediction models require DATA. Minimum 1,000 records with known outcomes. 10,000+ is better. If you don't have data, you cannot build a model. Start collecting now.

### Data Requirements
- 1,000+ records with a known outcome column
- Balanced classes (avoid 95%/5% split)
- Remove ID columns — no predictive value
- Remove columns causing data leakage

### Category Classification
Assigns text to predefined categories:
- Support tickets → IT / HR / Finance / Facilities
- Feedback → Product / Delivery / Support / Pricing
- Min 10 items/category; recommended 50+

### Sentiment Analysis (Prebuilt — No Training!)
Returns: Positive / Negative / Neutral / Mixed + score 0-1

---

## ✅ Best Practices

1. **Validate data quality FIRST** — garbage in, garbage out
2. **Check class imbalance** — 95% "No churn" makes accuracy meaningless
3. **Use meaningful features** — ask business experts what drives outcomes
4. **Retrain quarterly** — behavior changes, models drift
5. **Log all predictions** — build audit trail in Dataverse
6. **Show confidence to users** — "85% likely to churn" beats just "Churn: Yes"
7. **Combine prediction + business rules** — never act on prediction alone

---

## 🔬 Hands-On Lab — Customer Feedback Analyzer

### Scenario
Automatically detect sentiment and classify feedback into categories (Product/Delivery/Support/Pricing). Alert manager for negative product feedback.

### Phase 1 — Train Category Classification Model

#### Step 1 — Prepare Training CSV
```csv
FeedbackText,Category
"The product quality is excellent",Product
"My delivery took 3 weeks and arrived damaged",Delivery
"Support team was rude",Support
"The price increased 30% with no notice",Pricing
```
(Create 50+ items per category)

#### Step 2 — Create Model
1. AI Builder → **+ New model** → **Category classification**
2. Name: `FeedbackCategoryClassifier`
3. Import CSV → map Text column and Label column
4. **Train** → wait 15-20 min → **Publish**

### Phase 2 — Build Analysis Flow

```
Trigger: New row in CustomerFeedback (Dataverse)

Step 1: AI Builder — Analyze sentiment (prebuilt)
  Text: FeedbackText

Step 2: AI Builder — Classify text (custom model)
  Text: FeedbackText

Step 3: Dataverse — Update row
  Sentiment: from Step 1
  Category: from Step 2
  CategoryConfidence: from Step 2

Step 4: Condition
  If: Sentiment = "Negative" AND Category = "Product" AND Score > 0.8
  YES → Post Teams message to Product Manager with feedback text
```

### Phase 3 — Feedback Submission App

Canvas App with multiline text input + star rating + Submit button:
```powerfx
Patch(CustomerFeedback, Defaults(CustomerFeedback), {
    FeedbackText: txtFeedback.Text,
    Rating: ratingControl.Value,
    SubmittedBy: User().Email,
    SubmittedDate: Now(),
    Status: "Pending Analysis"
})
```

### Test
Submit: "The product stopped working after 2 days"
Expected: Sentiment=Negative, Category=Product, Teams alert sent

---

## 📋 POC Summary

**Project:** Customer Feedback Intelligence System

| Component | Technology |
|---|---|
| Sentiment | AI Builder Prebuilt |
| Classification | AI Builder Custom |
| Alert | Teams notification |
| Reporting | Dataverse → Power BI |

*Next: [Module 05 — Power Automate Integration →](../Module-05-PowerAutomate-Integration/README.md)*
