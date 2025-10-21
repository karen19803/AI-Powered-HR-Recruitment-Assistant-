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
```markdown
Candidate's resume:
{{ $('Standardize').item.json.text }}

---

# Overview
You are an expert technical recruiter specializing in data analysis, marketing, and software roles. Analyze the candidate resume in relation to the job description and output a JSON object with score and rationale.
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
│   ├── workflow.json
│   └── README.md
├── prompts/                          # AI prompt templates (System + User)
├── schema/                           # Structured JSON Schema for validation
├── sheets/                           # Google Sheets mapping examples
├── docs/                             # Additional documentation (architecture, security, troubleshooting)
├── scripts/                          # Utility scripts for deployment/export
└── .env.example                      # Example environment variables
```

---

## 🚀 Setup & Usage

### 1. **Clone the Repository**
```bash
git clone https://github.com/<your-username>/n8n-cv-screening.git
cd n8n-cv-screening
```

### 2. **Import the Workflow**
In n8n → Workflows → Import → upload `n8n/workflow.json`.

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

## 🧑‍💻 Contributing
Pull requests are welcome! Please format JSON files and update the documentation before committing.

---

## 📄 License
MIT License – feel free to use, modify, and improve.

---

**Maintainer:** [Your Name or Team]  
**Contact:** [your.email@example.com]

---

> 💡 *This repo automates candidate screening — saving recruiters hours while keeping results structured, traceable, and AI‑auditable.*

