
# Employee Attrition Risk & Retention Factor Analysis

**Domain:** Employee Attrition & HR Analytics  
**Author:** Saksham Singh (`2026B01010179`)  
**Branch / Section:** CSE / 12  
**Course:** Data Analyst Training Project (B.Tech First Year)

---

## 1. Business Problem
Unexpected employee resignations across Indian corporate offices disrupt project delivery timelines and increase hiring and onboarding costs. Currently, HR leadership and department managers lack clear visibility into which specific workplace factors—such as weekly overtime hours, job satisfaction, work mode, or department—drive employees toward leaving. This exploratory data analysis identifies key attrition drivers and high-risk employee segments so management can implement targeted retention policies before top talent resigns.

---

## 2. Dataset Overview
* **File Name:** `employee_attrition_data.csv`
* **Source:** Synthetic HR dataset generated for Python EDA practice (Seed: `010179`)
* **Raw Size:** 255 rows × 12 columns
* **Cleaned Size:** 250 unique employee records × 15 columns (after cleaning and feature engineering)
* **Target / Outcome Column:** `Attrition_Risk_Score` (`0.0` to `100.0`)

### Data Dictionary

| Column Name | Data Type | Valid Values / Range | Description |
| :--- | :--- | :--- | :--- |
| `Employee_ID` | String | `EMP1001` – `EMP1250` | Unique employee identifier |
| `Department` | Category | `Engineering`, `Sales`, `Operations`, `HR`, `Finance` | Functional business unit |
| `Office_City` | Category | `Bengaluru`, `Hyderabad`, `Pune`, `Gurugram`, `Chennai` | Primary Indian office location |
| `Work_Mode` | Category | `Hybrid`, `On-site`, `Remote` | Current working arrangement |
| `Performance_Rating` | Category | `Needs Improvement`, `Meets Expectations`, `Exceeds Expectations`, `Outstanding` | Latest annual review rating |
| `Years_At_Company` | Float | `0.5` – `12.0` | Total tenure in the organization (years) |
| `Monthly_Salary_INR` | Integer | `22,000` – `1,65,000` | Gross monthly salary in Indian Rupees (₹) |
| `Weekly_Overtime_Hours` | Float | `0.0` – `25.0` | Average extra hours worked per week |
| `Job_Satisfaction_Score` | Float | `1.0` – `10.0` | Internal employee satisfaction survey score |
| `Commute_Distance_Km` | Float | `1.5` – `42.0` | One-way commute distance (km) |
| `Last_Appraisal_Date` | Date | `2024-11-21` – `2026-11-20` | Date of last formal appraisal (`YYYY-MM-DD`) |
| `Attrition_Risk_Score` | Float | `0.0` – `100.0` | **Outcome:** Composite attrition likelihood index |

---

## 3. Data Cleaning & Feature Engineering

### Data Quality Issues Fixed
| Issue Type | Affected Column(s) | Count | Cleaning Action Taken |
| :--- | :--- | :--- | :--- |
| **Duplicate Rows** | All columns | 5 rows | Removed exact duplicates using `drop_duplicates()` to avoid double-counting. |
| **Missing Values (`NaN`)** | `Monthly_Salary_INR`, `Job_Satisfaction_Score`, `Commute_Distance_Km`, `Work_Mode` | 20 cells | Imputed numeric columns with the **median** and `Work_Mode` with the **mode** (`Hybrid`). |
| **Inconsistent Text** | `Office_City` | 7 values | Standardized formatting using `.str.strip().str.title()` (e.g., `" PUNE "` → `"Pune"`). |
| **Impossible Values** | `Job_Satisfaction_Score` | 2 values | Replaced out-of-range values (`-3.5` and `18.4`) with `NaN` and imputed with median. |

### New Columns Created
1. **`Overtime_Burnout_Index`**: Calculated as `Weekly_Overtime_Hours / Job_Satisfaction_Score` to measure workload strain relative to morale.
2. **`Attrition_Risk_Level`**: Categorizes `Attrition_Risk_Score` into `"Low Risk"` (`< 40`), `"Moderate Risk"` (`40–64.9`), and `"High Risk"` (`>= 65.0`).
3. **`Tenure_Band`**: Groups `Years_At_Company` into `"New Hire (<2 yrs)"`, `"Mid-Tenure (2–5 yrs)"`, `"Experienced (5–8 yrs)"`, and `"Veteran (>8 yrs)"`.

