# HelmGuard Product‑Led‑Growth Widget – Developer Guide

> **Purpose** – This document is the single source of truth for building, testing and shipping the drop‑in questionnaire‑automation widget that will live on HelmGuard’s marketing site (built in Framer/Webflow). Paste this into Cursor, Notion or anywhere you manage docs – it’s 100 % Markdown.

---

## 1 📦 Project at a Glance

|                  | Description                                                                                            |
| ---------------- | ------------------------------------------------------------------------------------------------------ |
| **User flow**    | Visitor clicks/drag‑drops a questionnaire → widget streams 3‑5 automated answers → lead‑capture CTA.   |
| **Integration**  | 2‑line `<script>` snippet in Framer / Webflow *Custom Code* block. No repo cloning.                    |
| **Build target** | One **bundled IIFE** (<50 kB gz) exposing `window.HelmGuardWidget.init()`; optional CSS in Shadow DOM. |
| **Credits used** | VEED avatar, ElevenLabs TTS, fal.ai LLM, Sieve analytics – all under hackathon sponsor quotas.         |

---

## 2 🔧 Tech Stack

### 2.1 Frontend

| Layer          | Choice & Reasoning                                                                                                            |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Framework      | **React 18 + TypeScript** – large ecosystem, hooks, easy SSR‑less build.                                                      |
| Bundler        | **Vite + esbuild** (production) – lightning dev server, tiny output.                                                          |
| Styling        | **Tailwind CSS** – utility‑first, compiled away.                                                                              |
| Isolation      | ‑ Prefer **Shadow DOM** (`container.attachShadow`) to avoid CSS bleed.<br>‑ Fallback: prefix every class under `[data-hg] …`. |
| Voice / Avatar | **VEED** embed + ElevenLabs TTS stream (lip‑sync).                                                                            |

### 2.2 Backend services (serverless)

| Concern            | Service                                                                 | Notes |
| ------------------ | ----------------------------------------------------------------------- | ----- |
| File upload store  | **S3** bucket (24‑h TTL) or Cloudflare R2.                              |       |
| Parsing & RAG      | **FastAPI** + `unstructured` loaders → `pgvector` embeddings.           |       |
| LLM inference      | **fal.ai GPUs** – OpenAI‑compatible endpoint (GPT‑4o or Mixtral 8×22B). |       |
| Analytics & Errors | **Sieve** events ➜ dashboard & Slack webhook.                           |       |

---

## 3 🗂 Repo Layout

```text
helmguard-widget/
├─ public/          # static assets, placeholder avatar
├─ src/
│  ├─ index.tsx     # exports init()
│  ├─ Widget.tsx    # root React component
│  ├─ api.ts        # fetch helpers (upload, ask, stats)
│  ├─ hooks/
│  └─ styles.css    # Tailwind entry (compiled)
├─ vite.config.ts
├─ tailwind.config.ts
├─ package.json
└─ README.md (this file)
```

> **Note**: A `questions.csv` file containing a 25‑question sample is included at the repo root for local testing.

---

## 4 ▶️ Local Development & Preview

1. **Prerequisites**

   * Node 20 LTS
   * npm 9 +
   * fal.ai key, S3 creds, ElevenLabs key in `.env`

2. **Setup**

   ```bash
   git clone git@github.com:helmguard/helmguard-widget.git
   cd helmguard-widget
   npm install
   ```

3. **Run dev server**

   ```bash
   npm run dev   # Vite on http://localhost:5173
   ```

   Vite auto‑injects the widget into a stub index.html so you can iterate.

4. **Test drop‑in snippet locally**
   *Run a static file server on `dist/` after a prod build:*

   ```bash
   npm run build      # outputs dist/widget.js
   npx serve dist     # or python -m http.server 8080
   ```

   Then open `playground/embed.html` (a minimal Framer‑style page) which loads `http://localhost:8080/widget.js` via `<script>`.

