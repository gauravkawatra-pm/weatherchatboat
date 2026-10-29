# PIN Code Weather Chat

A small chatbot website. Type a 6-digit Indian PIN code and it replies with the current temperature. It also shows how many Google Gemini tokens each reply used.

**Live site:** `https://gauravkawatra-pm.github.io/REPO-NAME/` (replace `REPO-NAME` with this repository's name)

## How it works

```
Browser (GitHub Pages)  ->  Cloudflare Worker  ->  Zippopotam (PIN -> latitude/longitude)
   index.html               worker.js            ->  Open-Meteo (current temperature)
                            holds the secret key ->  Google Gemini (writes the reply)
```

- `index.html` is the chat page. It contains **no API key**.
- `worker.js` runs on Cloudflare Workers. The Gemini API key is stored there as a secret, so it never reaches the browser or this repository.
- The page shows tokens for the last reply and a running total.

## Files

| File | Purpose |
|---|---|
| `index.html` | Chat page hosted on GitHub Pages |
| `worker.js` | Server code. Paste it into Cloudflare. It does not run on GitHub |
| `README.md` | This file |

## Setup

### 1. Get a Gemini API key
Create a key in Google AI Studio. Keep it private: never paste it into this repository, an issue, or a chat.

### 2. Create the Cloudflare Worker
1. Sign up at cloudflare.com and open **Workers & Pages**.
2. Create a new Worker (Hello World template) and deploy it.
3. Click **Edit code**, replace everything with the contents of `worker.js`, and deploy again.
4. Copy the Worker address (it ends in `.workers.dev`).

### 3. Add the Worker settings
In the Worker, open **Settings > Variables and Secrets** and add:

| Name | Type | Value |
|---|---|---|
| `GEMINI_API_KEY` | Secret | your Gemini API key |
| `ALLOWED_ORIGINS` | Text | `https://gauravkawatra-pm.github.io` |

`ALLOWED_ORIGINS` has no repository name and no trailing slash. To test locally, add `,http://localhost:8000` after it.

### 4. Point the page at your Worker
In `index.html`, find this line and put in your Worker address:

```js
const WORKER_URL = "https://YOUR-WORKER-NAME.YOUR-SUBDOMAIN.workers.dev";
```

### 5. Publish on GitHub Pages
1. Upload `index.html` to this repository.
2. Open **Settings > Pages**, choose the `main` branch, and save.
3. Open the site address after a minute or two.

## Using it

Type a PIN code such as `110001` and send. The reply gives the temperature, and the bar under the chat shows the tokens used.

## Notes and limits

- **Token total:** the running total is stored in your own browser only. For account-wide usage, check Google AI Studio or Google Cloud.
- **PIN coverage:** the PIN lookup does not include every Indian PIN code. If a valid PIN fails, the lookup service probably does not have it.
- **Model name:** the Gemini model is set by the `MODEL` line at the top of `worker.js`. If Google retires it, change that line to a current model ID.
- **Free services:** Zippopotam and Open-Meteo are free services with their own usage terms, and Open-Meteo's free tier is for non-commercial use.

## Security

- The key lives only in Cloudflare as a secret. Never commit it to GitHub.
- The Worker only answers requests from the site listed in `ALLOWED_ORIGINS`. This blocks other websites but is not a full lock, because a script can fake the origin.
- Set a budget alert in Google Cloud, or stay on the free tier, so a leaked key cannot run up a bill.
- If you ever expose the key, delete it in Google AI Studio and create a new one.
