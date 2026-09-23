# Med64 — deploying with a shared AI key on Vercel

This project is a static site (`index.html` + `questions/`) plus one serverless
function (`api/gemini.js`) that proxies AI requests to Google's Gemini API.
The Gemini API key lives only on the server (a Vercel environment variable),
so visitors to your deployed site can use the AI Assistant and AI-question
generator without entering any key of their own.

## 1. Get a Gemini API key

Create one at <https://aistudio.google.com/apikey> (Google account required).

## 2. Push this folder to GitHub

```bash
git init
git add .
git commit -m "Med64 with server-side Gemini proxy"
git branch -M main
git remote add origin <your-empty-github-repo-url>
git push -u origin main
```

(You can also skip GitHub and drag-and-drop this folder directly into the
Vercel dashboard, but a git repo makes future updates a simple `git push`.)

## 3. Import the project into Vercel

1. Go to <https://vercel.com/new> and import the GitHub repo (or upload the folder).
2. Framework preset: choose **Other** — no build step is needed.
3. Leave Build Command / Output Directory blank (this is a static site + one
   serverless function; Vercel detects `api/gemini.js` automatically).

## 4. Set environment variables

In your new Vercel project: **Settings → Environment Variables**, add:

| Name | Required | Example | Notes |
|---|---|---|---|
| `GEMINI_API_KEY` | Yes | `AIza...` | Your key from step 1. Never expose this in client code. |
| `GEMINI_MODEL` | No | `gemini-2.5-flash` | Default model if a visitor doesn't override one. |
| `GEMINI_ALLOWED_MODELS` | No | `gemini-2.5-flash,gemini-2.5-pro` | Restricts which models the "Model override" field can request. |
| `ACCESS_CODE` | No | `mymed64class2026` | If set, visitors must enter this in the app to use AI features — a simple way to keep the endpoint from being used by strangers. |
| `ALLOWED_ORIGIN` | No | `https://your-site.vercel.app` | Locks the proxy to your own deployed domain. |

See `.env.example` for the same list with comments.

## 5. Deploy

Click **Deploy**. Once it finishes, your site (including the AI Assistant,
AI Questions page, and the `/api/gemini` proxy) is live at the URL Vercel
gives you — no visitor needs their own API key.

## 6. Updating the question bank later

Edit or add files under `questions/`, make sure `questions/index.json` lists
every filename, commit, and push — Vercel redeploys automatically. Visitors
click **Refresh from GitHub** in the app (it just re-fetches the deployed
`questions/index.json`, wherever it's hosted).

## Security notes

- `api/gemini.js` includes a same-origin check, an optional shared
  `ACCESS_CODE`, and a best-effort per-IP rate limit. These reduce casual
  abuse but are **not** a full authentication system — anyone who discovers
  the endpoint URL and forges headers could still call it directly.
- For a public deployment, also set a spending/quota cap on the Google Cloud
  project tied to `GEMINI_API_KEY` so an abused endpoint can't run up an
  unexpected bill.
- If you want tighter control over who can use the AI features, set
  `ACCESS_CODE` and share that passphrase only with your intended users.
- The rate limiter is in-memory per serverless instance, so it resets on
  cold starts and isn't shared across concurrent instances. For stronger,
  globally-consistent rate limiting, back it with Vercel KV or Upstash Redis.

## Local development

```bash
npm i -g vercel
vercel dev
```

This runs both the static site and `api/gemini.js` locally. Create a local
`.env` (based on `.env.example`, and listed in `.gitignore` so it's never
committed) for your local `GEMINI_API_KEY`.
