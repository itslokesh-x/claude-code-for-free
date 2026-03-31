<div align="center">

# ⚡ Claude Code for Free

**[🌐 Live Site & Documentation](https://itslokeshx.github.io/Claude-Code-For-Free/)**

**Run Claude Sonnet 4.5 & Opus 4.5 inside Claude Code CLI — completely free via GitHub Copilot.**

> These models were removed from VS Code Copilot's extension UI but remain fully accessible through the Copilot API. This setup bridges that gap.


</div>

---

## What is this?

GitHub recently removed **Claude Sonnet 4.5** and **Claude Opus 4.5** from the VS Code Copilot extension. However, these models are still accessible through the GitHub Copilot **API** — just not through the VS Code UI.

This project shows you how to:

1. Authenticate with GitHub Copilot via **OpenCode CLI**
2. Run a local **claude-code-proxy** that translates API formats
3. Point **Claude Code CLI** at the proxy
4. Get full agentic Claude Sonnet 4.5 / Opus 4.5 — for free

**Students get GitHub Copilot free** via the [GitHub Student Developer Pack](https://education.github.com/pack). Everyone else pays $10/month for Copilot — still much cheaper than Anthropic's API.

---

## Architecture

```
Claude Code CLI
      │
      │  Anthropic API format
      ▼
claude-code-proxy (localhost:8082)
      │
      │  OpenAI API format
      ▼
api.githubcopilot.com
      │
      │  Model response
      ▼
claude-sonnet-4.5 / claude-opus-4.5
```

**Why the proxy?** Claude Code speaks Anthropic's API format. GitHub Copilot speaks OpenAI's API format. `claude-code-proxy` is an open-source bridge (2k+ stars) that handles the translation, including tool calls, streaming, and multi-turn conversations.

---

## Prerequisites

| Requirement    | Version | Notes                  |
| -------------- | ------- | ---------------------- |
| Node.js        | v18+    | For Claude Code CLI    |
| Python         | 3.8+    | For the proxy          |
| pip            | Latest  | For proxy dependencies |
| Git            | Any     | To clone the proxy     |
| GitHub Copilot | Active  | Free for students      |

---

## Step-by-Step Setup

### Step 0 — Get GitHub Copilot (if you don't have it)

**Students (free):**

1. Go to [education.github.com/pack](https://education.github.com/pack)
2. Click **"Get Student Benefits"**
3. Verify with your `.edu` email or student ID card
4. Wait 1–3 days for approval
5. GitHub Copilot is now active on your account — free

**Not a student:**

- GitHub Copilot is **$10/month** or **$100/year**
- Sign up at [github.com/features/copilot](https://github.com/features/copilot)

---

### Step 1 — Install OpenCode CLI

OpenCode is an open-source terminal coding assistant. We use it purely for its GitHub Copilot authentication system — it stores your Copilot OAuth token locally.

```bash
# Install OpenCode
curl -fsSL https://opencode.ai/install | bash

# Reload your shell
source ~/.bashrc

# Verify installation
opencode --version
# Expected: 1.2.x or higher
```

**If `opencode` command not found after install:**

```bash
# Try the full path
~/.local/bin/opencode --version

# Or add to PATH manually
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

### Step 2 — Authenticate with GitHub Copilot

```bash
opencode auth login github
```

This will display a **device code** in your terminal, for example:

```
Please visit https://github.com/login/device
Enter code: ABCD-1234
```

1. Open [github.com/login/device](https://github.com/login/device) in your browser
2. Enter the displayed code
3. Click **Authorize OpenCode**
4. Return to your terminal — authentication is complete

**Verify your token was saved:**

```bash
cat ~/.local/share/opencode/auth.json
```

You should see:

```json
{
  "github-copilot": {
    "type": "oauth",
    "access": "gho_xxxxxxxxxxxxxxxxxxxx",
    "refresh": "gho_xxxxxxxxxxxxxxxxxxxx",
    "expires": 0
  }
}
```

**Copy the `access` value** — you'll need it in Step 4.

**Check available models:**

```bash
opencode models | grep claude
```

```
github-copilot/claude-sonnet-4.5
github-copilot/claude-opus-4.5
github-copilot/claude-haiku-4.5
```

---

### Step 3 — Setup claude-code-proxy

```bash
# Clone the proxy repository
git clone https://github.com/fuergaosi233/claude-code-proxy
cd claude-code-proxy

# Install Python dependencies
pip install -r requirements.txt

# Or with the --break-system-packages flag on Ubuntu
pip install -r requirements.txt --break-system-packages
```

**Create the `.env` configuration file:**

```bash
nano .env
```

Paste this configuration (replace `your_token_here` with your actual token from Step 2):

```env
# Your GitHub Copilot OAuth token from auth.json
OPENAI_API_KEY=your_token_here

# GitHub Copilot API endpoint — NO /v1 suffix, this is important
OPENAI_BASE_URL=https://api.githubcopilot.com

# Claude models (removed from VS Code but accessible via API)
BIG_MODEL=claude-sonnet-4.5
MIDDLE_MODEL=claude-sonnet-4.5
SMALL_MODEL=claude-haiku-4.5

# Remove token limits for large codebases
MAX_TOKENS_LIMIT=8096
REQUEST_TIMEOUT=300

# Proxy server port
PORT=8082
```

Save with `Ctrl+O` → `Enter` → `Ctrl+X`.

> ⚠️ **Important:** The `OPENAI_BASE_URL` must be `https://api.githubcopilot.com` — NOT `https://api.githubcopilot.com/v1`. Adding `/v1` causes a 404 error.

---

### Step 4 — Install Claude Code CLI

```bash
npm install -g @anthropic-ai/claude-code
```

**Verify:**

```bash
claude --version
```

---

### Step 5 — Launch Everything

You need **two terminal windows** open simultaneously.

**Terminal 1 — Start the proxy (keep this running):**

```bash
cd claude-code-proxy
python3 start_proxy.py
```

You should see:

```
🚀 Claude-to-OpenAI API Proxy v1.0.0
✅ Configuration loaded successfully
   OpenAI Base URL: https://api.githubcopilot.com
   Big Model (opus): claude-sonnet-4.5
   Middle Model (sonnet): claude-sonnet-4.5
   Small Model (haiku): claude-haiku-4.5
   Server: 0.0.0.0:8082
INFO: Uvicorn running on http://0.0.0.0:8082
```

**Terminal 2 — Launch Claude Code:**

```bash
export ANTHROPIC_BASE_URL="http://localhost:8082"
export ANTHROPIC_AUTH_TOKEN="anything"
claude --model claude-sonnet-4-5
```

Claude Code will open. You should see `claude-sonnet-4-5 · API Usage Billing` in the header. Type `hi` to test.

---

## Available Models

All models are free with GitHub Copilot.

| Model                 | Type      | Speed      | Best For                         |
| --------------------- | --------- | ---------- | -------------------------------- |
| `claude-sonnet-4.5` ★ | Coding    | ⚡⚡⚡     | Complex tasks, large codebases   |
| `claude-opus-4.5` ★   | Reasoning | ⚡⚡       | Advanced reasoning, architecture |
| `claude-haiku-4.5`    | Fast      | ⚡⚡⚡⚡⚡ | Quick tasks, fast iteration      |
| `codex-5.3`           | General   | ⚡⚡⚡⚡   | General purpose, fast            |
| `gemini-3.1-pro`      | General   | ⚡⚡⚡     | Long context tasks               |

**★ = Removed from VS Code Copilot extension but still accessible via API**

### Switching models

Edit your `.env` file:

```env
# For Opus 4.5 (most powerful, slowest)
BIG_MODEL=claude-opus-4.5
MIDDLE_MODEL=claude-opus-4.5

# For fastest responses
BIG_MODEL=gpt-4.1
MIDDLE_MODEL=gpt-4.1
SMALL_MODEL=gpt-4o
```

Then restart the proxy: `python3 start_proxy.py`

---

## Making the Setup Permanent

Add environment variables to your shell profile so you don't type them every session:

```bash
echo 'export ANTHROPIC_BASE_URL="http://localhost:8082"' >> ~/.bashrc
echo 'export ANTHROPIC_AUTH_TOKEN="anything"' >> ~/.bashrc
source ~/.bashrc
```

**Create a launch script:**

```bash
cat > ~/start-claude.sh << 'SCRIPT'
#!/bin/bash
# Start proxy in background
cd ~/claude-code-proxy && python3 start_proxy.py &
PROXY_PID=$!
echo "Proxy started (PID: $PROXY_PID)"
sleep 2

# Launch Claude Code
claude --model claude-sonnet-4-5

# Kill proxy when Claude Code exits
kill $PROXY_PID
SCRIPT

chmod +x ~/start-claude.sh
```

Now just run `~/start-claude.sh` to start everything at once.

---

## Troubleshooting

### `opencode` command not found after install

```bash
source ~/.bashrc
# or
export PATH="$HOME/.local/bin:$PATH"
```

### `404 Not Found` from proxy

Your `OPENAI_BASE_URL` has a `/v1` suffix. Fix it:

```env
# Wrong
OPENAI_BASE_URL=https://api.githubcopilot.com/v1

# Correct
OPENAI_BASE_URL=https://api.githubcopilot.com
```

### `The requested model is not supported`

The model name format is wrong. Use the exact API name:

```bash
# Check exact model names
curl -s https://api.githubcopilot.com/models \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Editor-Version: vscode/1.95.0" \
  -H "Copilot-Integration-Id: vscode-chat" | python3 -m json.tool | grep '"id"' | grep claude
```

### `ECONNREFUSED` in Claude Code

The proxy isn't running. Make sure Terminal 1 shows `Uvicorn running on http://0.0.0.0:8082` before launching Claude Code in Terminal 2.

### Token expired / `401 Unauthorized`

```bash
# Re-authenticate
opencode auth login github

# Get new token
cat ~/.local/share/opencode/auth.json

# Update .env with new token
nano ~/claude-code-proxy/.env
# Update OPENAI_API_KEY=new_token_here

# Restart proxy
python3 start_proxy.py
```

### Responses are very slow (30+ seconds)

This is normal for Claude Sonnet 4.5 and Opus 4.5 — they are large models. Switch to a faster model:

```env
BIG_MODEL=gpt-4.1
MIDDLE_MODEL=gpt-4.1
```

GPT-4.1 responds in 3–5 seconds and handles coding tasks excellently.

### Claude Code can't write large files

Increase the token limit in `.env`:

```env
MAX_TOKENS_LIMIT=200000
REQUEST_TIMEOUT=600
```

### `opencode auth login github` fails with `fetch() URL is invalid`

This is a bug in some OpenCode versions. Workaround — manually create the auth file:

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens)
2. Generate a new token with the `copilot` scope
3. Create the auth file manually:

```bash
mkdir -p ~/.local/share/opencode
cat > ~/.local/share/opencode/auth.json << 'JSON'
{
  "github-copilot": {
    "type": "oauth",
    "access": "YOUR_GITHUB_TOKEN_HERE",
    "refresh": "YOUR_GITHUB_TOKEN_HERE",
    "expires": 0
  }
}
JSON
```

---

## Security

- **Never commit your `.env` file** — it contains your GitHub token
- Add `.env` to `.gitignore`: `echo ".env" >> .gitignore`
- **Revoke and regenerate** your token if it gets exposed: [github.com/settings/tokens](https://github.com/settings/tokens)
- The proxy runs locally — your token never leaves your machine except to contact `api.githubcopilot.com`
- `ANTHROPIC_AUTH_TOKEN="anything"` is intentional — the proxy doesn't validate it

---

## Project Structure

```
free-claude-copilot/
├── index.html          # Documentation website (GitHub Pages)
├── README.md           # This file
└── LICENSE             # MIT License

claude-code-proxy/      # Cloned separately
├── src/
│   └── main.py         # FastAPI proxy server
├── .env                # Your config (never commit this)
├── requirements.txt    # Python dependencies
└── start_proxy.py      # Entry point
```

---

## How the Proxy Works

When Claude Code sends a request:

1. **Claude Code** → sends Anthropic-format request to `localhost:8082/v1/messages`
2. **claude-code-proxy** → converts to OpenAI chat completions format
3. **Proxy** → forwards to `api.githubcopilot.com/chat/completions` with your token
4. **Copilot API** → responds with the model output
5. **Proxy** → converts response back to Anthropic format
6. **Claude Code** → receives the response, displays it, calls tools

The proxy handles:

- Message format conversion (Anthropic ↔ OpenAI)
- Tool/function calling translation
- System prompt handling
- Multi-turn conversation history
- Streaming (SSE) responses
- Error handling and retries

---

## Contributing

Found a bug or want to improve the setup guide?

1. Fork the repository
2. Make your changes
3. Open a pull request

Contributions welcome — especially:

- Updated model lists as Copilot adds/removes models
- Windows/WSL specific instructions
- Additional troubleshooting steps
- Translations

---

## Related Projects

- [claude-code-proxy](https://github.com/fuergaosi233/claude-code-proxy) — The proxy that makes this possible (2k+ stars)
- [OpenCode](https://opencode.ai) — The CLI used for Copilot authentication
- [Claude Code](https://anthropic.com/claude-code) — Anthropic's agentic coding CLI

---

## License

MIT — see [LICENSE](LICENSE) for details.

---

<div align="center">

Made for the developer community · Free forever with GitHub Copilot

**[⭐ Star this repo](https://github.com/itslokeshx/free-claude-copilot)** if it helped you!

</div>
