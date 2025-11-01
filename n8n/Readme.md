# Automated Candidate Screener (n8n Workflow Documentation)
This directory contains the **exported n8n workflow** and setup notes for the automated CV screening pipeline.

---

## **Import Notes:**
1. Import n8n/Automated Candidate Screener.json via Workflows → Import from File.
2. Connect credentials for Google Sheets, Google Drive, and your AI model API (Gemini/Groq).
3. The workflow listens for incoming Gmail messages, extracts CV attachments, analyzes CV–JD fit, and updates the tracking sheet with {match_score, rationale, recommendation}.
3. Ensure your AI node returns valid JSON (per CandidateEvaluation schema).

---

## 🔐 Credentials Setup

After importing, you’ll need to reconnect credentials (they aren’t exported for security reasons):

| Node          | Required Credential | Scopes/Notes                                                     |
| ------------- | ------------------- | ---------------------------------------------------------------- |
| Gmail Trigger | Google OAuth2       | `https://mail.google.com/` access to read & download attachments |
| Google Drive  | Google OAuth2       | Full access to upload/download from `/resumes/` folder           |
| Google Sheets | Google OAuth2       | Access to edit the target sheet                                  |
| AI Node       | Groq API Key        | Model: `llama-3.3-70b-versatile`                                 |

> ⚠️ Make sure each credential is authorized under the correct account. n8n stores credentials securely within your instance.

---

## ⚙️ Node Summary

| Step | Node Type                         | Description                                                                     |
| ---- | --------------------------------- | ------------------------------------------------------------------------------- |
| 1    | **Gmail Trigger**                 | Detects new outgoing emails with PDF attachments.                               |
| 2    | **Download Attachments**          | Saves resumes temporarily.                                                      |
| 3    | **Upload File (Drive)**           | Uploads the resume to Google Drive `/resumes/` folder.                          |
| 4    | **Download File**                 | Retrieves resume for processing.                                                |
| 5    | **Extract from PDF**              | Converts PDF to text.                                                           |
| 6    | **Manual Edit (Standardize)**     | Optional cleanup step for resume text.                                          |
| 7    | **Download JD File**              | Fetches job description PDF.                                                    |
| 8    | **Extract JD Text**               | Converts job description to text.                                               |
| 9    | **AI Agent (Groq)**               | Compares CV text with JD text using structured JSON schema.                     |
| 10   | **Google Sheets (Append/Update)** | Saves final results (name, email, job title, score, rationale, recommendation). |

---

## 🧠 AI Agent Details

**Model:** `llama-3.3-70b-versatile` via Groq

**Prompt type:** Structured output using JSON Schema

Schema reference: `../schema/candidate_evaluation.json`

---

## 🧪 Testing the Workflow

To test end-to-end:

1. Send an email with a PDF resume attached (from an account connected to the Gmail node).
2. The workflow will run automatically.
3. Check the **Execution Log** in n8n to trace each node.
4. Confirm output in your connected Google Sheet.

---

## 🧰 Troubleshooting

| Issue                   | Possible Cause                                   | Fix                                                 |
| ----------------------- | ------------------------------------------------ | --------------------------------------------------- |
| Workflow not triggering | Gmail Trigger not authorized or polling disabled | Reconnect Gmail credentials and enable trigger      |
| PDF extraction empty    | PDF is scanned image                             | Use OCR node or online service                      |
| AI node fails           | Groq API key missing or rate limit hit           | Reconnect credentials or set retry policy           |
| Sheet row not added     | Sheet ID mismatch or wrong mapping               | Verify Sheet ID and column mapping in node settings |

---

> 💡 *This folder makes it easy to restore or migrate the entire workflow between environments — development, staging, or production.*
