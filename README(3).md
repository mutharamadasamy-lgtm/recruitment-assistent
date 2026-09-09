# AI HR Recruitment Assistant

A full-stack final-year project for explainable, human-reviewed recruitment assistance.

## Implemented
- React + Vite responsive HR dashboard
- FastAPI backend with PostgreSQL/SQLAlchemy
- HR registration/login with JWT
- Job-description PDF upload and extraction
- Multiple resume PDF upload and structured extraction
- Candidate matching and explainable weighted scoring
- LangChain tools and configurable LLM integration
- LangGraph recruitment workflow
- RAG knowledge-base structure + ChromaDB ingestion
- Personalized interview-question generation
- Candidate report API
- Fairness rule: protected/sensitive attributes are not used for scoring
- Automated tests for core extraction/scoring

## Architecture
`Job PDF -> extraction -> Job Analysis -> Resume Analysis -> RAG Retrieval -> Candidate Matching -> Transparent Scoring -> Recommendation -> Interview Questions -> Report`

AI output is advisory only. HR must make the final hiring decision.

## Score
Technical Skills 40%, Experience 25%, Education 15%, Projects 10%, Certifications 10%.
80+ SHORTLIST, 60-79.99 REVIEW, below 60 NOT RECOMMENDED.

## PostgreSQL
Create database `ai_hr_recruitment` in PostgreSQL 18 and set `DATABASE_URL` in `.env`. The existing nine tables are preserved.

## Setup
### Backend (Windows PowerShell)
```powershell
cd backend
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```
Copy the root `.env.example` to `.env` and set the PostgreSQL password and JWT secret. Optionally set `OPENAI_API_KEY` for LLM-powered extraction/questions.

Run:
```powershell
uvicorn main:app --reload
```
Open `http://localhost:8000/docs`.

### Frontend
```powershell
cd frontend
npm install
copy .env.example .env
npm run dev
```
Open `http://localhost:5173`.

## RAG
Put approved `.txt`/`.md` material into:
`backend/knowledge_base/company_info`
`backend/knowledge_base/recruitment_guidelines`
`backend/knowledge_base/interview_guidelines`
`backend/knowledge_base/skill_descriptions`

Then call `POST /api/knowledge-base/ingest` after authentication. ChromaDB is persisted under `CHROMA_DIR`.

## LLM
Set `OPENAI_API_KEY` and optionally `OPENAI_MODEL`. Without an external LLM key, the application still supports PDF extraction, deterministic job-relevant matching/scoring, and the API/dashboard, but LLM-dependent structured generation is unavailable. No fake LLM response is used.

## Main APIs
- POST /api/auth/register, /api/auth/login
- GET/POST /api/jobs
- POST /api/jobs/upload
- POST /api/jobs/{job_id}/resumes
- GET /api/jobs/{job_id}/candidates
- GET/POST /api/candidates/{candidate_id}
- POST /api/candidates/{candidate_id}/analyze
- GET /api/dashboard
- POST/GET /api/interview/candidates/{candidate_id}/generate
- POST/GET /api/reports/candidates/{candidate_id}
- GET/POST /api/knowledge-base, /api/knowledge-base/ingest

## Tests
From `backend`:
`pytest`

## Security
Secrets stay in `.env`; `.env` is excluded from the submission. Uploads are PDF-only, size-limited, safely named, and text extraction failures are rejected. Protected attributes are excluded from ranking logic.
