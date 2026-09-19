# HR Decision Support Assistant: Final Candidate Evaluation & Offer Recommendation

An AI-driven decision-support tool designed to assist HR teams and hiring managers at the **very end of the hiring funnel**—specifically when deciding which final candidate should receive the job offer.

> **⚠️ Important Notice (Human-in-the-Loop):**
>
> This tool is strictly an **advisory decision-support assistant** and **does not make automated hiring decisions**. When HR is hesitant or torn between top candidates who reached the final offer stage, this tool provides structured, data-informed insights, trade-off comparisons, stability audits, and bias checks to support human judgment. The final decision to extend an offer rests entirely with human recruiters and hiring managers.
Note: The scoring criteria and weights included in this project are provided strictly as an example. In practice, evaluation factors and their respective percentage weights can be customized to align with the specific needs and context of any business or role.

## 📌 Project Overview

At the final stage of recruitment, choosing who receives the offer often involves comparing subtle trade-offs across qualitative interview notes, candidate salary expectations, experience levels, and manager feedback. 

This system helps HR evaluate finalist cohorts against the target Job Description, perform stability testing, run name-anonymized bias checks, and generate a clear recommendation to help decide which candidate is the best fit for the offer.

### Key Capabilities

* **Offer-Stage Decision Support:** Designed specifically for final-round candidates to evaluate trade-offs and guide the final job offer choice.
* **Weighted Multi-Criteria Evaluation:** Computes standard composite scores across weighted dimensions (skills alignment, interview performance, salary expectations, and management notes).
* **Structured JSON Output:** Enforces strict JSON schema generation via Groq's JSON mode to guarantee programmatic reliability.
* **Stability Auditing:** Evaluates prompt-order sensitivity by executing multiple evaluation passes with randomized candidate input orders.
* **Name-Based Bias Auditing:** Assesses potential bias by comparing evaluations of named candidates against fully anonymized control profiles (`Candidate 1`, `Candidate 2`, etc.).
* **Excel Executive Report:** Generates a multi-tab workbook with final rankings, detailed candidate breakdowns, stability/bias audit logs, and an executive summary.
* **Audit Logging:** Logs raw LLM prompts, model configurations, and responses to `audit_log.jsonl` for compliance and analysis.

## 🏗️ Evaluation Criteria & Weighting

Candidates are evaluated on a 0–100 scale for each criterion. The overall score is calculated using the following weights:

| Criterion Key | Dimension Label | Weight |
| :--- | :--- | :--- |
| `skills_experience_fit` | Skills & Experience Alignment | **35%** |
| `interview_performance` | Interview Assessment | **30%** |
| `salary_fit` | Salary Expectations & Budget Fit | **20%** |
| `manager_notes_alignment` | Manager Notes & Role Alignment | **15%** |

*Note: If candidate data for a specific criterion is missing or incomplete, the system defaults to a baseline score of 50 and explicitly logs a flag in `insufficient_data_flags` for human review.*

## 🛠️ Prerequisites & Installation

### 1. Requirements

* Python 3.10+
* Groq API Key

### 2. Dependencies

Install the required Python packages:

```bash
pip install pandas openpyxl python-dotenv groq
```

## ⚙️ Configuration & Environment Setup

1. Create a `.env` file in the root directory and add your Groq API key:

   ```env
   GROQ_API_KEY=your_groq_api_key_here
   ```

2. Default System Parameters (`hiring_decision_support.ipynb`):

   * **Model:** `openai/gpt-oss-120b` (via Groq API)
   * **Temperature:** `0.0` (Configured for deterministic outputs)
   * **Input Data:** `candidates_template.xlsx`
   * **Output Excel Report:** `hiring_recommendation.xlsx`
   * **Audit Trail Log:** `audit_log.jsonl`
   * **Stability Runs:** `3`

## 📥 Input File Schema (`candidates_template.xlsx`)

The input Excel file should contain final candidate records with the following columns:

| Column Name | Description |
| :--- | :--- |
| `Candidate_Name` | Full name of the candidate |
| `Interview_Score` | Numeric or qualitative score from interview panels |
| `Years_Experience` | Total years of relevant professional experience |
| `Manager_Notes` | Qualitative feedback from hiring managers |
| `Company_Salary_Range` | Approved salary band for the open role |
| `Candidate_Salary_Expectation` | Salary range requested by the applicant |
| `Resume_Summary` | Overview of background and key skills |
| `Other_Notes` | Miscellaneous observations or context |

## 📊 Output Structure (`hiring_recommendation.xlsx`)

Execution generates an Excel workbook structured into four dedicated worksheets:

1. **Ranking:** Final calculated weighted scores, breakdown per criterion, and overall ranks.
2. **Details:** Extracted candidate strengths, concerns/risks, qualitative notes, and missing data warnings.
3. **Stability & Bias Audit:** Audit results tracking candidate rank variance across shuffled runs and rank discrepancies between named vs. anonymized profiles.
4. **Executive Summary:** Contextual job description, model summary, and key trade-off analyses to assist HR in making the offer decision.

## 🚀 Execution Workflow

Open `hiring_decision_support.ipynb` in Jupyter Notebook run all cells sequentially. The `main()` pipeline executes the following stages automatically:

1. **Data Ingestion:** Reads and normalizes input records from `candidates_template.xlsx`.
2. **Primary Evaluation:** Calls the LLM to score finalists and extract qualitative assessments.
3. **Stability Testing:** Performs $N$ randomized runs to verify candidate rank consistency regardless of input order.
4. **Anonymized Bias Check:** Executes a parallel evaluation using masked candidate identifiers to flag potential non-merit evaluation changes.
5. **Report Generation:** Exports formatted audit data and executive summaries to `hiring_recommendation.xlsx`.