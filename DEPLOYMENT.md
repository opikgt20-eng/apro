# Deployment architecture (Vercel + persistent backend)

This project uses FFmpeg, yt-dlp, Whisper/PyTorch, OpenCV, long-running SSE analysis,
background render jobs, and local files. Those workloads should NOT run as a normal
Vercel serverless function if you want behavior comparable to localhost.

Recommended stable setup:

1. Deploy the **frontend** to Vercel from the project root.
2. Deploy the **backend** using the included `Dockerfile` to Render/Railway/Fly.io or
   another persistent container host.
3. Configure the frontend's API URL so `/api/*` points at that backend. For the current
   code, the easiest option is to put the backend behind the same domain/reverse proxy,
   or replace `/api/...` with your backend URL.
4. Set backend environment variables:
   - `GEMINI_API_KEY`
   - `SUPADATA_API_KEYS`
   - `ALLOWED_ORIGINS` = your Vercel URL
   - `YOUTUBE_COOKIES_B64` = base64 of a Netscape `cookies.txt`
5. Do not commit cookies, API keys, or `.env` files.

## Generate YOUTUBE_COOKIES_B64

PowerShell:
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes(".\cookies.txt"))
```

Then paste the resulting single-line value into the backend host's environment variables.

Why this matters:
- Vercel's filesystem is ephemeral/read-only outside `/tmp`.
- A `cookies.txt` uploaded through the UI cannot be treated as permanent storage there.
- This backend performs CPU/memory-heavy FFmpeg/Whisper/OpenCV work that can exceed
  serverless limits and is better suited to a persistent worker/container.

## Supadata

Set `SUPADATA_API_KEYS` on the backend. Multiple keys can be comma-separated. The
existing code already rotates keys and falls back through transcript methods.
