# Presenton - Project Context for Claude

## Project Overview
**Presenton** is an open-source AI presentation generator (alternative to Gamma, Beautiful.AI, Decktopus).
- **Repository**: https://github.com/shafenkhan/presenton (fork of https://github.com/presenton/presenton)
- **Purpose**: Generate professional presentations using AI with full local control
- **Stack**: FastAPI (Python 3.11) + Next.js 14 + TypeScript

## Development Setup

### Architecture
- **Backend**: `servers/fastapi/` - FastAPI server (port 8000)
  - Dependencies managed with `uv` (Python package manager)
  - Virtual env: `servers/fastapi/.venv`
- **Frontend**: `servers/nextjs/` - Next.js app (port 3000)
  - Dependencies managed with `npm`
- **MCP Server**: Built-in MCP server (port 8001) for Claude integration
- **Data Storage**: `app_data/` directory (gitignored, for presentations & config)

### Running Locally
```bash
# Start everything (from project root)
export APP_DATA_DIRECTORY=/Users/shafenkhan/projects/presenton/app_data
node start.js --dev
```

## Configuration

### Environment Variables (.env - not committed)
```bash
# LLM Configuration
LLM="custom"  # Using OpenRouter
CUSTOM_LLM_URL="https://openrouter.ai/api/v1"
CUSTOM_LLM_API_KEY="sk-or-v1-..."
CUSTOM_MODEL="google/gemini-2.0-flash-exp:free"

# Image Generation
IMAGE_PROVIDER="gemini_flash"
GOOGLE_API_KEY="..."  # For Gemini images (or use Pexels free)

# App Settings
CAN_CHANGE_KEYS="true"  # Allow UI configuration
APP_DATA_DIRECTORY="/Users/shafenkhan/projects/presenton/app_data"
```

### Persistent Storage
- **Configuration**: `app_data/userConfig.json` (auto-generated from .env)
- **Presentations**: `app_data/{presentation-id}/` (generated presentations)
- **Templates**: `servers/nextjs/presentation-templates/` (HTML/Tailwind templates)

## Git Workflow

### Remotes
- **origin**: `git@github.com:shafenkhan/presenton.git` (your fork, push here)
- **upstream**: `https://github.com/presenton/presenton.git` (original repo, pull updates)

### Sync with Upstream
```bash
git fetch upstream
git merge upstream/main
git push origin main
```

### Branch Strategy
- `main` - stable, synced with upstream
- `feature/*` - new features
- `custom/*` - custom modifications

## Key Features

### AI Providers Supported
- OpenAI (GPT-4, GPT-3.5 + DALL-E 3)
- Google Gemini (2.0 Flash, 1.5 Pro + Gemini Flash images)
- Anthropic Claude (3.5 Sonnet)
- Ollama (local models)
- **OpenRouter** (unified API for all models) ← Current setup

### Image Providers
- DALL-E 3 (OpenAI)
- Gemini Flash (Google)
- Pexels (free API)
- Pixabay (free API)

### Export Formats
- PowerPoint (.pptx)
- PDF

### MCP Integration
Built-in Model Context Protocol server at `servers/fastapi/mcp_server.py`
- Can be used with Claude Code/Desktop for presentation generation
- Exposes tools for creating presentations programmatically

## Custom Development

### Creating Custom Templates
1. Templates located: `servers/nextjs/presentation-templates/`
2. Built with HTML + Tailwind CSS
3. Can upload existing PPTX to generate template design

### API Usage
```bash
# Generate presentation via API
curl -X POST http://localhost:8000/api/v1/ppt/presentation/generate \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Introduction to Machine Learning",
    "n_slides": 5,
    "template": "general",
    "export_as": "pptx"
  }'
```

## Documentation
- **Official Docs**: https://docs.presenton.ai
- **API Docs**: https://docs.presenton.ai/using-presenton-api
- **Worklog**: See `WORKLOG.md` for session notes
- **Learnings**: See `LEARNINGS.md` for insights & decisions
- **Blog Ideas**: See `BLOG-IDEAS.md` for content ideas

## Important Files
- `start.js` - Main entry point (starts all servers)
- `servers/fastapi/server.py` - FastAPI backend
- `servers/fastapi/mcp_server.py` - MCP server
- `servers/nextjs/app/` - Next.js pages
- `docker-compose.yml` - Docker deployment config

## Dependencies

### Python (3.11)
- FastAPI, Uvicorn - Web framework
- OpenAI, Anthropic, Google GenAI - LLM clients
- python-pptx - PowerPoint generation
- Docling, PDFPlumber - Document processing
- ChromaDB - Vector storage
- NLTK - Natural language processing

### Node.js
- Next.js 14 - React framework
- Tailwind CSS + Radix UI - Styling
- Redux Toolkit - State management
- Tiptap - Rich text editor
- Puppeteer - PDF generation

## Notes
- App data is stored locally in `app_data/` (gitignored)
- Free tier limits: ~200 presentations/day with Gemini 2.0 Flash
- Can switch models in UI if `CAN_CHANGE_KEYS=true`
- MCP server runs alongside main app for Claude integration

## Next Steps / TODOs
- [ ] Configure OpenRouter API key
- [ ] Test presentation generation
- [ ] Explore custom template creation
- [ ] Document custom workflows
- [ ] Write blog posts about usage

---

**Last Updated**: 2025-11-16
**Status**: Initial setup complete, ready for API key configuration
