# Spotify Discovery — AI Review-Analysis Engine

Turns thousands of live public reviews into structured product insight. Scrapes reviews from four sources in real time, then analyses **all** of them with a map-reduce pipeline on the Anthropic API to surface why users struggle to discover new music.

**Live app:** https://spotify-review-engine.streamlit.app/

## What it does

1. **Collect** — pulls live reviews from the App Store (Apple SSR-JSON, up to 12 countries), Google Play (`google_play_scraper`), Reddit (public RSS/Atom, no credentials — r/spotify + r/musicsuggest across six discovery queries), and the Spotify Community forum.
2. **Analyze** — reviews are interleaved across sources for balance, split into batches of 100, and sent to Claude. Each batch returns themes, frustrations, unmet needs, behavioural patterns, and segments (the *map* step); the batches are then synthesised into one unified analysis (the *reduce* step).
3. **Insight** — renders an insights dashboard (summary, key themes, top frustrations, user segments, unmet needs, why discovery fails, repetitive-listening causes, AI opportunities) with a downloadable JSON export.

## Why map-reduce

A single model call can't hold thousands of reviews, and truncating to a sample biases the findings. Batching every review (balanced across sources) and then synthesising the batch results means the analysis is grounded in the full corpus while each call stays within context limits.

## Tech

Python · Streamlit · Anthropic API (`claude-sonnet-4-5`) · `requests` · `beautifulsoup4` · `google-play-scraper`

## Run locally

```bash
pip install -r requirements.txt
# set your key (or use a .env file / Streamlit secrets)
export ANTHROPIC_API_KEY="sk-ant-..."
streamlit run app.py
```

The app reads `ANTHROPIC_API_KEY` from Streamlit secrets or the environment. No data is stored — every run fetches live public data.
