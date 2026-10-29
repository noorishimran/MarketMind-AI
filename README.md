# MarketMind AI

**Full-Stack AI Marketing Content Generator | Big Brains Generative AI Internship**

MarketMind AI is a full-stack web application that transforms a product brief into an AI-generated marketing strategy, platform-specific copy, visual concepts, and generated images. It includes a campaign automation agent with a **human approval checkpoint** before final image generation.

### Live demo

- **Frontend:** https://market-mind-steel.vercel.app
- **Backend health check:** https://marketmind-backend-ten.vercel.app/api/health
- **Source code:** https://github.com/noorishimran/MarketMind-AI

> **Project status:** Phases 1–4 are implemented and deployed. Core end-to-end workflows have been tested on the live application, including campaign creation, content generation, AI images, approval-based automation, saved history, and mobile layout. This is an internship prototype, **not** a production-ready multi-user SaaS or automated social-media publishing service.

## Features

### Phase 1 — AI Marketing Strategy

- Campaign brief with validation for product name, description, target audience, platform, objective, tone, and language.
- Structured marketing strategy generated with Google Gemini: campaign idea, audience angle, selling points, CTA, content pillars, and recommended formats.
- Campaign history and persistent campaign records.

### Phase 2 — Platform-Specific Content

- Generates structured content for **Instagram, Instagram Stories, LinkedIn, Facebook, and YouTube**.
- Up to three variations per selected format, including hooks, main copy, calls to action, and hashtags where applicable.
- Allows selecting preferred content and reopening previously generated drafts.

### Phase 3 — AI Creative Studio

- Generates three creative concepts with descriptive AI image prompts.
- Supports **Instagram Post, Instagram Story, LinkedIn Creative, Facebook Ad, and YouTube Thumbnail** formats.
- Generates images with Cloudflare Workers AI when configured and quota permits.
- Optional reference image upload, image adjustments, and preferred-image selection.
- Saves images and associates them with their campaigns.

### Phase 4 — AI Campaign Agent

- Coordinates strategy, campaign planning, copy, and visual concepts.
- Pauses at **`awaiting_approval`** so a person can review draft content and concepts.
- Supports **Approve & Continue**, rejection, saved runs, and execution history.
- Generates approved visual drafts; **does not automatically publish to social networks**.

### Phase 5 — Testing and Deployment

- Frontend and backend deployed separately on Vercel.
- PostgreSQL-backed campaign and automation history in the hosted deployment.
- Image assets stored in Supabase Storage.
- Major workflows tested using leather-goods and restaurant campaigns, including persistence after refreshing the page.
- Responsive layout checked at a mobile viewport width of 400 px.

## Tech Stack

| Layer | Technology |
| --- | --- |
| Frontend | React, Vite, JavaScript, CSS |
| Backend | Python, FastAPI, Pydantic |
| Text and planning AI | Google Gemini API |
| AI image generation | Cloudflare Workers AI |
| Hosted database | Supabase PostgreSQL |
| Hosted image storage | Supabase Storage |
| Local development persistence | SQLite, when configured for local mode |
| Image processing | Pillow |
| Deployment | Vercel (frontend and backend) |

## Architecture

```text
Browser
  |
  v
React + Vite frontend (Vercel)
  |  HTTPS API requests
  v
FastAPI backend (Vercel)
  |-- Google Gemini: campaign strategy, copy, visual concepts
  |-- Cloudflare Workers AI: image generation
  |-- Agent workflow: plan -> approval -> continue/reject
  |-- Supabase PostgreSQL: campaigns, drafts, creatives, agent runs
  `-- Supabase Storage: uploaded and AI-generated images
```

In local development, the frontend typically runs at `http://localhost:5173` and the backend at `http://127.0.0.1:8000`. Storage and database behavior depend on the configured environment variables; the hosted configuration uses Supabase.

## Requirements

- Python 3.11+
- Node.js 20+
- Google Gemini API key
- Cloudflare account ID and Workers AI API token for image generation
- For cloud persistence: Supabase project with a PostgreSQL connection and image-storage bucket

## Run Locally (Windows PowerShell)

### 1. Clone the repository

```powershell
git clone https://github.com/noorishimran/MarketMind-AI.git
cd MarketMind-AI
```

### 2. Start the backend

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
```

Set your own values in `backend/.env`. Use the variable names supported by your current backend and `.env.example`. Example:

```dotenv
GEMINI_API_KEY=your_gemini_api_key
GEMINI_MODEL=your_available_gemini_model
GEMINI_FALLBACK_MODEL=your_available_fallback_model
CLOUDFLARE_ACCOUNT_ID=your_cloudflare_account_id
CLOUDFLARE_API_TOKEN=your_cloudflare_api_token
CLOUDFLARE_IMAGE_MODEL=@cf/black-forest-labs/flux-1-schnell
FRONTEND_ORIGIN=http://localhost:5173

