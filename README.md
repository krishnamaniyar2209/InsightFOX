# InsightFOX 🦊 – Your Smart Research Assistant

## 📌 Overview

**InsightFOX** is an AI-powered research assistant that fetches real-time web results, rates them for relevance using a local LLM (LLaMA 3.2 via Ollama), and generates a grounded answer from the retrieved context. Built as a final project for **CS676 – Algorithms for Data Science**, it offers a lightweight interface for fast, informed insights — with no cloud LLM and no API keys.

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

| Component | Technology |
|----------|------------|
| Frontend | Streamlit |
| Backend | FastAPI, Uvicorn, Python |
| AI Model | Ollama (LLaMA 3.2) — invoked as a local subprocess |
| Scraping | Selenium (headless Chrome), BeautifulSoup |
| Audio | Google Text-to-Speech (gTTS) |
| Logging | Loguru |

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
├── helper.py               # Core logic: Ollama runner, scraping, rating, TTS
├── requirements.txt        # Python dependencies
├── .gitignore
└── README.md               # Project documentation

Generated at runtime (gitignored):
├── output.mp3              # gTTS audio for the latest answer
└── logs/app.log            # Loguru output
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

## 🧪 How It Works

1. **User enters a query** in the chat UI.
2. **DuckDuckGo is scraped** (headless Chrome → BeautifulSoup) for the top *N* results. Each result's title, link, and **search snippet** are extracted — the snippet comes from DuckDuckGo, not from the model.
3. **Each result is rated 1–5** by the local LLaMA 3.2 model for relevance and quality, and the results are sorted by that score.
4. **The snippets are concatenated as context** and sent to the model, which generates the final grounded answer.
5. **Results are displayed** in a reference table with star ratings and clickable links.
6. **Text-to-Speech** renders the answer to `output.mp3` for playback in the UI.

> **Chatbot Only Mode** (sidebar toggle) skips steps 2–3 entirely and sends the prompt straight to the model.

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

## ⚠️ Known Limitations

1. **Article rating is serial and blocking.** `invoke_free_news_search` is declared `async` but never awaits — both Selenium and the Ollama subprocess block. Each result triggers a separate `ollama run` call with a 12-second timeout, executed one after another, so a 10-result search can hold the FastAPI event loop for well over a minute. Batching all ratings into a single prompt, or dispatching them to a thread pool, is the fix.
2. **`/rate-query` is unused.** The endpoint works but nothing calls it. Either surface it in the UI or remove it.
3. **`requirements.txt` is an unpruned `pip freeze`.** It includes `openai`, `serpapi`, and `google_search_results`, none of which are imported anywhere — leftovers from an earlier iteration that used hosted APIs. They are misleading on a project whose premise is local-only inference, and should be removed. `python-dotenv` and `aiohttp` are also unused.
4. **Audio playback is unconditional.** `st.audio("output.mp3")` renders even when the request failed and no MP3 was written, which raises on a first run that errors out.
5. **Scraping is brittle by nature.** The parser depends on DuckDuckGo's `result__body` / `result__a` / `result__snippet` class names. If the HTML endpoint changes, search silently returns zero results.

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

- Krishna Kirit Maniyar [@krishnamaniyar2209](https://github.com/krishnamaniyar2209)

---

## 📬 Contact

📧 maniyarkrishnakm22@gmail.com  
🔗 [LinkedIn](https://www.linkedin.com/in/krishnamaniyar/) · [Portfolio](https://krishnamaniyar2209.github.io/)  
📘 Created for Pace University | MS in Data Science
