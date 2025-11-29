title: LLM Analysis Quiz Solver
emoji: 🏃
colorFrom: red
colorTo: blue
sdk: docker
pinned: false
app_port: 7860
LLM Analysis – Autonomous Quiz Solver Agent






LLM Analysis is an intelligent, autonomous agent built with LangGraph and LangChain. It is designed to solve data-driven quizzes by orchestrating web scraping, data processing, analysis, and visualization tasks. The system utilizes Google’s Gemini 2.5 Flash model to manage tool selection and automated decision-making.

📋 Table of Contents

Project Overview

System Architecture

Key Features

Project Structure

Installation

Configuration

Usage

API Endpoints

Available Tools

Docker Deployment

Operational Workflow

Design Considerations

License

🔍 Project Overview

This system was developed for the Tools in Data Science (TDS) course project to autonomously solve multi-step quizzes involving:

Data Acquisition: Web scraping, API interactions, file downloads

Data Preparation: Cleaning and structuring text, PDFs, CSVs, and other formats

Data Analysis: Filtering, aggregation, statistical operations, and machine learning integration

Data Visualization: Generating charts, reports, and narrative summaries

The agent accepts quiz URLs via a REST API, navigates through sequential pages, executes tasks using LLM reasoning combined with specialized tools, and submits results to the evaluation server.

🏗️ System Architecture

The system employs a LangGraph state machine architecture:

┌─────────────┐
│   FastAPI   │  ← Receives POST requests with quiz URLs
│   Server    │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Agent     │  ← LangGraph orchestrator powered by Gemini 2.5 Flash
│   (LLM)     │
└──────┬──────┘
       │
       ├────────────┬────────────┬─────────────┬──────────────┐
       ▼            ▼            ▼             ▼              ▼
   [Scraper]   [Downloader]  [Code Exec]  [POST Req]  [Add Deps]

Core Components

FastAPI Server (main.py) – Handles POST requests, validates secrets, and triggers agent execution.

LangGraph Agent (agent.py) – Orchestrates tool usage and manages decision-making through a state machine.

Tools Package (tools/) – Modular tools for scraping, downloading, code execution, API requests, and dependency management.

LLM – Google Gemini 2.5 Flash with a rate limit of nine requests per minute.

✨ Key Features

Autonomous Multi-Step Problem Solving: Sequentially processes multiple quiz pages.

Dynamic JavaScript Rendering: Utilizes Playwright to handle client-side rendered pages.

Python Code Generation & Execution: Automates data processing, analysis, and visualization tasks.

Flexible Data Handling: Supports PDFs, CSVs, images, and other formats.

Self-Installing Dependencies: Automatically installs required Python packages.

Robust Error Handling: Retries failed tasks within defined time limits.

Docker-Ready Deployment: Compatible with cloud platforms including HuggingFace Spaces.

Rate Limiting: Ensures compliance with API quotas using exponential backoff.

📁 Project Structure
LLM-Analysis-TDS-Project-2/
├── agent.py                     # LangGraph orchestration and state machine
├── main.py                      # FastAPI server with /solve endpoint
├── pyproject.toml               # Project dependencies and configuration
├── Dockerfile                   # Container configuration with Playwright
├── .env                         # Environment variables (not committed)
├── tools/
│   ├── __init__.py
│   ├── web_scraper.py           # Playwright-based HTML renderer
│   ├── code_generate_and_run.py # Python code executor
│   ├── download_file.py         # File downloader
│   ├── send_request.py          # HTTP POST request handler
│   └── add_dependencies.py      # Dynamic package installer
└── README.md

📦 Installation
Prerequisites

Python 3.12 or higher

uv package manager
 (recommended) or pip

Git

Step 1: Clone the Repository
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
.\venv\Scripts\activate       # Windows
# source venv/bin/activate    # macOS/Linux
pip install -e .
playwright install chromium
python main.py


Server will be available at http://0.0.0.0:7860.

⚙️ Configuration
Environment Variables (.env)
EMAIL=your.email@example.com
SECRET=your_secret_string
GOOGLE_API_KEY=your_gemini_api_key_here

Gemini API Key

Navigate to Google AI Studio

Generate a new API key

Insert the key into your .env file

🚀 Usage
Local Development
uv run main.py    # Or use python main.py

Test the POST Endpoint
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

Triggers the autonomous agent.

Request Body:

{
  "email": "your.email@example.com",
  "secret": "your_secret_string",
  "url": "https://example.com/quiz-123"
}


Responses:

Status	Description
200	Secret verified; agent started
400	Invalid JSON payload
403	Invalid secret
GET /healthz

Health monitoring endpoint:

{
  "status": "ok",
  "uptime_seconds": 3600
}

🛠️ Available Tools

Web Scraper (get_rendered_html) – Playwright-based JavaScript rendering

File Downloader (download_file) – Downloads PDFs, CSVs, images, etc.

Code Executor (run_code) – Executes Python scripts in isolation

POST Request (post_request) – Submits JSON payloads to endpoints

Dependency Installer (add_dependencies) – Dynamically installs required packages

🐳 Docker Deployment
docker build -t llm-analysis-agent .
docker run -p 7860:7860 \
  -e EMAIL="your.email@example.com" \
  -e SECRET="your_secret_string" \
  -e GOOGLE_API_KEY="your_api_key" \
  llm-analysis-agent

HuggingFace Spaces Deployment

Create a Docker-based Space

Push this repository

Configure secrets (EMAIL, SECRET, GOOGLE_API_KEY)

Space builds and deploys automatically

🧠 Operational Workflow

FastAPI receives a POST request and validates the secret.

LangGraph agent initializes a state machine.

The agent iteratively:

Analyzes the current quiz page

Executes tools for scraping, downloading, or code execution

Submits answers and verifies correctness

Process continues until no further quiz URLs are available; the agent returns "END".

📝 Design Considerations

LangGraph Architecture – Enables flexible routing and complex decision-making

Background Processing – Prevents HTTP timeouts

Tool Modularity – Independent testing and debugging

Rate Limiting – Preserves API quotas (9 requests/min for Gemini)

Dynamic Code Execution – Supports complex data operations

Playwright – Handles JavaScript-intensive pages

uv Package Manager – Ensures fast and adaptive dependency management

📄 License

This project is licensed under the MIT License. See LICENSE
 for details.

Author: Hana Muhammed
Course: Tools in Data Science (TDS)
Institution: IIT Madras

For questions or issues, please open an issue on the GitHub repository# TDS_Project_2
