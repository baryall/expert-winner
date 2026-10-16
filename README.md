# 🎬 YouTube Video Summarizer

Paste a YouTube link, click **Summarize Video**, and get a clear summary of the video. Built for students who want to get through long lectures and tutorials faster.

**How it works:** the app validates the URL → downloads the video's transcript (captions) with `youtube-transcript-api` → sends it to Google Gemini → shows a structured summary.

## Features

- Short summary, detailed summary, or study notes (key terms, concepts, review questions)
- Video title and link
- Transcript info (language, auto-generated or manual, word count)
- Long transcripts are split into chunks, summarized, then combined
- Copy-friendly text view and a `.txt` download
- Friendly messages for invalid URLs, missing transcripts, rate limits, and API errors

## Prerequisites

- Python 3.10 or newer
- A free Gemini API key from [Google AI Studio](https://aistudio.google.com/apikey)
- A GitHub account (for deployment)
- A [Streamlit Community Cloud](https://streamlit.io/cloud) account (for deployment)

## Run locally

```bash
# 1. Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Add your API key (see next section)

# 4. Start the app
streamlit run app.py
```

## Add your Gemini API key (locally)

1. Create a folder named `.streamlit` next to `app.py`.
2. Inside it, create a file named `secrets.toml` containing:

```toml
GEMINI_API_KEY = "paste-your-key-here"
```

The `.gitignore` in this project already excludes `.streamlit/secrets.toml`, so the key won't be uploaded to GitHub. The key is never hardcoded, shown in the app, or included in downloaded files.

Optional: to use a different Gemini model, add `GEMINI_MODEL = "model-name"` to the same file. The default is `gemini-2.5-flash`.

## Deploy on Streamlit Community Cloud

1. **Push to GitHub.** Create a new repository and upload `app.py`, `requirements.txt`, `README.md`, and `.gitignore`. Do **not** upload `secrets.toml`.
   ```bash
   git init
   git add .
   git commit -m "YouTube summarizer"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPO.git
   git push -u origin main
   ```
2. Go to [share.streamlit.io](https://share.streamlit.io) and click **Create app** (or **New app**).
3. Pick your repository, branch `main`, and main file `app.py`.
4. Open **Advanced settings → Secrets** and paste:
   ```toml
   GEMINI_API_KEY = "paste-your-key-here"
   ```
5. Click **Deploy**. After a minute or two you'll get a public URL.

To change the key later: app menu → **Settings → Secrets**.

## Troubleshooting

| Problem | Likely cause / fix |
|---|---|
| "The Gemini API key is missing" | Add `GEMINI_API_KEY` to `.streamlit/secrets.toml` (local) or the app's Secrets settings (deployed). |
| "Gemini rejected the request" | The key is wrong, or was revoked. Create a new one in Google AI Studio. |
| "Gemini rate limit reached" | The free tier has per-minute and daily limits. Wait a minute and retry. |
| "No transcript found" / "Subtitles are turned off" | The video has no captions. Try another video. |
| "YouTube is blocking transcript requests" | See the note below. |

### Important note about YouTube blocking cloud servers

YouTube often blocks transcript requests that come from cloud provider IP addresses (which includes Streamlit Community Cloud). A video that works on your laptop may fail once deployed. If that happens:

- Run the app locally, where it normally works fine, or
- Route transcript requests through a proxy. `youtube-transcript-api` supports this; see its documentation on proxy configuration. If you add one, store the proxy credentials in Streamlit Secrets, never in the code.

## Project files

| File | Purpose |
|---|---|
| `app.py` | The whole Streamlit application |
| `requirements.txt` | Python dependencies |
| `.gitignore` | Keeps secrets and junk out of GitHub |
| `README.md` | This guide |

## Limitations

- Needs captions to exist. It can't "watch" the video, so visual-only content isn't covered.
- Videos with more than about 60,000 spoken words (roughly 6+ hours) are rejected.
- AI summaries can contain mistakes. Check important details against the video.
