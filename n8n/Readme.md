# Automated Candidate Screener (n8n Workflow)

**Import Notes**
1. Import n8n/Automated Candidate Screener.json via Workflows → Import from File.
2. Connect credentials for Google Sheets, Google Drive, and your AI model API (Gemini/Groq).
3. The workflow listens for incoming Gmail messages, extracts CV attachments, analyzes CV–JD fit, and updates the tracking sheet with {match_score, rationale, recommendation}.
3. Ensure your AI node returns valid JSON (per CandidateEvaluation schema).
4. Test by sending a sample email with a CV attachment to the monitored Gmail address, then verify Drive upload and Sheet update.
