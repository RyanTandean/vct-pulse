# VCT Pre-Match Sentiment Analyzer — Project Plan

## What You're Building
A Chrome extension that automatically detects when you open a VCT livestream (Twitch/YouTube) and surfaces a pre-built community sentiment briefing card for the upcoming match — powered by a Reddit NLP pipeline on the backend.

---

## Tech Stack

| Layer | Tool | Why |
|---|---|---|
| Extension | Chrome Extension (JS) | Tab detection + sidebar UI |
| Backend | Python FastAPI | Your wheelhouse |
| Match Data | Liquipedia API (or vlr.gg unofficial fallback) | Schedule, teams, rosters |
| Sentiment Data | Reddit API via PRAW | r/ValorantCompetitive |
| NLP | VADER + HuggingFace transformers | Sentiment classification |
| Storage | PostgreSQL or SQLite | Pre-built summary cards |
| Scheduler | Cron job | Rebuild summaries before each match |
| Hosting | Railway or Render (free tier) | Simple backend hosting |

---

## Phases

### Phase 1 — Data Pipeline (Week 1)
Get the data flowing before touching any UI.

- Set up PRAW and pull posts/comments from r/ValorantCompetitive
- Filter by team names and player names mentioned in upcoming matches
- Run VADER sentiment scoring on comments
- Aggregate scores per team and per player
- Store results in a simple SQLite database
- Test with a real upcoming VCT match manually

**Milestone:** Given two team names, you can produce a sentiment score for each from Reddit data.

---

### Phase 2 — Match Schedule Integration (Week 1-2)
Connect match data so the pipeline knows what's coming up.

- Reach out to Liquipedia for API access (send email early, it takes time)
- In the meantime, use vlr.gg unofficial API as fallback
- Pull upcoming match schedule — teams, date/time, tournament
- Wire schedule into the pipeline so it auto-triggers before each match
- Set up a cron job to run the sentiment pipeline ~24hrs and ~1hr before each match

**Milestone:** Pipeline runs automatically before scheduled matches and stores pre-built summary cards.

---

### Phase 3 — Summary Card Generation (Week 2)
Turn raw sentiment scores into a readable briefing.

- Design the summary card schema (team sentiment, player highlights, top Reddit narratives, community pick)
- Use Claude/OpenAI API to turn sentiment data into 2-3 readable narrative sentences per team
- Store the final card as JSON ready to serve
- Build a simple FastAPI endpoint that returns the card for a given match

**Milestone:** Hit an API endpoint and get back a structured match briefing card as JSON.

---

### Phase 4 — Chrome Extension (Week 2-3)
Build the frontend that actually surfaces this to users.

- Set up basic Chrome extension scaffold (manifest v3)
- Add tab listener that watches for Twitch/YouTube URLs
- Cross-reference page title or URL against known VCT stream channels
- When a match is detected, fetch the pre-built summary card from your backend
- Build the sidebar UI — team sentiment bars, player highlights, narrative summary
- Auto-popup when stream is detected, collapsible when not needed

**Milestone:** Open a VCT Twitch stream, extension pops up automatically with the match briefing.

---

### Phase 5 — Polish (Week 3)
Make it demo-ready.

- Visual design pass on the sidebar (Valorant-inspired colour scheme)
- Handle edge cases — no match found, match already started, offseason
- Add a "last updated" timestamp so users know how fresh the data is
- Record a demo video of the extension auto-detecting a stream
- Write a clean README with architecture diagram for your GitHub

**Milestone:** Something you'd be proud to screen-share in an interview.

---

## Data Sources

- **Primary match data:** Liquipedia API (email them for access)
- **Fallback match data:** vlr.gg unofficial API
- **Sentiment data:** Reddit API via PRAW — r/ValorantCompetitive
- **NLP:** VADER for fast baseline, HuggingFace for richer classification if needed

---

## Interview Talking Points

- **NLP pipeline** — entity extraction, sentiment classification, aggregation across thousands of comments
- **Pre-computation pattern** — summaries built on a schedule, not on-demand, for performance and cost efficiency
- **Chrome Extension** — cross-platform tab detection, sidebar UI, manifest v3
- **RAG awareness** — "next step would be a chat interface with Reddit data injected as context"
- **Real data** — live Reddit API, real match schedules, real VCT teams

---

## Chrome Extension Gotchas

Known issues to anticipate before you hit them:

**Manifest V3**
Chrome requires Manifest V3 now. Background scripts are "service workers" which sleep when idle — no persistent connections. Fine for your use case (detect tab → fetch data → show UI) but the syntax and structure is different from older tutorials online. Make sure any guide you follow is V3-specific.

**CORS**
Your extension makes requests to your FastAPI backend from the browser. You must explicitly configure CORS headers on the backend to whitelist the extension. Easy one-liner fix but will block you completely if you forget it.

**YouTube tab detection**
Twitch is easy — just match the URL (`twitch.tv/valorant`, `twitch.tv/vct`). YouTube is trickier because the URL doesn't change when you navigate between videos, only the page title does. You'll need a `MutationObserver` watching for title changes rather than a simple URL check. Budget extra time for this.

**Sidebar UI (Side Panel API)**
The Chrome Side Panel API (the cleanest way to do a persistent sidebar) only became available in Chrome 114. It's widely available now but double check compatibility. The alternatives are injecting HTML directly into the page or using a popup — both work but are less elegant.

**Publishing vs. Local Demo**
Chrome Web Store review takes days and they're strict. For portfolio/interview purposes, demo it as an unpacked extension loaded locally — no review needed, works perfectly for screen sharing in interviews.

---

## What To Build If Liquipedia Falls Through

You still have a legitimate project:
- Reddit sentiment analyzer for VCT teams/players
- Manual match input (just type the two team names)
- Pre-built summary cards served via FastAPI
- Chrome extension that you trigger manually instead of auto-detecting

Still a strong portfolio piece. The auto-detection is a nice-to-have on top.
