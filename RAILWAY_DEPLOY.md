# Cheat Clip Pro — Railway Ready

This package is configured as a **single Railway service**:
- Vite/React frontend is built during the Docker image build.
- FastAPI serves `/api/*` and the production frontend from the same domain.
- Railway's `$PORT` is used automatically.
- FFmpeg is installed inside the container.
- `YOUTUBE_COOKIES_B64` can be used for persistent deployment cookies.

## Required Railway variables

Set these in Railway > Service > Variables:

- `GEMINI_API_KEY` = your Gemini API key
- `SUPADATA_API_KEYS` = your Supadata API key(s)
- `YOUTUBE_COOKIES_B64` = optional base64 encoded Netscape cookies.txt
- `ALLOWED_ORIGINS` = optional; for this single-service deployment you can leave it empty

Do NOT commit real API keys or cookies to GitHub.

## Health check

`/health`

## Local Docker test

```bash
docker build -t cheat-clip-pro .
docker run --rm -p 8000:8000 cheat-clip-pro
```

Then open `http://localhost:8000`.
