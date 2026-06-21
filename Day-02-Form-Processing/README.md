# Day 02 — AI Builder: Form Processing

**Duration:** 2 Hours | **Faculty:** Krisnendu Roy

---

## 🎯 Learning Objectives
- Build and train a custom document processing model
- Understand structured vs unstructured documents
- Extract invoice data and store in Dataverse
- Implement confidence score checks

---

## 📖 Theory

### Prebuilt vs Custom Document Processing

| | Prebuilt (Invoice) | Custom Document Processing |
|---|---|---|
| Training needed | No | Yes (min 5 samples) |
| Works on | Standard invoices | Any structured document |
| Flexibility | Fixed fields | You define fields |

> **Trainer insight:** In 90% of enterprise projects, teams start with prebuilt, then discover their forms are non-standard and need custom models. Build custom from Day 1 if your documents vary.

### Document Types
- **Structured** — Fixed layout (tax forms, standard invoices)
- **Semi-structured** — Variable layout, consistent fields (POs, receipts)
- **Unstructured** — No fixed layout (contracts) → use Prompt/GPT instead

### Field Types You Can Label
- Text, Number, Date, Selection mark (checkboxes), Table (rows & columns), Signature

### Training Best Practices
- Minimum 5 samples; recommended **15-20** for good accuracy
- Include **variation** — different vendors, fonts, page counts
- Label **every field in every sample**
- Include documents with some missing fields (real-world has gaps)

---

## ✅ Best Practices — Day 2

1. **Use 15+ sample documents** — 5 is minimum but gives poor accuracy
2. **Include document variations** — different layouts, fonts, formats
3. **Log confidence scores** — if confidence < 80%, route to manual review
4. **Version your models** — keep old version until new one is validated in production
5. **Test with unseen documents** — never test only with training samples
6. **Handle multi-page docs** — test with 2-page and 3-page documents

---

## 🔬 Hands-On Lab — Invoice Extraction to Dataverse

### Scenario
Accounts payable receives 50+ invoices daily from multiple vendors. Build a model to extract: Vendor, Invoice number, Date, Due date, Line items, Total — and store in Dataverse.

### Step 1 — Create Custom Document Processing Model
1. [make.powerapps.com](https://make.powerapps.com) → **AI Builder** → **Models** → **+ New model**
2. Select: **Document processing** → **Get started**

### Step 2 — Define Fields
Add these fields:
| Field | Type |
|---|---|
| VendorName | Text |
| InvoiceNumber | Text |
| InvoiceDate | Date |
| DueDate | Date |
| TotalAmount | Number |
| LineItems | Table (Description, Qty, UnitPrice, LineTotal) |

### Step 3 — Upload and Label 5+ Sample Invoices
For each document:
1. Highlight text → select matching field
2. For tables: use Table mode, draw box around entire table, map columns
3. Label ALL fields on ALL documents

### Step 4 — Train, Test, Publish
1. **Train** → wait 15-30 min
2. **Quick test** with unseen invoice → verify fields and confidence scores
3. Confidence target: > 90% per field
4. **Publish** when satisfied

### Step 5 — Build Power Automate Flow
```
Trigger: File created in SharePoint /Invoices-Inbox

Step 1: Get file content

Step 2: AI Builder — Extract information from documents
  Model: your custom model

Step 3: Condition — confidence > 0.8?
  YES → Dataverse Add row (Invoices table)
  NO → Email AP team for manual review

Step 4: Apply to each (LineItems)
  Dataverse Add row (InvoiceLineItems table)
```

### Step 6 — Test End to End
1. Upload test invoice to SharePoint
2. Check Dataverse Invoices table → verify record created
3. Check InvoiceLineItems → verify each line item row

---

## 📋 POC Summary

**Project:** Invoice Automation System
**Business value:** Eliminates manual data entry for 50+ invoices/day (~12.5 hours/week saved)

*Next: [Day 03 — Object Detection →](../Day-03-Object-Detection-Image-Models/README.md)*
