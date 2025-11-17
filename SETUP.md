# Presenton - Local Development Setup Guide

Quick start guide for running Presenton locally on macOS with OpenRouter.

---

## Prerequisites

- macOS (tested on M1 Pro)
- Python 3.11 (not 3.12+)
- Node.js v25+ and npm
- Git

---

## Quick Setup (5 minutes)

### 1. Clone & Install Dependencies

```bash
# Clone the repo (or your fork)
cd /Users/shafenkhan/projects
git clone https://github.com/shafenkhan/presenton.git
cd presenton

# Install uv (Python package manager)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install Python dependencies
cd servers/fastapi
~/.local/bin/uv sync
cd ../..

# Install Node.js dependencies
cd servers/nextjs
npm install
cd ../..

# Create app data directory
mkdir -p app_data
```

### 2. Configure Environment

```bash
# Copy example env file
cp .env.example .env

# Edit .env and add your API keys
# At minimum, you need:
# - CUSTOM_LLM_API_KEY (OpenRouter)
# - GOOGLE_API_KEY (for Gemini images, or use Pexels)
```

**Get API Keys** (all free):
- OpenRouter: https://openrouter.ai/keys
- Google AI Studio: https://aistudio.google.com/app/apikey
- Pexels (optional): https://www.pexels.com/api/

### 3. Run the App

```bash
# Export app data directory
export APP_DATA_DIRECTORY=/Users/shafenkhan/projects/presenton/app_data

# Start all servers (FastAPI + Next.js + MCP)
node start.js --dev
```

### 4. Open in Browser

Navigate to: **http://localhost:3000**

---

## Configuration

### Recommended Free Setup

**Text Generation**: Gemini 2.0 Flash via OpenRouter
```bash
LLM="custom"
CUSTOM_LLM_URL="https://openrouter.ai/api/v1"
CUSTOM_LLM_API_KEY="sk-or-v1-..."
CUSTOM_MODEL="google/gemini-2.0-flash-exp:free"
```

**Image Generation**: Gemini Flash or Pexels
```bash
# Option 1: Gemini Flash (requires Google API key)
IMAGE_PROVIDER="gemini_flash"
GOOGLE_API_KEY="..."

# Option 2: Pexels (free, no Google key needed)
IMAGE_PROVIDER="pexels"
PEXELS_API_KEY="..."
```

### Alternative Providers

<details>
<summary>OpenAI (GPT-4 + DALL-E 3)</summary>

```bash
LLM="openai"
OPENAI_API_KEY="sk-..."
OPENAI_MODEL="gpt-4o"
IMAGE_PROVIDER="dall-e-3"
```
</details>

<details>
<summary>Anthropic Claude</summary>

```bash
LLM="anthropic"
ANTHROPIC_API_KEY="sk-ant-..."
ANTHROPIC_MODEL="claude-3-5-sonnet-20241022"
IMAGE_PROVIDER="pexels"  # Claude doesn't generate images
PEXELS_API_KEY="..."
```
</details>

<details>
<summary>Ollama (Local Models)</summary>

```bash
# First install Ollama: https://ollama.ai
# Pull a model: ollama pull llama3.2:3b

LLM="ollama"
OLLAMA_URL="http://localhost:11434"
OLLAMA_MODEL="llama3.2:3b"
IMAGE_PROVIDER="pexels"
PEXELS_API_KEY="..."
```
</details>

---

## Git Workflow

### Remotes Setup
```bash
# Origin = your fork
git remote add origin git@github.com:shafenkhan/presenton.git

# Upstream = original repo (for updates)
git remote add upstream https://github.com/presenton/presenton.git
```

### Sync with Upstream
```bash
# Pull latest changes from original repo
git fetch upstream
git merge upstream/main

# Push to your fork
git push origin main
```

---

## Development Workflow

### Running in Dev Mode
```bash
export APP_DATA_DIRECTORY=/Users/shafenkhan/projects/presenton/app_data
node start.js --dev
```

This starts:
- FastAPI backend on **http://localhost:8000**
- Next.js frontend on **http://localhost:3000**
- MCP server on **http://localhost:8001**

### Making Changes

**Backend** (Python):
- Edit files in `servers/fastapi/`
- Auto-reloads with `--dev` flag

**Frontend** (Next.js):
- Edit files in `servers/nextjs/`
- Hot-reload enabled in dev mode

**Templates**:
- Edit/add templates in `servers/nextjs/presentation-templates/`

### Testing
```bash
# Backend tests
cd servers/fastapi
~/.local/bin/uv run pytest

# Frontend tests (if needed)
cd servers/nextjs
npm test
```

---

## Tracking Your Work

This repo includes documentation files for blog writing:

- **WORKLOG.md** - Daily work sessions and progress
- **LEARNINGS.md** - Insights, decisions, gotchas
- **BLOG-IDEAS.md** - Content ideas as you work
- **CLAUDE.md** - Project context for Claude sessions

Update these as you work to make blog writing easy later!

---

## Troubleshooting

### Python Version Issues
```bash
# Check Python version (must be 3.11)
python3.11 --version

# If not installed:
brew install python@3.11
```

### Port Already in Use
```bash
# Find process using port
lsof -i :3000  # or :8000, :8001

# Kill the process
kill -9 <PID>
```

### Dependencies Not Installing
```bash
# Backend: Clear cache and reinstall
cd servers/fastapi
rm -rf .venv
~/.local/bin/uv sync

# Frontend: Clear cache and reinstall
cd servers/nextjs
rm -rf node_modules package-lock.json
npm install
```

---

## Production Deployment

For production, use Docker:

```bash
docker run -it --name presenton \
  -p 5000:80 \
  -e LLM="custom" \
  -e CUSTOM_LLM_URL="https://openrouter.ai/api/v1" \
  -e CUSTOM_LLM_API_KEY="sk-or-v1-..." \
  -e CUSTOM_MODEL="google/gemini-2.0-flash-exp:free" \
  -e IMAGE_PROVIDER="gemini_flash" \
  -e GOOGLE_API_KEY="..." \
  -e CAN_CHANGE_KEYS="false" \
  -v "./app_data:/app_data" \
  ghcr.io/presenton/presenton:latest
```

---

## Next Steps

1. ✅ Generate your first presentation
2. 📝 Explore custom templates
3. 🔌 Try the API endpoints
4. 🧪 Experiment with different models
5. 📖 Document your learnings in WORKLOG.md

---

## Resources

- **Official Docs**: https://docs.presenton.ai
- **API Reference**: https://docs.presenton.ai/using-presenton-api
- **OpenRouter Docs**: https://openrouter.ai/docs
- **Gemini API**: https://ai.google.dev/gemini-api/docs

---

**Questions or Issues?**
- Check `LEARNINGS.md` for common gotchas
- Review `CLAUDE.md` for project context
- Open an issue on GitHub

**Last Updated**: 2025-11-16
