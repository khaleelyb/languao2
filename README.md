# Arabic Staircase (Edge TTS)

- `index.html` – the app (static)
- `api/tts.py` – Vercel Python function that returns MP3 from Microsoft Edge neural voices

Deploy: `vercel` (or push to a GitHub repo and import it in Vercel). Nothing else to configure.
Local test: `vercel dev`.

Android WebView: load your deployed URL, or if you bundle index.html inside the app,
set `const API="https://YOUR-APP.vercel.app"` near the top of the script.
If the API is unreachable the app falls back to the device's built-in Arabic voice.
