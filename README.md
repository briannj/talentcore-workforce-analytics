# TalentCore Workforce Analytics
A five-page Power BI dashboard and CRISP-DM analysis of a company's workforce: headcount, performance, attrition, and pay and diversity. I cleaned 1,000 messy HR records down to 243 employees, built a star-schema data model with DAX measures, and turned the findings into recommendations.
<img width="900" alt="Executive Summary page" src="https://github.com/user-attachments/assets/f39e266d-5a8f-4a30-9df7-c6cc07b201bd" />
## Business problem
TalentCore's HR data sits in one spreadsheet with no entry rules, so leaders cannot see where people leave or whether pay is fair. The company has six offices across Kenya, Uganda, Tanzania and Rwanda.
## Questions answered
Where is the workforce concentrated, and how has hiring moved?
Which departments differ on performance, training and overtime?
Where and when do people leave, and which groups are at highest risk?
Is pay fair across gender, department and education?
# Tools
Power BI · Power Query · DAX · Excel · CRISP-DM
# Data
A course HR extract of 1,000 rows and 16 columns. The raw data is not included in this repository.
| Item | Value |
|---|---|
| Raw rows | 1,000 |
| Rows holding only a hire date | 753 |
| Rows with employee data | 247 |
| Unique employees after removing duplicates | 243 |
| Employees who left | 38 |
# Data cleaning (Power Query)
Every step was logged so it can be traced and repeated. Missing or invalid values were left blank, never replaced with zero, so averages ignore them.
| Column | Problem found | Fix | Result |
|---|---|---|---|
| Rows | 753 rows with no Employee ID | Filtered out | 247 rows |
| Employee ID | Stray spaces, mixed case, ".0" suffix, missing hyphen, 4 duplicates | Trim, upper-case, repair; keep earliest hire date | 243 rows |
| Department | 18 spelling variants | Mapped to 6 departments | 6 values |
| Job Role | 45 variants (case and spacing) | Standardised | 24 roles |
| Office Location | 22 variants (typos, abbreviations) | Mapped to 6 offices | 6 values |
| Gender | 8 variants | Standardised | Female 126, Male 117 |
| Age | Text such as "34 yrs"; impossible values 200 and -5 | Kept digits; blank if under 18 or over 70 | 233 valid, 10 blank |
| Education Level | 15 variants | Mapped to 4 levels | 4 values |
| Monthly Salary | 34 text values such as "KES 242,800" | Kept digits only | 235 valid, 8 blank |
| Performance Rating | 26 text values such as "4/5" | Took the first character | 1 to 5; 5 blank |
| Training Hours | Text "N/A", 3 negative values | Numbers only; others blank | 219 valid, 24 blank |
| Overtime | 10 variants (Y/N, TRUE/FALSE, 1/0) | Mapped to Yes / No | Yes 133, No 110 |
| Attrition Status | 9 variants | Standardised to Active / Left | Active 205, Left 38 |
| Exit Date | Real dates, text formats, Excel serial numbers, placeholders | Custom column parsing each form | 35 dates |

Columns added: Exit Type, Tenure (Years), Age Group and Tenure Group.
Decisions: keep the earliest record for duplicate IDs; blank impossible ages; keep leavers with no exit date as "Left" but exclude them from tenure.

# Data model
A star schema: `Employees` (243 rows) is the fact table, joined one-to-many to five dimensions: `Date`, `Department`, `Office`, `Education` and `AgeGroup`. The Hire Date relationship is active. The Exit Date relationship is inactive and switched on inside one measure with `USERELATIONSHIP`, so exits can be trended without a second date table.

Key measures: Total Headcount, Active Headcount, Employees Left, Attrition Rate, Average Monthly Salary, Average Tenure, Average Training Hours, Average Performance Rating, Overtime Rate, Female Share, Gender Pay Gap, Exits by Exit Date.
```
Attrition Rate = DIVIDE(\[Employees Left], \[Total Headcount], 0)

Exits by Exit Date =
CALCULATE(\[Employees Left], USERELATIONSHIP(Date\[Date], Employees\[Exit Date]))
```
# Dashboard
Five pages: Executive Summary, Workforce Overview, Departmental Performance, Attrition Analysis, and Compensation and Diversity.

## Workforce overview
243 employees: 205 active and 38 who have left. Average age is 39.7 and average tenure 3.8 years. Customer Support is the largest department, with 54 people (22%).

## Departmental performance
Customer Support rates lowest (2.47) and Sales highest (3.11), a 0.64-point gap. Operations has the least training (35.1 hours) and joint-highest overtime (64.9%).

## Attrition analysis
Attrition is 15.6%. It is highest in Finance (21.6%) and Customer Support (20.4%) and lowest in Operations (8.1%).

## Compensation and diversity
The overall gender pay gap is about 1%, but it is larger inside some departments.

# Key findings
Attrition is 15.6% (38 of 243), highest in Finance (21.6%) and Customer Support (20.4%), and lowest in Operations (8.1%).

Risk sits at two points: staff with 1 to 3 years of tenure (16.2%) and staff aged 50 and over (26.5%).

Exits are rising: 11 employees left in 2024, against 3 to 6 a year since 2020.

Exit reasons are missing: only 3 of 38 leavers have a recorded reason, so we know who leaves and when, but not why.

Pay is near-equal overall (about 1% gap), but men earn roughly 7 to 9% more in Engineering, Operations and HR, while women earn slightly more in Finance, Sales and Customer Support.

Pay does not rise with education: Master's holders have the lowest average (about KES 139,000).

Operations works the most overtime but has the lowest attrition, so the data does not show overtime driving people out.

# Recommendations
These are proposed actions. The targets are my suggestions, anchored to the baselines in the data, for management to confirm.
| # | Recommendation | Evidence | Proposed target |
|---|---|---|---|
| 1 | Record a reason for every exit with a structured exit interview | Only 3 of 38 leavers have one | 100% of leavers with a reason |
| 2 | Focus retention on Finance and Customer Support: review pay and workload | Attrition 21.6% and 20.4% | Under 15% in both within 12 months |
| 3 | Early-career retention programme; succession planning for staff aged 50+ | Attrition 16.2% at 1 to 3 years, 26.5% at 50+ | 1 to 3 year attrition under 12% |
| 4 | Review pay equity in Engineering, Operations and HR | Men earn about 7 to 9% more | Unexplained gaps under 3% |
| 5 | Rebalance overtime and training in Operations | 64.9% overtime, 35.1 training hours | Overtime under 55%, 40 training hours |
| 6 | Investigate the rise in exits in 2024 | 11 exits, up from 3 to 6 a year | Findings presented within 60 days |

## Limitations
Only 243 employees, so small groups swing percentages.

3 leavers have no exit date and 12 have no usable tenure. One exit is dated 2017, which looks like an entry error.

8 salaries, 5 ratings and 10 ages are missing and were left blank.

2025 data ends mid-year, so 2025 hires and exits are partial.

The analysis shows association, not cause.

## Repository contents
`images/`: dashboard screenshots
`docs/`: CRISP-DM report and presentation (PDF)

## Author
Brian Njoroge
