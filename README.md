# MarketMind AI — Phase 1

**Big Brains Internship | Full Stack AI Marketing Content Generator**

Working Phase 1 scope: user brief → FastAPI validation → Gemini structured **strategy** → SQLite saving → React strategy display. This phase intentionally does not generate final captions or images (scheduled for phases 2 and 3).

## Architecture

```
React + Vite (http://localhost:5173)
     │ POST /api/strategy + GET /api/campaigns
     ▼
FastAPI + Pydantic (http://localhost:8000)
     ├─ validates input
     ├─ sends strategy prompt to Gemini API (server-side only)
     ├─ parses structured output with Pydantic
     └─ persists campaign to SQLite (backend/marketmind.db)
```

## Prerequisites

- Python 3.10+ (3.11 or 3.12 recommended)
- Node.js 20+
- Gemini API key (create at https://aistudio.google.com/apikey)

## Backend — terminal 1 (Windows PowerShell)

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
```

Edit `backend/.env` and replace `your_gemini_api_key_here` with your own key. If PowerShell blocks activation, run `.venv\Scripts\python.exe -m pip install -r requirements.txt` and `.venv\Scripts\python.exe -m uvicorn main:app --reload` instead.

Run server:

```powershell
uvicorn main:app --reload
```

Backend: http://127.0.0.1:8000/docs

## Frontend — terminal 2

```powershell
cd frontend
npm install
npm run dev
```

App: http://localhost:5173

> Run commands from each respective folder, in two separate terminals.

## Verify Phase 1

1. Open frontend; status at top right shows connected when API key is configured and backend is running.
2. Try submitting empty form → inline validation errors.
3. Click **Use example**, then **Generate marketing strategy**.
4. When Gemini returns successfully, strategy cards show campaign idea, audience, selling points, message, tone, CTA, content types and pillars.
5. Click **Campaign history** to see the saved campaign; reload page to confirm persistence.
6. Test the backend at http://127.0.0.1:8000/docs.

## Submission screenshots

- Filled campaign brief (all required input fields)
- Validation errors on an empty form
- Generated structured marketing strategy (top and bottom)
- Campaign history containing the saved strategy
- (Optional) FastAPI `/docs` or terminal showing successful HTTP request

## Database (Phase 1)

SQLite `campaigns` table: `id`, `created_at`, `brief_json`, `strategy_json`. This is a valid local Phase 1 persistence foundation; PostgreSQL and user accounts can be introduced in subsequent phases.

## Notes

- **No fabricated AI output**: missing API keys or failed API calls display an error; no dummy strategy is shown as if AI-generated.
- Gemini keys are read only on backend; `.env` is ignored by Git.
- Gemini model defaults to `gemini-2.5-flash`, configurable in `.env`. Model availability depends on key and provider quota.
- The app is a local prototype, not a production multi-user SaaS. Add authentication, per-user records and stronger limits before publishing publicly.
- Usage of Gemini instead of OpenAI should be checked against your internship mentor's phase rules if they mandate a specific LLM provider.
