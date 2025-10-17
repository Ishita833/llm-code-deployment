# LLM-Driven Code Generation and Deployment Service

This project is a FastAPI-based service that automates code generation using Large Language Models (LLM) like Gemini, processes task briefs, handles attachments, and deploys generated code to GitHub Pages. It supports iterative development rounds, with full generation in Round 1 and surgical updates in subsequent rounds to preserve existing functionality.

## Features

- **Task Reception**: Accepts JSON task requests via the `/ready` endpoint, including briefs, attachments (images, Markdown, CSV), and metadata.
- **LLM Code Generation**: Integrates with the Gemini API to generate or update single-file web apps (`index.html` with Tailwind CSS) and `README.md` based on task briefs.
- **Attachment Management**: Downloads and saves attachments (via data URIs or URLs) to the repository, with support for images in LLM prompts.
- **GitHub Deployment**: Creates or clones repositories, commits files, pushes changes, and configures GitHub Pages for live deployment.
- **Round-Based Iteration**: Full code generation in Round 1; minimal, structure-preserving updates in Round 2+ to implement feedback or changes.
- **Notification System**: Sends deployment details (repo URL, commit SHA, pages URL) to an evaluation server.
- **Concurrency and Logging**: Limits concurrent tasks, provides detailed logging, and includes monitoring endpoints (`/status`, `/health`, `/logs`).
- **Docker Support**: Containerized setup for easy deployment, e.g., on Hugging Face Spaces.

## Technologies Used

- **Python 3.10**: Core language for the service.
- **FastAPI**: API framework for handling requests.
- **Pydantic**: For settings, models, and validation.
- **GitPython**: Git operations for repository management.
- **httpx**: Asynchronous HTTP client for API calls and downloads.
- **Gemini API**: LLM for code generation and updates.
- **Uvicorn**: ASGI server for running the FastAPI app.
- **Docker**: For containerization.
- **Tailwind CSS**: Used in generated web apps for responsive design.

## Setup and Installation

### Prerequisites

- Python 3.10+
- Git
- Docker (optional)
- GitHub Personal Access Token (with `repo` scope)
- Gemini API Key
- System dependencies: `gcc`, `g++`, `cmake`, `git` (installed in Docker)

### Installation

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/<your-username>/<repo-name>.git
   cd <repo-name>
   ```

2. **Install Dependencies**:

   ```bash
   pip install -r requirements.txt
   ```

3. **Configure Environment Variables**: Create a `.env` file:

   ```env
   GEMINI_API_KEY=your-gemini-key
   GITHUB_TOKEN=your-github-token
   GITHUB_USERNAME=your-username
   STUDENT_SECRET=your-auth-secret
   ```

4. **Run Locally**:

   ```bash
   uvicorn main:app --host 0.0.0.0 --port 7860
   ```

5. **Run with Docker**:

   ```bash
   docker build -t llm-code-deploy .
   docker run -p 7860:7860 --env-file .env llm-code-deploy
   ```

## Usage

1. **Submit a Task**: POST to `/ready` with JSON payload (examples in `json-post-requests.txt`):

   ```bash
   curl -X POST http://localhost:7860/ready -H "Content-Type: application/json" -d '{"task": "example-task", "email": "user@example.com", "secret": "your-secret", "round": 1, "brief": "Create a simple web app", "evaluation_url": "https://eval.com/notify", "nonce": "abc123", "attachments": []}'
   ```

2. **Monitor**:

   - Status: `curl http://localhost:7860/status`
   - Health: `curl http://localhost:7860/health`
   - Logs (last 200 lines): `curl http://localhost:7860/logs`

## Project Structure

```
.
├── Dockerfile              # Multi-stage Docker setup for security and efficiency
├── main.py                 # Core FastAPI app logic
├── requirements.txt        # Python dependencies
├── json-post-requests.txt  # Sample task request payloads
├── .env                    # Environment configuration (gitignore)
├── logs/                   # Runtime logs
└── generated_tasks/        # Temporary storage for generated files
```

## Workflow

1. **Receive Task**: Validate secret, queue for processing.
2. **Process Brief**: Use Gemini to generate/update code; handle attachments.
3. **Deploy**: Setup repo, commit, push, enable Pages.
4. **Notify**: POST results to evaluation URL.

## Example Tasks

- **Captcha Solver**: Generates a web app handling image URLs for CAPTCHA solving.
- **Markdown to HTML**: Converts attached Markdown to rendered HTML.
- **Sum of Sales**: Processes CSV, computes sales sum, displays in Bootstrap-styled page.
- **Markdown Summary**: Extracts values from Markdown and displays them.

See `json-post-requests.txt` for JSON examples.

## Contributing

Fork, make changes, and submit pull requests.