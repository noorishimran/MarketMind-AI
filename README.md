# MarketMind AI

**Full-Stack AI Marketing Content Generator | Big Brains Generative AI Internship**

MarketMind AI is a full-stack web application that turns a product brief into a structured marketing strategy, platform-specific marketing copy, and AI-generated visual concepts and images. It also includes an AI campaign agent with a **human approval checkpoint** before the agent proceeds to final image generation. The application saves campaigns, drafts, and automation runs locally for review.

> **Project status:** Phases 1–4 implemented and locally tested. This is an internship prototype, not a production-ready multi-user SaaS or social-media publishing platform.

## Features

### Phase 1 — Campaign Strategy
- Campaign brief with input validation: product, description, target audience, platform, objective, tone, and language.
- Gemini-generated structured strategy containing campaign idea, audience angle, selling points, CTA, content pillars, and recommended formats.
- Campaign persistence and history in SQLite.

### Phase 2 — Marketing Content
- Platform-specific structured content for **Instagram, Instagram Stories, LinkedIn, Facebook, and YouTube**.
- Three content variations per platform, with copy and preferred-version selection.
- Saved output tied to the campaign strategy.

### Phase 3 — AI Creative Studio
- Three visual concepts and prompts for Instagram Post, Instagram Story, LinkedIn Creative, Facebook Ad, and YouTube Thumbnail.
- AI image generation using Cloudflare Workers AI when configured.
- Optional product reference image upload, image adjustments, and preferred-image selection.
- Generated image assets saved in the local media directory.

### Phase 4 — AI Campaign Agent
- Guided workflow from marketing strategy through platform content and creative concepts.
- **Human-in-the-loop approval:** agent pauses with `awaiting_approval` before final image generation.
- **Approve & Continue**, **Reject**, and resume controls for saved runs.
- Persistent run history and step statuses.
- Generates local drafts only; **does not publish or post externally**.

## Tech Stack

| Layer | Technology |
| --- | --- |
| Frontend | React, Vite, JavaScript, CSS |
| Backend | Python, FastAPI, Pydantic |
| Text AI | Google Gemini API |
| Image AI | Cloudflare Workers AI (configured model) |
| Persistence | SQLite |
| Image handling | Pillow |

## Architecture

```text
React + Vite (http://localhost:5173)
    │  HTTP requests via /api
    ▼
FastAPI (http://127.0.0.1:8000)
    ├── Pydantic request and response validation
    ├── Gemini: marketing strategy, copy and image concepts
    ├── Cloudflare Workers AI: visual generation
    ├── AI agent: run → await approval → approve/reject → continue
    ├── SQLite: campaigns, generated content, creatives and agent runs
    └── Local backend/media: uploaded references and generated images
```

## Prerequisites

- Python 3.11+ (the project has also been developed with Python 3.13)
- Node.js 20+
- A [Gemini API key](https://aistudio.google.com/apikey)
- Cloudflare account ID and Workers AI API token for image generation

## Local Setup (Windows PowerShell)

Clone the repository and open the project folder:

```powershell
git clone https://github.com/noorishimran/MarketMind-AI.git
cd MarketMind-AI
```

### 1. Backend

From the project root:

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
```

Edit `backend/.env` and insert your own credentials. Example configuration (actual available models and environment-variable names should be checked against `backend/.env.example`):

```env
GEMINI_API_KEY=your_gemini_api_key_here
GEMINI_MODEL=gemini-3.1-flash-lite
GEMINI_FALLBACK_MODEL=gemini-3.1-flash-lite
CLOUDFLARE_ACCOUNT_ID=your_cloudflare_account_id
CLOUDFLARE_API_TOKEN=your_cloudflare_api_token
CLOUDFLARE_IMAGE_MODEL=@cf/black-forest-labs/flux-1-schnell
FRONTEND_ORIGIN=http://localhost:5173
```

The `GEMINI_MODEL` value is an example only. Use a model your API key can access and that has remaining quota. Model names and API availability can change.

Start the backend:

```powershell
uvicorn main:app --reload
```

Backend API docs: http://127.0.0.1:8000/docs  
Health endpoint: http://127.0.0.1:8000/api/health

If PowerShell prevents activation, run the backend via `.venv\Scripts\python.exe -m uvicorn main:app --reload`.

### 2. Frontend

In a **second terminal**, from the project root:

```powershell
cd frontend
npm install
npm run dev
```

Open http://localhost:5173.

## How to Use

1. Enter a product brief and generate a marketing strategy.
2. Open Phase 2 to create and select platform-specific marketing copy.
3. Use Phase 3 to generate concepts, create AI images, and select a preferred visual.
4. In Phase 4, select **Run Complete Campaign Agent** to automate draft creation.
5. When the agent reaches **Human Approval Required**, review its drafts and concepts. Choose **Approve & Continue** for final image generation or **Reject** to stop that run.
6. Use Campaign History and Saved Automation Runs to reopen prior work.

## Testing Checklist

- [ ] Backend health endpoint returns `status: ok`.
- [ ] Empty/invalid campaign form shows validation feedback.
- [ ] Strategy is generated, saved, and reappears in campaign history.
- [ ] Content generation returns three variations for the selected platform.
- [ ] AI creative concepts generate correctly.
- [ ] Images generate and display when image-service quota is available.
- [ ] Agent pauses at `awaiting_approval` before generating its final image.
- [ ] Reject records a rejected run.
- [ ] Approve & Continue resumes and completes the workflow.
- [ ] Frontend production build passes: `cd frontend; npm run build`.

Locally tested example domains include e-commerce, education, restaurant, software, and clothing. API rate limits, billing, and model availability may affect repeatability.

## Project Structure

```text
MarketMind-AI/
├── backend/
│   ├── main.py                 # API and campaign-agent workflows
│   ├── requirements.txt
│   ├── .env.example            # Safe configuration template
│   ├── marketmind.db           # Local-only, Git ignored
│   └── media/                  # Local-only generated assets, Git ignored
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   └── styles.css
│   ├── package.json
│   └── ...
├── .gitignore (or component-level .gitignore files)
└── README.md
```

## API and Data Notes

- Campaign, content, creative, and agent-run records are stored in a local SQLite database.
- Generated assets and uploaded reference photos are stored locally in `backend/media/`.
- The Gemini key and Cloudflare credentials are used server-side and must never be committed.
- An AI request failure should be surfaced as an error, not replaced with fabricated AI output.
- This application has no social-platform publishing integration.

## Security and Limitations

- Do not commit `.env`, `*.db`, uploaded images, or `.venv` to Git.
- Rotate any credentials accidentally exposed in screenshots, logs, or a repository.
- Generated content and images require human review for factual accuracy and visual fidelity.
- Add authentication, authorization, secure media storage, database migrations, API rate limiting, and restricted diagnostic endpoints before public deployment.
- The approval checkpoint controls the **agent workflow**; independent Phase 3 image-generation actions are separate controls.
- Free API quotas can return `429 RESOURCE_EXHAUSTED`; transient provider overload can return `503`.

## Suggested Submission Evidence

- Campaign brief and generated strategy
- Three structured platform-specific content variations
- Three visual concepts and at least one rendered image
- Agent in `awaiting_approval` state, before image generation
- Agent after Approve & Continue (`completed`) and a separate rejected run
- Campaign history and saved agent runs
- Backend API health check and frontend build output

## Credits

Developed as a **Big Brains Generative AI Internship** full-stack project.