---

## 4. Key Questions & Findings

### Q1. What does `Attrition_Risk_Score` look like overall?
* **Summary:** Across 250 employees, `Attrition_Risk_Score` follows a roughly bell-shaped distribution centered around a **mean of 48.2** and a **median of 48.0** (standard deviation: **16.5**, range: **8.4 to 92.1**).
* ![Attrition Distribution](images/q1_attrition_distribution.png)

### Q2. Do `Weekly_Overtime_Hours` and `Job_Satisfaction_Score` affect `Attrition_Risk_Score`?
* **Summary:** Yes—both are strong primary drivers of attrition risk, whereas `Commute_Distance_Km` (`r = -0.04`) has virtually no impact:
  * **Weekly Overtime (`r = +0.45`):** Employees working `> 10 hrs/week` overtime average a risk score of **57.4**, compared to **43.9** for those working `<= 10 hrs`.
  * **Job Satisfaction (`r = -0.46`):** Employees with satisfaction `< 4.0` average a risk score of **58.6**, compared to **39.2** for employees scoring `>= 7.0`.
* ![Overtime and Satisfaction Correlation](images/q2_overtime_satisfaction_corr.png)

### Q3. Does `Attrition_Risk_Score` differ across categories (`Department`, `Work_Mode`, `Office_City`, `Performance_Rating`)?
* **Summary:**
  * **By Department:** `Sales` (**53.6**) and `Operations` (**51.8**) show the highest average attrition risk, while `Finance` (**42.1**) and `HR` (**43.5**) are the lowest.
  * **By Work Mode:** `On-site` staff experience higher average risk (**51.9**) than `Hybrid` (**47.5**) and `Remote` (**43.2**) employees.
* ![Department and Work Mode Risk](images/q3_department_workmode_risk.png)

### Q4. Which employee records need immediate retention attention (`Attrition_Risk_Score >= 65.0`)?
* **Summary:** **42 employees (16.8% of the workforce)** fall into the **High Attrition Risk (`>= 65.0`)** segment.
  * Over half of this group belongs to **Sales** and **Operations**.
  * High-risk employees average **13.4 overtime hours/week** (vs. **7.4 hrs** for others) and **3.7 job satisfaction** (vs. **5.9** for others).
  * Notably, **15 of these 42 high-risk employees** hold `"Outstanding"` or `"Exceeds Expectations"` ratings, representing critical talent flight risk.
* ![High Risk Segment](images/q4_high_risk_segment.png)

---

## 5. Recommendations for Management
1. **Cap Weekly Overtime in Sales & Operations:** Introduce workload balancing and automated overtime alerts whenever an employee exceeds **10 overtime hours/week** for consecutive weeks.
2. **Conduct Stay Interviews for High-Performing At-Risk Staff:** Prioritize immediate 1-on-1 check-ins and compensation/role reviews for the **15 top-rated performers** currently scoring `>= 65.0` on attrition risk.
3. **Expand Hybrid Flexibility for On-Site Teams:** Where operationally feasible, transition eligible `On-site` roles to a `Hybrid` schedule to lower baseline attrition risk by ~4–5 points.

---

## 6. Project Structure

```text
employee-attrition-analysis/
├── data/
│   ├── raw/
│   │   └── employee_attrition_data.csv
│   └── processed/
│       └── employee_attrition_cleaned.csv
├── notebooks/
│   ├── 01_first_look_inspection.ipynb
│   └── 02_cleaning_and_analysis.ipynb
├── images/
│   ├── q1_attrition_distribution.png
│   ├── q2_overtime_satisfaction_corr.png
│   ├── q3_department_workmode_risk.png
│   └── q4_high_risk_segment.png
├── documents/
│   ├── 2026B01010179_SakshamSingh_SRS.docx
│   └── 2026B01010179_SakshamSingh_PPT.pptx
└── README.md

```

---

## 7. Tools & Technologies Used

* **Language:** Python 3.11+
* **Libraries:** `pandas`, `NumPy`, `Matplotlib`, `Seaborn`
* **Environment:** Jupyter Notebook, VS Code, GitHub

---


