---
title: LLM Analysis Quiz Solver
emoji: 🏃
colorFrom: red
colorTo: blue
sdk: docker
pinned: false
app_port: 7860
---

# LLM Analysis – Autonomous Quiz Solver Agent

An intelligent autonomous agent leveraging **LangGraph** and **LangChain** to solve data-centric quizzes. The system orchestrates web scraping, data processing, analysis, and visualization tasks using Google’s **Gemini 2.5 Flash** model to guide tool selection and decision-making.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Features](#features)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
- [Tools & Capabilities](#tools--capabilities)
- [Docker Deployment](#docker-deployment)
- [Operational Workflow](#operational-workflow)
- [Design Considerations](#design-considerations)
- [License](#license)

---

## 🔍 Overview

Developed as part of the **Tools in Data Science (TDS)** course project, this system autonomously handles multi-step quizzes that involve:

- **Data Acquisition:** Web scraping, API requests, file downloads  
- **Data Preparation:** Cleaning text, PDFs, CSVs, and other formats  
- **Data Analysis:** Filtering, aggregation, statistical computations, ML integration  
- **Data Visualization:** Generating charts, reports, and narrative summaries  

Quizzes are submitted via a REST API. The agent navigates sequential pages, executes tasks using LLM reasoning and specialized tools, and submits results back to the evaluation server.

---

## 🏗️ Architecture

The system uses a **LangGraph state machine**:

┌─────────────┐
│ FastAPI │ ← Receives POST requests with quiz URLs
│ Server │
└──────┬──────┘
│
▼
┌─────────────┐
│ Agent │ ← LangGraph orchestrator with Gemini 2.5 Flash
│ (LLM) │
└──────┬──────┘
│
├────────────┬────────────┬─────────────┬──────────────┐
▼ ▼ ▼ ▼ ▼
[Scraper] [Downloader] [Code Exec] [POST Req] [Add Deps]


**Core Components:**

- **FastAPI Server (`main.py`)** – Handles POST requests, validates secrets, triggers agent execution.  
- **LangGraph Agent (`agent.py`)** – State machine coordinating tool usage and decision-making.  
- **Tools Package (`tools/`)** – Modular tools for scraping, downloading, code execution, API requests, and dependency management.  
- **LLM** – Google Gemini 2.5 Flash with rate limiting (9 requests/min).  

---

## ✨ Features

- Autonomous multi-step problem solving across quiz pages  
- Dynamic JavaScript rendering via Playwright  
- Python code generation and execution for analysis tasks  
- Flexible handling of PDFs, CSVs, images, and other data formats  
- Self-installing Python dependencies  
- Robust error handling and retries  
- Docker-ready for cloud deployment (e.g., HuggingFace Spaces)  
- API rate-limiting with exponential backoff  

---

## 📁 Project Structure


LLM-Analysis-TDS-Project-2/
├── agent.py # LangGraph orchestration
├── main.py # FastAPI server
├── pyproject.toml # Dependencies & configuration
├── Dockerfile # Container setup with Playwright
├── .env # Environment variables (not committed)
├── tools/
│ ├── init.py
│ ├── web_scraper.py # Playwright HTML renderer
│ ├── code_generate_and_run.py # Python code executor
│ ├── download_file.py # File downloader
│ ├── send_request.py # POST request handler
│ └── add_dependencies.py # Dynamic package installer
└── README.md


---

## 📦 Installation

### Prerequisites

- Python 3.12+  
- `uv` package manager **or** `pip`  
- Git  

### Step 1: Clone Repository

```bash
git clone <your-github-link-here>
cd LLM-Analysis-TDS-Project-2
Step 2: Install Dependencies

Option A – Using uv (Recommended)

pip install uv
uv sync
uv run playwright install chromium
uv run main.py


Option B – Using pip

python -m venv venv
.\venv\Scripts\activate      # Windows
# source venv/bin/activate    # macOS/Linux
pip install -e .
playwright install chromium
python main.py


Server runs at http://0.0.0.0:7860.

⚙️ Configuration
Environment Variables (.env)
EMAIL=your.email@example.com
SECRET=your_secret_string
GOOGLE_API_KEY=your_gemini_api_key_here

Gemini API Key Setup

Visit Google AI Studio

Generate a new API key

Add it to your .env file

🚀 Usage
Local Development
uv run main.py   # or python main.py

Test POST Request
curl -X POST http://localhost:7860/solve \
  -H "Content-Type: application/json" \
  -d '{
    "email": "your.email@example.com",
    "secret": "your_secret_string",
    "url": "https://tds-llm-analysis.s-anand.net/demo"
  }'


Expected response:

{
  "status": "ok"
}

🌐 API Endpoints
POST /solve

Triggers the agent to process a quiz.

Request Body:

{
  "email": "your.email@example.com",
  "secret": "your_secret_string",
  "url": "https://example.com/quiz-123"
}


Responses:

Status	Description
200	Secret verified, agent started
400	Invalid JSON payload
403	Invalid secret
GET /healthz

Health monitoring endpoint:

{
  "status": "ok",
  "uptime_seconds": 3600
}

🛠️ Tools & Capabilities

Web Scraper (get_rendered_html) – Playwright-based JS rendering

File Downloader (download_file) – Saves PDFs, CSVs, images, etc.

Code Executor (run_code) – Runs Python scripts in isolation

POST Request (post_request) – Handles submission endpoints

Dependency Installer (add_dependencies) – Installs required packages dynamically

🐳 Docker Deployment
docker build -t llm-analysis-agent .
docker run -p 7860:7860 \
  -e EMAIL="your.email@example.com" \
  -e SECRET="your_secret_string" \
  -e GOOGLE_API_KEY="your_api_key" \
  llm-analysis-agent


HuggingFace Spaces Deployment

Create a Docker-based Space

Push repository

Add secrets (EMAIL, SECRET, GOOGLE_API_KEY)

Space auto-builds and deploys

🧠 Operational Workflow

FastAPI receives POST request and validates secret

LangGraph agent initializes state machine

Agent iteratively:

Reads quiz instructions

Executes tools (scraping, file download, code execution)

Submits answers and checks correctness

Continues until no new URL remains, then returns "END"

📝 Design Considerations

LangGraph over sequential execution: Flexible routing and decision-making

Background processing: Avoids HTTP timeouts

Tool modularity: Independent testing and debugging

Rate-limiting: Prevents API quota exhaustion

Dynamic code execution: Handles complex data tasks

Playwright: Supports JavaScript-heavy pages

uv for dependencies: Fast, adaptive package installation

📄 License

MIT License. See LICENSE
 for details.

Author: Hana Muhammed
Course: Tools in Data Science (TDS)
Institution: IIT Madras

For issues, please open an issue on the GitHub repository.
