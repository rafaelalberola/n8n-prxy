# AI Video Studio

**Generate AI-powered short videos with realistic avatars, lipsync, and product-focused scripts — all from a simple web form.**

AI Video Studio is a fully automated video generation pipeline that turns a few creative choices into a polished 5-second vertical video (9:16) with an AI avatar speaking naturally to camera. You describe your product once, pick a character, choose a vibe, and the system handles everything: scriptwriting, image generation, video synthesis, voice cloning, and lipsync — delivered straight to Telegram.

**Cost per video: ~$1.36** | **Generation time: ~6 minutes** | **Zero coding required to operate**

---

## What You Get

- **Product-driven AI scripts** — Claude analyzes your product and writes scroll-stopping scripts calibrated to exactly 5 seconds of audio (12-25 words)
- **Persistent character library** — Create AI avatars once, reuse them across unlimited videos with consistent appearance and voice
- **6 video formats** — Snapchat Hook, Hook, Reveal, Demo, CTA, and Split — each with 3 pre-built script templates
- **Realistic lipsync** — WaveSpeed Lipsync-2-pro synchronizes generated speech with avatar mouth movements
- **Scene control** — Choose location (interior, city, beach, nature, studio, intimate), lighting (morning, golden hour, midday, night), camera style (handheld UGC, extreme close-up, push-in, static), and visual tone (iPhone Raw, Fujifilm UGC, 35mm Kodak, Cinematic)
- **Frame transitions** — Optional start-to-end pose transitions within a single video
- **Multi-model fallback** — If a primary AI service is unavailable, the pipeline gracefully degrades through alternative models
- **Instant delivery** — Final video sent to Telegram with full metadata stored in Airtable

---

## How It Works

```
  YOU (Web Form)
    |
    v
 [1] n8n Webhook receives your choices
    |
    v
 [2] Claude Sonnet generates:
     - TTS script (product-aware, 5s calibrated)
     - Image prompts for avatar frames
     - Video motion prompts
    |
    v
 [3] Google Gemini (NanoBanana) generates avatar frames
     → Uploaded to Cloudinary
    |
    v
 [4] ElevenLabs synthesizes speech with character voice
     → Uploaded to Cloudinary
    |
    v
 [5] Kling 3.0 generates 5s video from frame + motion prompt
     (Image-to-Video, 9:16 vertical)
    |
    v
 [6] WaveSpeed lipsync merges audio + video
    |
    v
 [7] Final video → Cloudinary + Airtable + Telegram
```

---

## Tech Stack

| Layer | Technology | Role |
|-------|-----------|------|
| **Frontend** | Vanilla HTML/CSS/JS | 6-step guided wizard (single `index.html`, no dependencies) |
| **Orchestration** | n8n (self-hosted) | 40+ node workflow managing the full pipeline |
| **Scriptwriting** | Claude Sonnet 4.6 | Product-aware script + prompt generation |
| **Image Generation** | Google Gemini (NanoBanana) | Avatar frame generation with reference consistency |
| **Video Generation** | Kling 3.0 (via PiAPI) | Image-to-video synthesis, 9:16, 5 seconds |
| **Voice Synthesis** | ElevenLabs | Text-to-speech with cloned character voices |
| **Lipsync** | WaveSpeed AI | Audio-video mouth synchronization |
| **Media Storage** | Cloudinary | CDN for all images, audio, and video assets |
| **Database** | Airtable | Character library + video metadata + run tracking |
| **Notifications** | Telegram Bot | Instant video delivery to your chat |

---

## Project Structure

```
.
├── index.html                 # Complete web UI (1,494 lines, zero dependencies)
├── n8n_pipeline_v74.json      # n8n workflow definition (40+ nodes, import-ready)
├── .env.local.example         # Environment variable template
├── .devcontainer/             # Docker + PostgreSQL for local n8n development
│   ├── docker-compose.yml
│   ├── Dockerfile
│   └── devcontainer.json
└── .github/                   # CI/CD workflows
```

---

## Setup

### Prerequisites

- A running [n8n](https://n8n.io) instance (self-hosted or cloud)
- API keys for: Anthropic (Claude), Google AI Studio, PiAPI (Kling), ElevenLabs, WaveSpeed, Cloudinary, Airtable, Telegram Bot

### 1. Import the Workflow

Open your n8n instance and import `n8n_pipeline_v74.json`. This creates the full 40+ node pipeline.

### 2. Configure Environment Variables

Set these in your n8n instance:

```
ANTHROPIC_API_KEY=
AIRTABLE_API_KEY=
AIRTABLE_BASE_ID=
GOOGLE_AI_STUDIO_KEY=
ELEVENLABS_API_KEY=
PIAPI_API_KEY=
WAVESPEED_API_KEY=
TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=
CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=
```

### 3. Deploy the Frontend

Host `index.html` anywhere (GitHub Pages, Netlify, Vercel, or just open locally). On first use, click the settings gear to configure:

- **Webhook URL** — Your n8n webhook endpoint (e.g., `https://your-n8n.example.com/webhook/ai-video-pipeline`)
- **Product description** — Describe what you're selling; Claude uses this to write every script

### 4. Create Your First Video

1. Select or create a character (name + gender)
2. Pick a video format (Hook, Reveal, Demo, CTA, Split, Snapchat Hook)
3. Set the scene (location + lighting)
4. Choose visual style (tone + camera movement)
5. Pick a script template or write your own
6. Review and launch

Your video arrives in Telegram in ~6 minutes.

---

## Available Voices

| Female | Male |
|--------|------|
| Laura | Bernat |
| Raquel | Volu |
| Cristina | Carlos |

---

## Video Formats

| Format | Words | Style | Best For |
|--------|-------|-------|----------|
| **Snapchat Hook** | 4-6 | Extreme close-up, whisper energy | Opening hooks on Snapchat/TikTok |
| **Hook** | 6-10 | Scroll-stopping, direct to camera | First 5 seconds of any video |
| **Reveal** | 15-20 | Setup + twist | Product reveals, before/after |
| **Demo** | 10-15 | Show something in action | Product demonstrations |
| **CTA** | 8-12 | Call to action, urgency | Closing clips, ad endings |
| **Split** | 2 phrases | Contrasting poses | Comparison content |

---

## Architecture Highlights

- **No Google Drive dependency** — All media flows through Cloudinary CDN
- **Airtable as character memory** — Characters persist across sessions with appearance descriptions, reference images, and voice IDs
- **Multi-model fallback chains** — Gemini cycles through 3-pro, 2.0-flash-exp; Kling cycles through v2.0-pro, v1.6-pro, v1.6-std, v1.5-std
- **Polling with retry** — Kling: 35 retries x 15s; WaveSpeed: 25 retries x 8s — handles variable AI processing times
- **Script calibration** — All templates are word-count calibrated so ElevenLabs produces exactly ~5 seconds of audio

---

## License

Private project by [@rafaelalberola](https://github.com/rafaelalberola).