5. **Browser extension preview** (optional)
   Use Chrome DevTools > *Sources* > *Overrides* to inject `widget.js` into the live HelmGuard site without deploying.

---

## 5 🚀 Build & Deploy

| Stage          | Command / Action                                                                  |
| -------------- | --------------------------------------------------------------------------------- |
| **Lint**       | `npm run lint` (ESLint + Prettier)                                                |
| **Type check** | `npm run typecheck`                                                               |
| **Prod build** | `npm run build:prod` → `dist/widget.js`                                           |
| **Upload**     | `aws s3 cp dist/widget.js s3://cdn.helmguard.com/widget/v1/` (or `vercel deploy`) |
| **Versioning** | Path‑based (`/v1/`, `/v1.0.1/`) so old embeds never break.                        |

Injected snippet for marketing:

```html
<script src="https://cdn.helmguard.com/widget/v1/widget.js" async></script>
<script>
  window.addEventListener('DOMContentLoaded', () => {
    HelmGuardWidget.init({ mode: 'demo' });
  });
</script>
```

---

## 6 ✅ Dos & Don’ts

### Dos

* **Expose exactly one global** (`HelmGuardWidget`).
* **Shadow DOM or `[data-hg]`** – keep styles sandboxed.
* **Stream responses** over `fetch` + `ReadableStream` for snappy UX.
* **Guard costs**: limit preview to first 5 Q\&A, throttle to 1 run/IP/hour.
* **Fail softly**: if LLM errors, show "Sorry, our robots are busy" toast.
* **Log anonymised usage** to Sieve for ROI stats.
* **Write E2E test** in Playwright (`tests/embed.spec.ts`).

### Don’ts

* ❌ Ship `node_modules` in the bundle.
* ❌ Depend on Framer/Webflow globals (keep pure JS).
* ❌ Assume PDF/Excel parsing always succeeds – wrap in `try/catch`.
* ❌ Hard‑code API keys; use env vars & signed URLs.
* ❌ Block main thread with large parsing – offload to Web Worker if >2 MB.

---

## 7 🔬 Testing Checklist

| Area                | Scenario                                                             |
| ------------------- | -------------------------------------------------------------------- |
| **UX**              | Avatar appears <2 s; drag‑drop highlights zone; stream latency <3 s. |
| **Cross‑site**      | Works in standalone HTML, Framer preview, Webflow staging.           |
| **Responsive**      | Tooltip positions correctly on mobile viewport 375 px.               |
| **Error states**    | Simulate 429 / 500 from API – widget recovers.                       |
| **Accessibility**   | Keyboard focus, ARIA roles on buttons, alt text on avatar.           |
| **Cost guardrails** | 10 rapid uploads from same IP → 429.                                 |

---

## 8 📈 Analytics & Monitoring

1. Emit `widget_view`, `file_upload`, `rag_answered`, `cta_clicked` events.
2. Sieve dashboard: conversion funnel + error rate.
3. Slack alert in `#help` if error % > 5 % over 5 min.

---

## 9 💡 Future Enhancements

* Use **iframe sandbox** mode for zero style/JS bleed.
* Token‑loss meter (“You saved 97 % of manual typing”).
* Add **hand‑off mode**: push full questionnaire to HelmGuard SaaS backend.
* Integrate Microsoft Fabric for bigger doc parsing.

---

### Appendices

**A. Environment variables sample**

```ini
FAL_API_KEY=...
S3_BUCKET=helmguard-upload-demo
ELEVENLABS_KEY=...
POSTHOG_KEY=...
```

**B. ASCII Architecture Diagram**

```
Visitor ──┬ drag & drop
          ▼
   [Widget JS]─┬──► S3 PUT (presigned)
               └──► /api/ask (FastAPI)
                           │  pgvector ╮
                           │           │
                           ▼           ▼
                       fal.ai LLM ◄── context
```

---

