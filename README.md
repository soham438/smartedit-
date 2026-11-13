# SmartEdit AI — MVP source project

A simple, beginner-friendly photo and video enhancement web app.

## What works in this MVP

- Next.js + TypeScript frontend with a simple upload-first workflow.
- Photo enhancement endpoint using OpenCV (CLAHE, denoising, sharpening).
- Video enhancement endpoint using FFmpeg (basic stabilization is not included; this MVP applies a real FFmpeg color/contrast/denoise filter).
- Real processing status and error reporting.
- Downloadable processed files.
- Local project history in the browser.
- PostgreSQL schema for the next stage (Supabase-compatible).

## Important scope notes

This is a functional local MVP, not yet a production SaaS. It does **not** include production authentication, cloud object storage, payments, GPU-based generative restoration, or multi-user cloud history. The current app stores uploaded and processed media on the backend machine and keeps recent job metadata in browser local storage. Do not expose this development server publicly with private user media.

AI enhancement is not guaranteed to recover information that was never captured. The photo pipeline performs real classical computer-vision enhancement; it is not a generative restoration model. Video processing performs real FFmpeg filtering, not semantic AI editing.

## Requirements

- Node.js 20+
- Python 3.10+
- FFmpeg installed and available on PATH (for video processing)

## Run locally

### 1. Start the API

```bash
cd backend
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

API docs: http://127.0.0.1:8000/docs

### 2. Start the web app

In a second terminal:

```bash
cd frontend
cp .env.example .env.local
npm install
npm run dev
```

Open http://localhost:3000

## Optional Supabase database

1. Create a Supabase project.
2. Open SQL Editor and run `database/schema.sql`.
3. Copy the project URL and anon key into `frontend/.env.local`.
4. The current MVP does not yet call Supabase. The schema is provided for the next integration phase; do not treat local browser history as cloud-synced.

## API endpoints

- `GET /health`
- `POST /api/photo/enhance` — multipart field `file`
- `POST /api/video/enhance` — multipart field `file`
- `GET /api/files/{filename}` — download processed output

## Production checklist before launch

- Add Supabase Auth and enforce ownership on every API request.
- Replace local disk storage with private S3/Supabase Storage buckets and signed URLs.
- Add background workers/Redis for long video jobs.
- Add upload quotas, MIME sniffing, malware scanning, retention/deletion controls and rate limits.
- Run processing in isolated containers with CPU/GPU/time limits.
- Add tests, monitoring, backups and payment-provider webhooks.
- Configure HTTPS, CORS, secrets and a production deployment.
