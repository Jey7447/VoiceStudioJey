# Vercel frontend deployment

The browser UI is deployed to Vercel as a Vite SPA. The AI-heavy FastAPI backend remains on a persistent machine or GPU host.

## Vercel project settings

- **Repository:** `Jey7447/VoiceStudioJey`
- **Production/Preview branch:** `deploy/vercel-ready` while deployment is being validated
- **Root Directory:** `frontend`
- **Framework Preset:** Vite
- **Build Command:** `npm run build`
- **Output Directory:** `dist`

The frontend already includes `frontend/vercel.json` for SPA deep-link routing.

## Environment variable

Set this Vercel environment variable for Preview and Production:

```text
VITE_OMNIVOICE_API=https://<your-public-backend-host>
```

Do not put an API key or other secret in a `VITE_*` variable. Vite exposes `VITE_*` values to browser code.

## Backend requirements

The backend URL must be HTTPS and reachable by the browser. It must support the API routes used by the UI and WebSocket connections used by real-time features.

Configure the backend with:

```text
OMNIVOICE_SERVER_MODE=1
OMNIVOICE_API_KEY=<strong-secret>
OMNIVOICE_ALLOWED_ORIGINS=https://<your-vercel-domain>
```

If the backend requires an API key, configure the corresponding key in the app's supported client settings rather than exposing the key through `VITE_OMNIVOICE_API`.

## Important architecture note

Do not deploy `backend/main.py` as an ordinary Vercel serverless function. OmniVoice starts workers, maintains model state, serves WebSockets, uses local persistent data, and performs GPU/CPU-heavy inference. Those workloads belong on the persistent backend host.

## Validation checklist

1. Vercel builds the `frontend` directory successfully.
2. The home page loads directly.
3. Refreshing a client-side route still returns the SPA.
4. `VITE_OMNIVOICE_API` points to the real backend.
5. `/health` is reachable from the browser's network.
6. API requests succeed with the configured authentication method.
7. WebSocket features connect over `wss://`.
8. Backend CORS allows the exact Vercel origin.
9. Audio uploads/downloads and generated audio URLs work through the persistent backend.
10. The desktop/local backend behavior remains unchanged.
