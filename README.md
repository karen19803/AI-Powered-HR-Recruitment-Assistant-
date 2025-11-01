# 🤖 Automated CV Screening Workflow (n8n + Groq + Google Suite)

This repository contains a **fully automated CV screening pipeline** built using **n8n** — integrating Gmail, Google Drive, PDF text extraction, AI-based comparison (Groq LLaMA‑3.3‑70B), and Google Sheets. The goal is to **automatically evaluate candidate resumes** against job descriptions and record structured results for easy shortlisting.

---

## 📋 Workflow Overview

### ⚙️ End-to-End Flow
1. **Gmail Trigger** → Monitors outgoing mail for attachments (candidate resumes).
2. **Download Attachments** → Saves resumes locally for processing.
3. **Upload to Google Drive** → Stores each CV under `/resumes/`.
4. **Download File** → Retrieves the uploaded CV.
5. **Extract from PDF** → Extracts raw text from the CV.
6. **Standardize (Manual Edit)** → Cleans extracted text (optional; to be automated later).
7. **Download Job Description PDF** → Fetches the JD for comparison.
8. **Extract JD Text** → Extracts job description text.
9. **AI Evaluation (Groq LLaMA‑3.3‑70B)** → Compares CV text vs. JD text using a custom prompt and JSON Schema.
10. **Google Sheets (Append/Update Row)** → Logs results: name, email, job title, match score, rationale, and recommendation.

---

## 🧠 AI Evaluation Details

**Model:** `llama-3.3-70b-versatile` (via Groq)

**Prompt Template:**
***Prompt (User Message):***
```markdown
Candidate's resume:
{{ $('Standardize').item.json.text }}
```

***System Message:***
```markdown
# Overview

You are an expert technical recruiter specializing in data analysis, Marketing, and software roles. You have been given a job description and a candidate resume. Your task is to analyze the resume in relation to the job description and provide a consistent JSON object containing a score and a brief rationale

Focus specifically on how well the candidate matches the core requirements and ideal profile outlined in the job description. Evaluate both technical skill alignment and business-context understanding. Use reasoning grounded in the actual content of the resume and job post – avoid making assumptions.

## Output

Your output should follow this exact format:

- Overall Fit Rating (0%–100%):
Assign a number between 0% (terrible match) and 100% (perfect match). Do not give decimals.

Justification for Rating (rationale):
Explain clearly why this candidate received that score. Reference specific resume content and how it aligns or doesn’t with the job description.

## Job description:
{{ $json.text }}
```

**Input Schema (structured JSON):**
```json
{
  "title": "CandidateEvaluation",
  "type": "object",
  "properties": {
    "candidate_name": {
      "type": "string",
      "description": "Full name of the candidate as extracted or provided."
    },
    "email": {
      "type": "string",
      "description": "Candidate email if available."
    },
    "job_title": {
      "type": "string",
      "description": "Job title or role the candidate applied for."
    },
    "match_score": {
      "type": "integer",
      "minimum": 0,
      "maximum": 100,
      "description": "Overall numerical match score between CV and job description (0%–100%)."
    },
    "rationale": {
      "type": "string",
      "description": "Brief reasoning or summary explaining the match score."
    },
    "recommendation": {
      "type": "string",
      "enum": ["Strong Match", "Consider", "Weak Match", "Reject"],
      "description": "AI’s final recommendation based on overall fit."
    },
  "required": ["candidate_name", "job_title", "match_score", "rationale", "recommendation"]
  }
}
```

**Output Schema (structured JSON):**
```json
{
  "candidate_name": "string",
  "email": "string",
  "job_title": "string",
  "match_score": 0,
  "rationale": "string",
  "recommendation": "Strong Match | Consider | Weak Match | Reject"
}
```

**Scoring Logic:**
- **90–100%:** Strong Match → Resume directly fits JD.
- **70–89%:** Consider → Close fit, may need follow-up.
- **40–69%:** Weak Match → Partial skill overlap.
- **0–39%:** Reject → Minimal or no alignment.

---

## 🗂️ Repository Structure
```
├── README.md                        # Main documentation (this file)
├── n8n/                              # Exported workflow & import notes
│   ├── Automated Candidate Screener.json
│   └── README.md
├── sheets/                           # Google Sheets mapping examples
└── demo-video/                       # video illustrating the entire work flow
```

---

## 🚀 Setup & Usage

### 1. **Clone the Repository**
```bash
git clone https://github.com/<your-username>/n8n-cv-screening.git
cd n8n-cv-screening
```

### 2. **Import the Workflow**
In n8n → Workflows → Import → upload `n8n/Automated Candidate Screener.json`.

### 3. **Configure Credentials**
Add credentials for:
- Gmail (read + send + attachment access)
- Google Drive (file upload/download)
- Google Sheets (append/update rows)
- Groq API Key (for AI node)

### 4. **Set Up the Google Sheet**
Create a sheet with columns:
```
Full Name | Email | Job Title | CV Upload | Score | Rationale | Recommendation
```
Map them in the Google Sheets node as shown in `/sheets/mapping.md`.

### 5. **Test the Flow**
Send yourself an email with a PDF resume attached and watch the sheet auto-populate.

---

## 🧩 Optional Improvements
- Replace the manual *Standardize* node with an automated Function node to clean CV text.
- Add a JSON validator node to ensure Groq outputs match schema.
- Add a deduplication step for repeated CV uploads.
- Trigger review email to recruiter for top matches (>85%).

---


> 💡 *This repo automates candidate screening — saving recruiters hours while keeping results structured, traceable, and AI‑auditable.*

