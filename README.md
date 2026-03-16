# Bridges Strategy Website

Static website for [bridgesstrategy.com](https://bridgesstrategy.com), deployed via Cloudflare Pages.

## Project structure

```
index.html                  Main homepage
use-cases.html              Practical AI use cases for nonprofits
governance-diagnostic.html  AI Governance Gap Diagnostic tool
styles.css                  Site styles
assets/                     Logo and images
worker/                     Cloudflare Worker (API proxy)
  worker.js                 Worker source
  wrangler.toml             Worker configuration
  package.json              Dependencies (wrangler)
```

## Deploying the Cloudflare Worker (API proxy)

The diagnostic tool calls the Anthropic API to generate personalized reports. The Worker acts as a proxy so the API key is never exposed to the browser.

### Prerequisites

- [Node.js](https://nodejs.org/) v18 or later
- A Cloudflare account (free tier is fine)
- An [Anthropic API key](https://console.anthropic.com/)

### Steps

1. **Install dependencies:**
   ```bash
   cd worker
   npm install
   ```

2. **Log in to Cloudflare:**
   ```bash
   npx wrangler login
   ```
   This opens a browser window to authenticate with your Cloudflare account.

3. **Set the API key as a secret:**
   ```bash
   npx wrangler secret put ANTHROPIC_API_KEY
   ```
   Paste your Anthropic API key when prompted. This is stored securely in Cloudflare — it never appears in any file.

4. **Deploy the Worker:**
   ```bash
   npx wrangler deploy
   ```
   The terminal will print the Worker URL, e.g. `https://governance-diagnostic-proxy.your-subdomain.workers.dev`

5. **Update the diagnostic page with the Worker URL:**

   Open `governance-diagnostic.html` and find this line near the top of the `<script>` block:
   ```js
   const WORKER_URL = 'https://YOUR_WORKER_URL/proxy';
   ```
   Replace `https://YOUR_WORKER_URL/proxy` with your actual Worker URL followed by `/proxy`, e.g.:
   ```js
   const WORKER_URL = 'https://governance-diagnostic-proxy.your-subdomain.workers.dev/proxy';
   ```

## Deploying the website

The site auto-deploys via Cloudflare Pages when you push to GitHub:

```bash
git add .
git commit -m "Add AI Governance Gap Diagnostic"
git push origin master
```

Cloudflare Pages will build and deploy within a couple of minutes. Visit your site to verify.

## Updating the Worker later

Edit `worker/worker.js`, then:
```bash
cd worker
npx wrangler deploy
```

## Rotating the API key

```bash
cd worker
npx wrangler secret put ANTHROPIC_API_KEY
```
Enter the new key when prompted. The change takes effect immediately.

## Local development

To test the diagnostic page locally, run a simple HTTP server from the project root:
```bash
python -m http.server 8000
```
Then open `http://localhost:8000/governance-diagnostic.html`.

Note: The API call will only work once the Worker is deployed and the `WORKER_URL` is set correctly.