# Optional for hosted-style cloud persistence:
# DATABASE_URL=postgresql://USER:PASSWORD@HOST:PORT/DBNAME
# SUPABASE_URL=https://YOUR_PROJECT.supabase.co
# SUPABASE_SERVICE_ROLE_KEY=your_server_side_service_role_key
# SUPABASE_STORAGE_BUCKET=marketmind-images
```

**Security:** Never put your service-role key, Gemini key, or Cloudflare token in frontend variables, Git commits, or screenshots. Ensure the Supabase bucket and access policies are configured appropriately. Never commit `.env`.

```powershell
uvicorn main:app --reload
```

Backend health check: http://127.0.0.1:8000/api/health  
Interactive API documentation: http://127.0.0.1:8000/docs

If PowerShell prevents activation, use `.\.venv\Scripts\python.exe -m uvicorn main:app --reload`.

### 3. Start the frontend

Open a **second terminal** at the repository root:

```powershell
cd frontend
npm install
```

Create `frontend/.env.local` to point at the local API:

```dotenv
VITE_API_BASE_URL=http://127.0.0.1:8000
```

Then start the dev server:

```powershell
npm run dev
```

Open http://localhost:5173.

## Deployed Environment

The live deployment separates the frontend and backend into Vercel projects. The frontend's `VITE_API_BASE_URL` points to the backend's HTTPS domain; the backend's `FRONTEND_ORIGIN` allows the production frontend origin. Backend secrets and Supabase credentials must be set only in the **backend's** environment settings.

| Setting | Purpose |
| --- | --- |
| `VITE_API_BASE_URL` | Frontend setting: backend HTTPS URL |
| `FRONTEND_ORIGIN` | Backend setting: allowed frontend origin |
| `GEMINI_API_KEY`, `GEMINI_MODEL`, `GEMINI_FALLBACK_MODEL` | Gemini configuration |
| `CLOUDFLARE_ACCOUNT_ID`, `CLOUDFLARE_API_TOKEN`, `CLOUDFLARE_IMAGE_MODEL` | Cloudflare image generation |
| `DATABASE_URL` | PostgreSQL connection for hosted persistence |
| `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `SUPABASE_STORAGE_BUCKET` | Server-side image storage |

**Do not store production secret values in this README.** Service availability and API quotas can affect generation.

## Usage

1. Create a campaign from a product brief and generate its marketing strategy.
2. Generate platform-specific content and select a preferred variation.
3. Generate visual concepts and images, then select a preferred creative.
4. Launch the AI campaign agent. Review the plan and draft content when it reaches `awaiting_approval`.
5. Approve to continue generating final images, or reject the run.
6. Reopen campaigns, drafts, images, and saved automation runs from history.

## Testing Checklist

The following checks reflect the live workflow tests performed for the internship demo, not a full security or load-test certification.

- [x] Backend health check reports a healthy API configuration.
- [x] Marketing strategy is generated and saved.
- [x] Campaign history reloads previously saved data.
- [x] Platform content generation produces three variations.
- [x] Creative concepts and AI-generated images display in the web app.
- [x] Generated image files appear in Supabase Storage.
- [x] AI agent reaches `awaiting_approval` before final image generation.
- [x] Approve & Continue proceeds to a `completed` automation run.
- [x] Mobile form fields and buttons fit a 400 px responsive viewport.
- [ ] Verify a rejected automation run is saved with the expected status.
- [ ] Verify invalid-form errors and failure states across all inputs.
- [ ] Run and record a fresh `npm run build` result.

## Project Structure

```text
MarketMind-AI/
|-- backend/
|   |-- main.py              # FastAPI endpoints and agent workflow
|   |-- requirements.txt
|   `-- .env.example         # Configuration template (no secrets)
|-- frontend/
|   |-- src/
|   |   |-- App.jsx
|   |   `-- styles.css
|   `-- package.json
|-- README.md
`-- .gitignore                # Or component-level ignore files
```

Local database files, local media, virtual environments, and credentials must remain excluded from version control.

## Scope and Limitations

- This is a marketing **draft creation** tool, not a social-media scheduler or automatic publisher.
- Generated copy and images require human review, particularly for factual claims and accurate product depiction.
- Gemini and Cloudflare requests may fail due to rate limits (`429`), provider overload (`503`), or exhausted quotas.
- The prototype is not designed as an authenticated, multi-tenant SaaS product. Production use would require authentication, per-user authorization, abuse prevention, database migrations, additional monitoring, and security review.
- The agent's approval gate applies to its workflow; standalone creative-generation controls can generate assets independently.

## Internship Demonstration Evidence

Recommended materials for submission: live frontend URL, backend health check, a saved campaign strategy, three content variations, creative concepts and images, Supabase Storage proof, an agent paused for approval, a completed run, campaign history after refresh, and mobile layout screenshots.

## Credits

Developed for the **Big Brains Generative AI Internship**.
