🚀 Overview

This project implements an automated quiz-solving system for the IITM TDS “LLM Analysis Quiz” project.
It exposes a POST /quiz API endpoint that:

Receives a quiz URL + student credentials

Loads the quiz page using a headless browser (Playwright)

Extracts question, links, downloadable files, and rendered DOM content

Uses Google Gemini to:

Understand the question

Plan a solution strategy

Perform data analysis / extraction / computation

Computes the required answer

Submits the answer to the quiz’s own submit endpoint

Follows quiz chaining until all tasks are solved

This system supports data scraping, PDF/CSV/Excel parsing, API calls, data cleaning, statistical analysis, and visualization.

⚙️ Tech Stack
Core Components

FastAPI — API server

Playwright — Headless browser to render JavaScript quizzes

Google Gemini (via google-generativeai) — LLM used for reasoning + computation

Pandas / PyPDF / OpenPyXL — Data processing

Matplotlib / Base64 Images — Visualization support

Async I/O (aiohttp) — Async network requests and submissions

📌 Environment Variables

Create a .env file with:

# Student credentials
EMAIL=your_email_here
SECRET=your_secret_here

# Google Gemini API
GEMINI_API_KEY=your_gemini_key_here
GEMINI_MODEL=gemini-2.0-flash

# Optional
DEBUG=False


Your code uses Gemini, so Claude or Anthropic keys are NOT needed.

🧠 LLM Used — Google Gemini

Your project uses:

google.generativeai

Model configured in config.py:

GEMINI_MODEL = "gemini-2.0-flash"


Gemini is used in two stages:

Question Analysis

Extract submit URL

Identify required operations

Determine answer type (number/string/object/etc.)

Identify file download requirements

Answer Computation

Analyze data (PDF/CSV/Excel/HTML table)

Perform transformations & calculations

Produce exact final answer

🔍 API Endpoints
POST /quiz

Starts a quiz-solving session.

Request:
{
  "email": "your_email",
  "secret": "your_secret",
  "url": "https://example.com/quiz-123"
}

Responses:

200 OK — Quiz accepted and processing starts

400 — Invalid JSON

403 — Secret mismatch

This endpoint internally triggers:

Browser navigation

Content extraction

Gemini analysis

File downloads

Data processing

Answer submission

Quiz chaining

GET /health

Simple health check.

🧩 Quiz Solving Workflow
1. Fetch quiz page

Using Playwright (browser_handler.py):

Load JS-rendered HTML

Extract text, DOM, links, downloadable files

2. LLM Analysis (Gemini)

quiz_solver.py → analyze_question()

Gemini receives the full page content and returns:

{
  "question_summary": "...",
  "task_type": "...",
  "download_required": true,
  "download_url": "...",
  "file_type": "pdf/csv/xlsx/json",
  "operations": ["sum", "filter", "mean"],
  "answer_type": "number",
  "submit_url": "...",
  "solution_strategy": "..."
}

📁 Project Structure
├── app.py               # FastAPI server
├── quiz_solver.py       # Main orchestrator
├── browser_handler.py   # Headless browser logic
├── data_processor.py    # PDF/CSV/Excel/JSON processing
├── visualizer.py        # Chart generation + base64 output
├── config.py            # Env + settings
├── requirements.txt
├── .env.example
└── README.md

📦 Supported Answer Types

Your solver automatically handles:

Numbers

Strings

Booleans

Objects (JSON)

Base64 encoded images (charts)

🧪 Local Testing

Start server:

python app.py


Send test request:

curl -X POST http://localhost:8000/quiz \
  -H "Content-Type: application/json" \
  -d "{\"email\":\"YOUR_EMAIL\",\"secret\":\"YOUR_SECRET\", \"url\":\"https://tds-llm-analysis.s-anand.net/demo\"}"

📜 License

This project is released under the MIT License (required by IITM TDS).
