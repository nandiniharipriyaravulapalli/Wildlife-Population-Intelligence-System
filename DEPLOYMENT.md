# Deploying WPIS

## 1. Database (hosted PostgreSQL)
Create a Postgres database, then run in order:
1. `database/wildlife_db.sql`
2. `database/migrations/001_reports.sql`
3. `database/migrations/002_admin.sql`

The dump creates no users. Register an account in the app, then make it an admin:
`UPDATE users SET role = 'administrator' WHERE email = 'you@example.com';`
(The `/admin/*` endpoints now require an administrator account.)

## 2. Backend (Docker, from the repo root)
Environment variables on the host:
- `APP_ENV=production`
- `DATABASE_URL=postgresql://user:pass@host:5432/dbname`
- `JWT_SECRET=<long random string>` (the app refuses to start in production without it)
- `CORS_ORIGINS=https://your-frontend-domain`
- `PUBLIC_API_URL=https://your-backend-domain`

Health check: `GET /health`. The backend needs enough RAM for PyTorch/YOLO/CLIP
(roughly 2 GB or more). CLIP weights download from Hugging Face on first start.

## 3. Audio model (optional until the file is available)
Without `backend/audio_model/AnimalModel/model.safetensors`, everything works
except audio detection, which returns a clear error. To enable it, put the file
in that folder on the server (do not commit it to git).

## 4. Frontend (Vercel / Netlify)
Root directory `frontend`, build `npm run build`, output `dist`.
Set `VITE_API_URL=https://your-backend-domain` and `VITE_USE_MOCK=false`.
