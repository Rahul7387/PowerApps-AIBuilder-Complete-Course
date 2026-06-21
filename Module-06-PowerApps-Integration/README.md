# Module 06 — AI Builder with Power Apps


---

## 🎯 Learning Objectives
- Integrate AI Builder models in Canvas Apps
- Use Prompt Builder (GPT) inside apps
- Build AI-generated summaries and pre-filled forms
- Create an AI-powered IT helpdesk app

---

## 📖 Theory

### AI Builder in Canvas Apps

**Native AI controls (Insert → AI Builder):**
- Business card reader, Form processor, Object detector, Text recognizer, Receipt processor

**Prompt Builder (GPT in Power Platform):**
Reusable AI prompts for summarizing, drafting, classifying, extracting information.
Access: AI Builder → Prompts → + New prompt

### Using Prompts in App Formulas
```powerfx
Set(varSummary, 'MyPrompt'.Predict({InputText: txtContent.Text}).summary)
```

### AI-Powered UX Patterns

| Pattern | How |
|---|---|
| Summarize on demand | Long doc → "Summarize" → 3-bullet AI summary |
| AI-assisted data entry | Photo upload → AI pre-fills form → user reviews |
| Smart search | Natural language → AI interprets → relevant records |
| Draft and refine | User describes → AI drafts → user edits and sends |

---

## ✅ Best Practices

1. **Show loading spinners** — GPT calls take 2-5 seconds; never leave the user wondering
2. **Make AI optional** — always allow users to skip AI and enter manually
3. **Show AI results as editable** — never lock AI output; users must be able to correct
4. **Add "Regenerate" button** — let users retry if they dislike the response
5. **Trigger AI only on button press** — not on every keystroke; saves credits
6. **Log all AI interactions** — store prompt + response in Dataverse for audit
7. **Test on slow connections** — AI calls on mobile can timeout

---

## 🔬 Hands-On Lab — AI-Powered IT Helpdesk App

### Scenario
Employees describe their IT problem in plain text. AI: classifies ticket type, sets priority, drafts a solution, and pre-fills the form — employee just reviews and submits.

### Phase 1 — Create the AI Prompt

1. AI Builder → **Prompts** → **+ New prompt**
2. Name: `IT Ticket Analyzer`
3. System prompt:
```
You are an IT support specialist at Contoso Ltd.
Analyze the following support request and respond ONLY in valid JSON:

Support request: {ProblemDescription}

Return exactly:
{
  "category": "Hardware|Software|Network|Account|Other",
  "priority": "High|Medium|Low",
  "summary": "One sentence summary",
  "suggestedSolution": "2-3 sentence suggested first step",
  "estimatedResolutionTime": "e.g. 1 hour, 1 day"
}

Priority: High=cannot work at all; Medium=significant difficulty; Low=minor inconvenience
```
4. Test with: "My laptop screen went black and won't turn on"
5. Verify JSON output → **Save** → **Publish**

### Phase 2 — Build Canvas App (3 Screens)

#### Screen 1 — Problem Input
```
Label: "Describe your IT problem in plain language"
txtProblem: Multiline text input
  Hint: "e.g. My laptop won't connect to WiFi since this morning"

btnAnalyze: "Analyze & Pre-fill →"
  OnSelect:
    Set(varLoading, true);
    Set(varAIResult, 'IT Ticket Analyzer'.Predict({ProblemDescription: txtProblem.Text}).text);
    Set(varLoading, false);
    Navigate(Screen2, ScreenTransition.Slide)

imgLoading: Loading spinner, Visible: varLoading
```

#### Screen 2 — Review & Submit (AI Pre-filled)
```
txtSummary: Default = ParseJSON(varAIResult).summary
drpCategory: Default = ParseJSON(varAIResult).category
  Items: ["Hardware","Software","Network","Account","Other"]
drpPriority: Default = ParseJSON(varAIResult).priority
  Items: ["High","Medium","Low"]
lblSuggestion: "💡 " & ParseJSON(varAIResult).suggestedSolution
txtAdditional: Additional details (optional)

btnSubmit: "Submit Ticket"
  OnSelect:
    Patch(SupportTickets, Defaults(SupportTickets), {
        ProblemDescription: txtProblem.Text,
        Summary: txtSummary.Text,
        Category: drpCategory.Selected.Value,
        Priority: drpPriority.Selected.Value,
        AISuggestedSolution: ParseJSON(varAIResult).suggestedSolution,
        SubmittedBy: User().Email,
        SubmittedDate: Now(),
        Status: "Open"
    });
    Navigate(Screen3, ScreenTransition.Fade)
```

#### Screen 3 — Confirmation
```
"✅ Ticket Submitted!"
"Ticket: " & Last(SupportTickets).TicketNumber
"Estimated resolution: " & ParseJSON(varAIResult).estimatedResolutionTime
"💡 While you wait: " & ParseJSON(varAIResult).suggestedSolution
Button: "Submit Another" → Navigate(Screen1)
```

### Test with Real Scenarios
1. "I can't log in — account locked" → Account, High
2. "My mouse double-clicks on single click" → Hardware, Low
3. "Teams crashes every time I join a meeting" → Software, Medium
4. "Everyone in my team can't access the shared drive" → Network, High

---

## 📋 POC Summary

**Project:** AI-Powered IT Helpdesk App
**User benefit:** 60% reduction in ticket form completion time

*Next: [Module 07 — Advanced Scenarios →](../Module-07-Advanced-Scenarios/README.md)*
