# Quick Reference — Power Platform & AI Builder

## AI Builder Model Types

| Model | Prebuilt? | Training Data Needed | Use Case |
|---|---|---|---|
| Document processing | Custom | 5-15 sample docs (50+ recommended) | Invoices, forms, contracts |
| Object detection | Custom | 15 images/tag (50+ recommended) | Inventory, quality control |
| Category classification | Custom | 10 items/category (50+ recommended) | Ticket routing, feedback |
| Prediction | Custom | 1,000+ historical records | Churn, fraud, outcomes |
| Sentiment analysis | Prebuilt | None | Feedback, reviews |
| Invoice processing | Prebuilt | None | Standard invoices |
| Receipt processing | Prebuilt | None | Expense receipts |
| Business card reader | Prebuilt | None | Contact import |
| Text recognition (OCR) | Prebuilt | None | Image to text |
| Prompt (GPT) | Prompt-based | None | Summarize, draft, classify |
| Language detection | Prebuilt | None | Multilingual routing |
| Entity extraction | Prebuilt | None | Names, dates, places from text |

---

## AI Builder Credit Consumption

| Action | Credits |
|---|---|
| Document processing (per page) | 1 |
| Object detection (per image) | 1 |
| Prediction (per row) | 1 |
| Text classification (per item) | 1 |
| Invoice/Receipt processing (per page) | 1 |
| Sentiment analysis (per call) | 1 |
| Prompt — small model | ~2-5 |
| Prompt — GPT-4 class model | ~10-20 |

---

## PowerFx Common Formulas

### Data Operations
```powerfx
Patch(Table, Defaults(Table), {Col: Val})         // Create record
Patch(Table, LookUp(Table, ID=varID), {Col: Val}) // Update record
Remove(Table, LookUp(Table, ID=varID))            // Delete record
Filter(Table, Column = "Value")                   // Filter records
LookUp(Table, ID = varID)                         // Single record
```

### Collections
```powerfx
ClearCollect(colName, Filter(Table, Condition))   // Fresh load
Collect(colName, {Col: Val})                      // Add item
RemoveIf(colName, Condition)                      // Remove items
Clear(colName)                                    // Empty collection
```

### Navigation
```powerfx
Navigate(ScreenName, ScreenTransition.Slide)
Navigate(ScreenName, ScreenTransition.Fade)
Back()                                            // Go back
```

### User & Notifications
```powerfx
User().Email                                      // Current user email
User().FullName                                   // Current user name
Notify("Message", NotificationType.Success)       // Toast notification
Notify("Error!", NotificationType.Error)
```

### Common Logic
```powerfx
If(Condition, TrueValue, FalseValue)
Switch(varValue, "A", actionA, "B", actionB, defaultAction)
IsBlank(Control.Value)                            // Check if empty
IfError(Action, FallbackValue)                    // Error handling
Coalesce(Value1, Value2, DefaultValue)            // First non-blank
CountIf(Collection, Condition)                    // Conditional count
SortByColumns(Collection, "Col", Ascending)       // Sort
```

### Date & Text
```powerfx
Now()                                             // Current datetime
Today()                                           // Current date
Text(Date, "dd/mm/yyyy")                          // Format date
DateAdd(Today(), 7, TimeUnit.Days)                // Add days
Len(txtInput.Text)                                // String length
Left("Hello", 3)                                  // "Hel"
Concatenate(val1, " ", val2)                      // Join strings
```

---

## Naming Convention Reference

| Component | Prefix | Example |
|---|---|---|
| Dataverse Table | cst_ | cst_ITTicket |
| Dataverse Column | cst_ | cst_ticketNumber |
| Screen | scr | scrHome |
| Variable | var | varCurrentUser |
| Collection | col | colTickets |
| Text input | txt | txtSubject |
| Label | lbl | lblTitle |
| Button | btn | btnSubmit |
| Gallery | gal | galItems |
| Dropdown | drp | drpCategory |
| Checkbox | chk | chkUrgent |
| Image | img | imgLogo |
| Icon | ico | icoSearch |
| Form | frm | frmTicketDetail |

---

## Environment Strategy Template

```
{OrgCode}-{AppCode}-DEV   ← build and test
{OrgCode}-{AppCode}-TEST  ← UAT with real data
{OrgCode}-{AppCode}-PROD  ← live users

Examples:
CONTOSO-HRBOT-DEV
CONTOSO-HRBOT-TEST
CONTOSO-HRBOT-PROD
```

## Solution Version Numbering

```
Major.Minor.Patch.Build
1.0.0.0  ← initial release
1.1.0.0  ← new feature
1.1.1.0  ← bug fix
2.0.0.0  ← breaking change
```

---

## Pre-Deployment Checklist

- [ ] All components inside a solution
- [ ] Publisher prefix set correctly
- [ ] Environment variables created for all config values
- [ ] Connection references replace all direct connections
- [ ] Flows tested with non-admin user account
- [ ] Canvas app tested on mobile device
- [ ] AI models published (not just trained)
- [ ] Error handling on all AI Builder flow steps
- [ ] Security roles configured (least privilege)
- [ ] Monitoring / alerting configured
- [ ] Rollback plan documented

---

## Useful Links

| Resource | URL |
|---|---|
| Power Platform Home | make.powerapps.com |
| Power Automate | make.powerautomate.com |
| Power Pages | make.powerpages.microsoft.com |
| AI Builder Docs | learn.microsoft.com/en-us/ai-builder/ |
| Developer Plan (free) | aka.ms/PowerAppsDevPlan |
| ALM Guide | learn.microsoft.com/en-us/power-platform/alm/ |
| PL-100 Cert | learn.microsoft.com/en-us/credentials/certifications/power-platform-app-maker/ |
