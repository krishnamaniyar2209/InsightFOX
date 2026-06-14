# InsightFOX 🦊 – Your Smart Research Assistant

## 📌 Overview

**InsightFOX** is an AI-powered research assistant designed to fetch real-time news results, summarize them using a local LLM (LLaMA 3.2 via Ollama), and provide relevance-based star ratings. Built as a final project for **CS676 – Algorithms for Data Science**, it offers a lightweight interface for fast, informed insights.

---

## 🚀 Features

- 🔎 Real-Time Web Search (via DuckDuckGo + Selenium)
- 🤖 Local LLM Integration (Ollama running LLaMA 3.2)
- 🧠 Contextual Answer Generation
- ⭐ Article Rating System (AI-evaluated)
- 🔊 Text-to-Speech Responses (gTTS)
- ⚡ FastAPI Backend for Decoupled Logic
- 🎨 Clean Streamlit UI with Star Ratings + Audio Playback

---

## 🛠️ Tech Stack

| Component | Technology |
|----------|------------|
| Frontend | Streamlit |
| Backend | FastAPI, Python |
| AI Model | Ollama (LLaMA 3.2) |
| Scraping | Selenium, BeautifulSoup |
| Audio | Google Text-to-Speech (gTTS) |

---

## 🧩 Project Structure

```
InsightFOX/
│
├── .idea/                  # IDE config
├── logger/                 # Logging setup
│   └── app_logger.py
├── app.py                  # Streamlit frontend
├── api.py                  # FastAPI backend for chat + search
├── helper.py               # Core logic: LLM + scraping + rating
├── output.mp3              # Audio output from gTTS
├── requirements.txt        # Python dependencies
└── README.md               # Project documentation
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

### 2. Launch the Streamlit frontend

In a new terminal:

```bash
streamlit run app.py
```

---

## 🧪 How It Works

1. **User enters a query** in the chat UI.
2. **DuckDuckGo** is scraped for latest results.
3. Each article is **summarized and rated** using the local LLaMA 3.2 model.
4. Top results are displayed with **star ratings** and AI-generated insights.
5. **Text-to-Speech** support reads the answer aloud.

---

## 🔁 API Endpoints

| Method | Route              | Description                      |
|--------|--------------------|----------------------------------|
| `GET`  | `/chat-response`   | LLM-generated answer from prompt |
| `POST` | `/search-news`     | DuckDuckGo article summaries     |

---

## ❗ Troubleshooting

- **LLM issues?** Make sure Ollama is installed and the `llama3.2` model is pulled:
  ```bash
  ollama pull llama3.2
  ```
- **WebDriver errors?** Update ChromeDriver to match your browser version.
- **Text-to-speech not working?** Ensure `gTTS` has internet access to generate MP3s.

---

## 👨‍💻 Contributors

- Krishna Kirit Maniyar [@krishnamaniyar2209](https://github.com/krishnamaniyar2209)

---

## 📬 Contact

📧 krishnamaniyarkm22@gmail.com  
🔗 [LinkedIn](https://www.linkedin.com/in/krishnamaniyar/) · [Portfolio](https://krishnamaniyar2209.github.io/)  
📘 Created for Pace University | MS in Data Science

---

## 📄 License

MIT License – free to modify and use with attribution.
