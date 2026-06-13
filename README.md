
# Real-World Data Cleaning Assignment with Pandas

## 1. Assignment Objective
The goal of this assignment is to take a real-world, "dirty" dataset and engineer a robust data cleaning pipeline using Python and `pandas`. The dataset has **15 intentionally injected data quality problems** across different column types (numeric, categorical, datetime, text, and structural). 

Each problem is addressed sequentially and documented meticulously inside a Jupyter Notebook (`.ipynb`) featuring:
* **Markdown Cells:** Contextualizing the issue, strategy, and reasoning.
* **Code Cells:** Executable pandas transformations solving the issue.
* **Verification Checks:** Code snippets proving the issue has been successfully resolved (e.g., shape checks, value counts, or assertions).

---

## 2. Dataset Overview
The dataset contains corporate employee records containing the following 15 target columns:

| Column Name | Expected Type | Problems Injected / Operational State |
| :--- | :--- | :--- |
| `employee_id` | Integer | Duplicate unique IDs (~499 duplicates) |
| `name` | String | *Clean reference column (No issues)* |
| `age` | Integer | Missing values, invalid entries (`-5`, `200`, `999`), range violations |
| `salary` | Float | Missing values, extreme outliers (`9999999`), numeric as string formatted (`$91118`) |
| `join_date` | Date | Missing values, 5 mixed date formats |
| `department` | String | Missing values, inconsistent labels, spelling errors (`Mrketing`, `Finace`) |
| `gender` | String | Missing values, empty strings, inconsistent casing/labels (`Male/male/M/Man`) |
| `country` | String | Missing values, inconsistent naming conventions (`USA/U.S.A/us/America`) |
| `city` | String | Missing values, spelling errors (`Dhka`, `Chitagong`, `Slyhet`) |
| `weight_kg` | Float | Missing values, numeric data stored as text string strings (`71kg`, `52kg`) |
| `is_active` | Boolean | Missing values, boolean fields stored as strings (`True/False/1/0` mixed) |
| `review` | String | Missing values, noisy sparse text (`ok`, `bad`, `na`, `...`, `fine`) |
| `price` | Float | Missing values, negative prices, numeric as string formatted (`$219.18`) |
| `weight_kg_duplicate` | None | Schema defect — entire column is `NULL` |
| `target` | Integer | Severe class imbalance (95% = `0`, 5% = `1`) |

---

## 3. Assignment Tasks & Execution Summary

The cleaning workflow is mapped directly to the following 15 tasks executed step-by-step in the Jupyter Notebook:

### Task 01: Missing Values (NaN, Empty Strings)
* **Strategy:** Identified missing entries using `df.isnull().sum()`. Imputed numerical markers (`age`, `salary`, `price`, `weight_kg`) with their column medians. Categorical descriptors (`department`, `gender`, `country`, `city`) were backfilled using their mode. Structured text reviews default to `'No Review'`, and missing indicator flags (`is_active`) default strictly to `False`.

### Task 02: Duplicate Rows
* **Strategy:** Extracted and viewed full duplicate rows using boolean indexing (`df[df.duplicated()]`). Dropped roughly ~300 exact duplicates using `df.drop_duplicates(inplace=True)` and verified structural reduction via `df.shape`.

### Task 03: Duplicate IDs
* **Strategy:** Addressed duplicate identification index values (`employee_id`). Dropped the duplicate identifiers while retaining original instances utilizing `df.drop_duplicates(subset=['employee_id'], keep='first')` to maintain database-level entry integrity.

### Task 04: Wrong Date Formats
* **Strategy:** Parsed mixed format strings (`YYYY-MM-DD`, `DD/MM/YYYY`, etc.) dynamically into uniform Timestamp instances with `pd.to_datetime(..., errors='coerce')`. Coerced exceptions (`NaT`) were handled via median imputation before standardizing outputs to ISO `YYYY-MM-DD`.

### Task 05: Numeric Stored as String
* **Strategy:** Sanitized object columns containing non-numeric remnants (removing characters like `$`, `kg`). Substituted strings using regex methods and systematically typecast array blocks using `.astype(float)` and `pd.to_numeric()`.

### Task 06: Inconsistent Labels
* **Strategy:** Leveraged structural string formatting methods (`.str.lower()`, `.str.strip()`, `.str.title()`) to eliminate noise, then mapped disparate target strings (like matching `us` or `America` directly back to `United States`).

### Task 07: Spelling Mistakes
* **Strategy:** Configured categorical mapping translation keys using specialized dictionary objects to instantly catch and normalize standard typos (e.g., transforming `Dhka` $\rightarrow$ `Dhaka`, `Mrketing` $\rightarrow$ `Marketing`) with `df.replace()`.

### Task 08: Outliers
* **Strategy:** Calculated Interquartile Ranges ($IQR = Q_3 - Q_1$). Established strict upper and lower bounds to intercept errors (such as a structural `9999999` salary injection) and applied statistical capping boundaries via `df.clip(lower, upper)`.

### Task 09: Invalid Values
* **Strategy:** Implemented masking constraints to flag unreasonable contextual boundaries (e.g., isolating operational age thresholds outside of 18–65 or negative prices). Set targets to `np.nan` before re-imputing valid entries via median distributions.

### Task 10: Noisy Text (Reviews)
* **Strategy:** Created a noise-filtering array tracking structurally hollow placeholders (`'ok'`, `'na'`, `'...'`). Evaluated column weights via text length vectors and normalized string gaps to `'No Review'`.

### Task 11: Boolean as String
* **Strategy:** Tracked literal text entries (`'True'`, `'1'`, `1`) by validating truthy status values against an established boolean checklist or custom lambda expression mappings. Cast final evaluations directly to structural boolean data types.

### Task 12: Data Type Issues
* **Strategy:** Validated the schema profile of the final active data layout using `df.dtypes`. Explicitly locked properties down to their proper programmatic datatypes (`int`, `float`, `datetime64`, `bool`).

### Task 13: Range Violations
* **Strategy:** Evaluated the overall data ranges against business constraints (Age: 18-65, Salary: 15k-500k). Used `.clip()` or logical evaluation vectors to resolve boundaries and confirmed corrections with `df.describe()`.

### Task 14: Class Imbalance
* **Strategy:** Highlighted severe skew (95% vs 5%) using statistical distributions (`value_counts(normalize=True)`). Documented programmatic remediation options, such as `RandomOverSampler` from `imblearn` or configuring downstream modeling parameters to `class_weight='balanced'`.

### Task 15: Schema / Column Validation
* **Strategy:** Evaluated macro-level null weights across columns using `df.isnull().mean()`. Dropped fully broken columns (like `weight_kg_duplicate`) and ran automated validation scripts to ensure output layouts match expected schemas.

---

## 4. Setup and Installation

### Prerequisites
Make sure you have Python installed on your machine. You will need `pandas`, `numpy`, `matplotlib`, and `imbalanced-learn`.

### Installation Steps
1. **Clone the repository:**
   ```bash
   git clone [https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git](https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git)
   cd YOUR_REPO_NAME
