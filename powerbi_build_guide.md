# Rebuild this dashboard in Power BI / Tableau (≈20 minutes)

`hr_attrition_dashboard.html` is the working interactive deliverable. If your course requires a native Power BI (`.pbix`) or Tableau file, follow these steps with `data/raw/hr_employee_attrition.xlsx` (sheet `in`).

## 1. Load & prepare (Power Query)
1. **Home → Get data → Excel** → select the file → sheet `in` → **Transform data**.
2. Remove constant columns: `EmployeeCount`, `StandardHours`, `Over18`.
3. Add custom columns:
   - `Attrition Flag` = `if [Attrition] = "Yes" then 1 else 0`
   - `Age Group` = `if [Age] <= 25 then "18-25" else if [Age] <= 35 then "26-35" else if [Age] <= 45 then "36-45" else if [Age] <= 55 then "46-55" else "56+"`
   - `Income Band` = `if [MonthlyIncome] < 3000 then "<3k" else if [MonthlyIncome] < 5000 then "3-5k" else if [MonthlyIncome] < 8000 then "5-8k" else if [MonthlyIncome] < 12000 then "8-12k" else "12k+"`
4. **Close & Apply**.

## 2. DAX measures
```DAX
Headcount       = COUNTROWS ( 'in' )
Leavers         = SUM ( 'in'[Attrition Flag] )
Attrition Rate  = DIVIDE ( [Leavers], [Headcount] )
Avg Income Leavers = CALCULATE ( AVERAGE ( 'in'[MonthlyIncome] ), 'in'[Attrition] = "Yes" )
Avg Tenure Leavers = CALCULATE ( AVERAGE ( 'in'[YearsAtCompany] ), 'in'[Attrition] = "Yes" )
```

## 3. Report page layout
| Visual | Fields |
|---|---|
| 4–5 **Cards** | Headcount, Leavers, Attrition Rate, Avg Income Leavers, Avg Tenure Leavers |
| **Slicers** (dropdown) | Department, Age Group, JobRole, MaritalStatus, OverTime (the dataset has no Region column) |
| **Clustered bar** ×6 | Axis = Department / Age Group / JobRole / OverTime / Income Band / YearsAtCompany (binned); Values = Attrition Rate |
| **Decomposition tree** | Analyze = Attrition Rate; Explain by = OverTime, JobRole, MaritalStatus, Age Group, Income Band |
| **Key influencers** (AI visual) | Analyze = Attrition; Explain by = all other fields → this directly answers *"Why are employees leaving?"* |

## 4. Finishing touches
- Conditional formatting: bars red when `Attrition Rate` > company average.
- Add a text box titled **"Why are employees leaving?"** with the 3–4 findings from the README.
- Publish to Power BI Service or export a PDF, and add screenshots to the repo.

*Tableau:* same steps - connect to the Excel file, create calculated fields `Attrition Flag` / `Age Group`, build bar charts of `AVG([Attrition Flag])`, add filters (Show Filter) to a dashboard.
