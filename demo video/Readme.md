## 🎥 Demo — Automated Candidate Screener

This demo showcases the n8n workflow in action. When a new email with a CV attachment arrives in Gmail, the workflow:

- Detects and downloads the CV.

- Sends it (along with the job description) to the connected AI model for evaluation.

- Extracts a match score, rationale, and recommendation from the AI response.

- Updates the Google Sheet and organizes the CV in Google Drive automatically.
