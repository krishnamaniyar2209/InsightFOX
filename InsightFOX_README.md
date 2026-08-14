# 🦊 InsightFOX — Local-LLM Research Assistant

**A research assistant that searches the web, judges its own sources, and answers you — all on a local LLM, with no API keys and no cloud inference.**

![Python](https://img.shields.io/badge/Python-3.8+-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-Backend-009688?logo=fastapi&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-UI-FF4B4B?logo=streamlit&logoColor=white)
![Ollama](https://img.shields.io/badge/Ollama-LLaMA%203.2-000000?logo=ollama&logoColor=white)
![License](https://img.shields.io/badge/License-Not%20Specified-lightgrey)

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Highlights](#-highlights)
- [Demo](#-demo)
- [Features](#-features)
- [Tech Stack](#️-tech-stack)
- [How It Works](#-how-it-works)
- [Project Structure](#-project-structure)
- [Setup Instructions](#️-setup-instructions)
- [Running the Project](#-running-the-project)
- [API Endpoints](#-api-endpoints)
- [Future Improvements](#-future-improvements)
- [Troubleshooting](#-troubleshooting)
- [Contributors](#-contributors)
- [Contact](#-contact)

---

## 📌 Overview

**InsightFOX** is an AI-powered research assistant that fetches real-time web results, rates each one for relevance using a local LLM (LLaMA 3.2 via Ollama), and generates a grounded answer from the retrieved context. Built as a final project for **CS676 – Algorithms for Data Science** at Pace University, it runs entirely on local inference — no cloud LLM, no API keys, no per-request cost.

---

## ✨ Highlights

- Built a full pipeline end-to-end: web scraping → LLM-based source ranking → grounded answer generation → text-to-speech playback.
- Runs entirely on local, open-weight inference (Ollama + LLaMA 3.2) — no paid API keys needed to use it.
- Decoupled the UI (Streamlit) from the logic layer (FastAPI), so either half can be modified or swapped independently.
- Added structured, rotating logs (Loguru) to make failures traceable across several flaky external dependencies (headless Chrome, DuckDuckGo's HTML, subprocess calls to Ollama).

---

## 🎥 Demo

*Add a screenshot or short screen recording of the app here — a GIF of a query going in and a sourced, star-rated answer coming back is the single most effective thing you can add to this README.*

```
![InsightFOX demo](docs/demo.gif)
```

---

## 🚀 Features

- 🔎 Real-Time Web Search (DuckDuckGo scraped via Selenium + BeautifulSoup)
- 🤖 Local LLM Integration (Ollama running LLaMA 3.2) — no API keys, no cloud inference
- 🧠 Contextual Answer Generation from the retrieved snippets
- ⭐ AI-Evaluated Article Rating (1–5, rendered as stars)
- 🔊 Text-to-Speech Responses (gTTS)
- ⚡ FastAPI Backend for Decoupled Logic
- 🎨 Clean Streamlit UI with Star Ratings + Audio Playback
- 📝 Structured Logging via Loguru (daily rotation, 10-day retention)

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | Streamlit |
| Backend | FastAPI, Uvicorn, Python |
| AI Model | Ollama (LLaMA 3.2) — invoked as a local subprocess |
| Scraping | Selenium (headless Chrome), BeautifulSoup |
| Audio | Google Text-to-Speech (gTTS) |
| Logging | Loguru |

---

## 🧪 How It Works

1. **User enters a query** in the chat UI.
2. **DuckDuckGo is scraped** (headless Chrome → BeautifulSoup) for the top *N* results. Each result's title, link, and **search snippet** are extracted — the snippet comes from DuckDuckGo, not from the model.
3. **Each result is rated 1–5** by the local LLaMA 3.2 model for relevance and quality, and the results are sorted by that score.
4. **The snippets are concatenated as context** and sent to the model, which generates the final grounded answer.
5. **Results are displayed** in a reference table with star ratings and clickable links.
6. **Text-to-Speech** renders the answer to `output.mp3` for playback in the UI.

> **Chatbot Only Mode** (sidebar toggle) skips steps 2–3 entirely and sends the prompt straight to the model.

---

## 🧩 Project Structure

```
InsightFOX/
│
├── logger/
│   ├── __init__.py
│   └── app_logger.py       # Loguru wrapper (sync + async log methods)
├── app.py                  # Streamlit frontend
├── api.py                  # FastAPI backend for chat + search + rating
├── helper.py                # Core logic: Ollama runner, scraping, rating, TTS
├── requirements.txt        # Python dependencies
├── .gitignore
└── README.md                # Project documentation

Generated at runtime (gitignored):
├── output.mp3               # gTTS audio for the latest answer
└── logs/app.log              # Loguru output
```

---

## ⚙️ Setup Instructions

### 📦 Prerequisites

- Python 3.8+
- pip
- Chrome & ChromeDriver (compatible version)
- Ollama installed locally with `llama3.2` pulled

### ✅ Installation

```bash
# Clone the repository
git clone https://github.com/krishnamaniyar2209/InsightFOX.git
cd InsightFOX

# (Optional) Create virtual environment
python -m venv venv
venv\Scripts\activate     # On Windows
# or
source venv/bin/activate  # On macOS/Linux

# Install dependencies
pip install -r requirements.txt
```

---

## 🧠 Running the Project

### 1. Start the FastAPI server

```bash
uvicorn api:app --reload
```
This serves on `http://localhost:8000`, which is the address the Streamlit app expects.

### 2. Launch the Streamlit frontend

In a new terminal:

```bash
streamlit run app.py
```

---

## 🔁 API Endpoints

| Method | Route | Description | Used by UI |
|--------|-------|-------------|------------|
| `GET`  | `/ping` | Health check | — |
| `GET`  | `/chat-response` | LLM-generated answer from a `prompt` query parameter | ✅ |
| `POST` | `/search-news` | DuckDuckGo results with scraped snippets + AI ratings. Body: `{query, num, location, time_filter}` | ✅ |
| `GET`  | `/rate-query` | Rates a user query's clarity, specificity, and usefulness (1–5) and returns stars | ⚠️ available, not wired into the UI |

`/search-news` defaults: `num=5`, `location="us-en"`, `time_filter="d"` (past day).

---

## 🔭 Future Improvements

- **Parallelize article rating.** Ratings are currently generated one at a time — each result triggers a separate `ollama run` call, so a 10-result search can take over a minute. Batching all snippets into a single prompt, or dispatching them to a thread pool, would cut this down significantly.
- **Wire up `/rate-query`.** The endpoint rates a user's query for clarity and usefulness but isn't called from the UI yet — surfacing it could give users live feedback on how to phrase a better question.
- **Trim `requirements.txt`.** A few packages from an earlier iteration (`openai`, `serpapi`, `google_search_results`) are no longer used now that inference is fully local, and can be removed.
- **Guard audio playback** so the player only renders once a response has actually been generated.
- **Add a fallback for scraping.** DuckDuckGo's HTML structure can change without notice; a backup selector or a supported search API would make results more reliable long-term.

---

## ❗ Troubleshooting

- **LLM issues?** Make sure Ollama is installed and the `llama3.2` model is pulled:
  ```bash
  ollama pull llama3.2
  ```
  The code invokes `ollama run llama3.2:latest`, so the `latest` tag must resolve.
- **WebDriver errors?** Update ChromeDriver to match your browser version.
- **Empty results?** DuckDuckGo may be rate-limiting the headless client, or the page structure may have changed. Check `logs/app.log` — scraping failures are logged.
- **Text-to-speech not working?** Ensure `gTTS` has internet access to generate MP3s.
- **Connection refused in the UI?** The FastAPI server must be running on port 8000 before you launch Streamlit.

---

## 👨‍💻 Contributors

- Krishna Kirit Maniyar — [@krishnamaniyar2209](https://github.com/krishnamaniyar2209)

---

## 📬 Contact

📧 maniyarkrishnakm22@gmail.com
🔗 [LinkedIn](https://www.linkedin.com/in/krishnamaniyar/) · [Portfolio](https://krishnamaniyar2209.github.io/)
📘 Created for Pace University | MS in Data Science
