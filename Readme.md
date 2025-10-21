# AI-Powered HR Recruitment Assistant
## 🧠 Project Objective
Develop a two-part n8n-based system that automates HR recruitment:
1. Automated Candidate Screener
2. AI-powered HR Chatbot Assistant

## ⚙️ Part 1: Automated Candidate Screener
### Phase 1 – Application Intake and Organization
* Capture applications (CV + data) via a web form.
* Store CVs in Google Drive, organized by job title.
* Maintain a master tracking sheet (e.g., Google Sheets) for candidate info.

### Phase 2 – AI-Powered CV Analysis
* Extract text from CV PDFs.
* Compare CVs with the relevant job description using a generative AI model (e.g., Gemini or GPT).
* Generate a structured evaluation (JSON) containing: Score, Rationale
* Update the tracking sheet automatically.

## 💬 Part 2: HR Assistant Chatbot
### Phase 3 – Building the AI’s Memory

* Build a Retrieval-Augmented Generation (RAG) system using candidate CVs.
* Convert CV text → chunks → embeddings.
* Store embeddings in a vector database (e.g., Pinecone, Weaviate, FAISS).

### Phase 4 – Constructing the AI Agent

Create a chatbot agent that:
* Receives messages from a chat interface.
* Uses the vector database as a “memory tool.”
* Follows professional, data-based rules (system prompt).
