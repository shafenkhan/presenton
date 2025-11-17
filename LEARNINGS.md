# Presenton - Learnings & Insights

Document key insights, technical decisions, gotchas, and "aha!" moments. This becomes the knowledge base for blog posts.

---

## Architecture & Design

### FastAPI + Next.js Stack
**Decision**: Presenton uses separate FastAPI backend + Next.js frontend
- **Why**: Clean separation of concerns
  - Python excels at AI/ML integrations (OpenAI, Anthropic, Gemini clients)
  - Next.js provides great UX for presentation editing
- **Trade-off**: Requires running 2 servers (but `start.js` handles this)
- **Benefit**: Can use FastAPI as standalone API service

### MCP Server Integration
**Insight**: Presenton includes built-in MCP (Model Context Protocol) server
- **Location**: `servers/fastapi/mcp_server.py`
- **Port**: 8001
- **Use Case**: Generate presentations from Claude Code/Desktop
- **Cool Factor**: Can integrate with Claude workflows!

---

## Configuration & Setup

### Why `uv` Instead of `pip`?
**Learning**: Project uses `uv` for Python dependency management
- **Speed**: Much faster than pip (written in Rust)
- **Lock files**: `uv.lock` ensures reproducible installs
- **Virtual envs**: Auto-creates `.venv` in project
- **Gotcha**: Need Python 3.11 specifically (3.12+ breaks some deps)

### Environment Configuration Pattern
**Pattern**: Multi-layer config system
1. `.env` file (gitignored, your secrets)
2. `userConfig.json` in `app_data/` (auto-generated from .env)
3. UI can override if `CAN_CHANGE_KEYS=true`

**Why this matters**: Can ship with pre-configured keys OR let users configure
- Production: Set `.env` + `CAN_CHANGE_KEYS=false` (keys hidden)
- Development: `CAN_CHANGE_KEYS=true` (configure in UI)

---

## AI Provider Selection

### OpenRouter vs Direct APIs
**Decision**: Using OpenRouter instead of direct Gemini API
- **Pros**:
  - Single API for all models (Gemini, Claude, GPT-4, Llama)
  - Easy to switch models without code changes
  - Gemini 2.0 Flash free tier available
- **Cons**:
  - Extra hop (slight latency)
  - Rate limits (~200/day on free tier vs higher with direct API)
- **Verdict**: Perfect for development/personal use, consider direct API for production

### Gemini 2.0 Flash Free Tier Limits
**Reality Check**: Free tier is generous but has limits
- ✅ **200 presentations/day** - More than enough for personal use
- ✅ **$0 cost** - No credit card needed
- ⚠️ **8,192 token limit** - May truncate very long presentations
- ⚠️ **Data retention** - Google keeps prompts for ~55 days
- 💡 **Workaround**: Add $5 credit to OpenRouter for higher limits (don't need to spend it)

---

## Image Generation

### Image Provider Options
**Discovery**: Can mix-and-match text LLM + image provider
- Text: OpenRouter (Gemini)
- Images: Gemini Flash OR Pexels OR Pixabay OR DALL-E 3

**Best Free Combo**:
1. Text: `google/gemini-2.0-flash-exp:free` (OpenRouter)
2. Images: Gemini Flash (Google API) OR Pexels (free API)

**Gotcha**: Using Gemini for images requires separate Google API key (not OpenRouter)

---

## Git & Collaboration

### Fork vs Clone
**Decision**: Forked original repo instead of direct clone
- **Why**:
  - Persist customizations to your GitHub
  - Easy to pull upstream updates
  - Can contribute back via PRs if desired
- **Setup**:
  ```bash
  origin → your fork (push changes here)
  upstream → original repo (pull updates from here)
  ```

### What to Commit vs Ignore
**Gitignore Strategy**:
- ✅ Commit: Code, templates, documentation
- ❌ Ignore: `.env`, `app_data/`, `node_modules`, `.venv`
- 💡 **Why**: Secrets stay local, presentations stay local, code is shared

---

## Docker vs Local Development

### When to Use Each
**Docker**: Production deployment, team sharing
- One command: `docker run ...`
- Includes Nginx, pre-built frontend
- Can add GPU support for Ollama

**Local**: Development, customization
- Edit code with hot reload
- Debug easily
- Faster iteration

**Current Choice**: Local dev (can Docker-ize later)

---

## Gotchas & Tips

### Python Version Lock
⚠️ **Must use Python 3.11** (not 3.12+)
- Reason: Some dependencies (torch, transformers) need 3.11
- Check: `python3.11 --version` before setup

### Port Configuration
📌 **Default Ports**:
- 8000: FastAPI backend
- 3000: Next.js frontend
- 8001: MCP server
- 5000: Nginx (Docker only)

### APP_DATA_DIRECTORY
💡 Must export before running:
```bash
export APP_DATA_DIRECTORY=/Users/shafenkhan/projects/presenton/app_data
node start.js --dev
```

---

## Blog-Worthy Insights

1. **"Why I Chose OpenRouter Over Direct AI APIs"**
   - Cost comparison, flexibility, multi-model access

2. **"Building a Free AI Presentation Generator Stack"**
   - OpenRouter + Gemini free tier + Pexels = $0/month

3. **"MCP Integration: Connecting Presenton to Claude Code"**
   - How the built-in MCP server works

4. **"Forking Open Source: Customization vs Contribution"**
   - When to fork, when to contribute, how to stay in sync

---

## Questions to Explore

- [ ] Can we train custom templates from brand guidelines?
- [ ] How does the PPTX → template generation work?
- [ ] Can we integrate with our existing MCP infrastructure?
- [ ] What's the quality difference between Gemini vs Claude for presentations?
- [ ] Can we batch generate presentations from CSV data?

---

**Last Updated**: 2025-11-16
