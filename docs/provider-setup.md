# Provider Setup

How to configure different model providers for your multi-agent workflow. Each section is independent — set up whichever provider fits your needs.

---

## Local setup (no cloud provider)

Use local models via Ollama or vLLM for development and testing.

### Ollama
```bash
# Install Ollama
curl -fsSL https://ollama.ai/install.sh | sh

# Pull a model
ollama pull llama3.1

# Configure OpenClaw
openclaw config set agents.defaults.model.primary "ollama/llama3.1"
```

### vLLM
```bash
# Start vLLM server
python -m vllm.entrypoints.openai.api_server --model your-model

# Configure OpenClaw
openclaw config set agents.defaults.model.primary "vllm/your-model"
```

---

## VM setup

Running on a remote VM (Azure, AWS, GCP, etc.) adds deployment considerations.

### Gateway startup
The gateway doesn't auto-start after VM reboot. Add manual startup or a cron job:

```bash
# Manual start
nohup openclaw gateway > /tmp/openclaw-gateway.log 2>&1 & disown

# Auto-start on boot (add to crontab)
crontab -e
# Add: @reboot /home/youruser/.local/bin/openclaw gateway > /tmp/openclaw-gateway.log 2>&1
```

### Environment variables
The gateway runs in its own process and doesn't inherit your shell's environment unless you either:
1. Export vars in `~/.bashrc` before starting the gateway
2. Use credential files (e.g., `~/.aws/credentials`)
3. Use systemd drop-ins (if running as a service)

### VM shutdown handling
If your VM auto-shuts down, save agent state before shutdown:
```bash
# Add to crontab — saves state 5 minutes before shutdown (adjust time as needed)
25 20 * * * /home/youruser/.local/bin/openclaw agent --message "Save current state to WORK_LOG.md" 2>/dev/null
```

---

## Amazon Bedrock (Anthropic Claude)

Use Claude models via AWS Bedrock with EU data residency.

### Prerequisites
- AWS IAM user with `bedrock:InvokeModel` permission
- Access to Anthropic models enabled in your AWS account
- Region: typically `eu-west-1` (Ireland) for EU data residency

### Setup

**Step 1 — Configure OpenClaw model:**
```bash
openclaw config set agents.defaults.model.primary "amazon-bedrock/eu.anthropic.claude-sonnet-4-6"
openclaw config set agents.defaults.model.fallbacks '["amazon-bedrock/eu.anthropic.claude-opus-4-6-v1"]'
```

**Step 2 — AWS credentials (choose one method):**

Method A — Credentials file (recommended):
```bash
mkdir -p ~/.aws

cat > ~/.aws/credentials << 'EOF'
[default]
aws_access_key_id = YOUR_ACCESS_KEY_ID
aws_secret_access_key = YOUR_SECRET_ACCESS_KEY
EOF

cat > ~/.aws/config << 'EOF'
[default]
region = eu-west-1
EOF

chmod 600 ~/.aws/credentials ~/.aws/config
```

Method B — Environment variables:
```bash
echo 'export AWS_ACCESS_KEY_ID=YOUR_KEY_ID' >> ~/.bashrc
echo 'export AWS_SECRET_ACCESS_KEY=YOUR_SECRET' >> ~/.bashrc
echo 'export AWS_DEFAULT_REGION=eu-west-1' >> ~/.bashrc
source ~/.bashrc
```

**Important:** Do NOT rely on OpenClaw's `env` section in `openclaw.json` for AWS credentials. The AWS SDK doesn't read from there. Use `~/.aws/credentials` or shell environment variables.

**Step 3 — Disable Bedrock discovery (if your IAM user lacks ListFoundationModels permission):**
```bash
openclaw config set plugins.entries.amazon-bedrock.config.discovery.enabled false
```

**Step 4 — Disable Codex plugin (prevents OAuth conflicts):**
```bash
openclaw config set plugins.entries.codex.enabled false
```

### Model IDs
Use the exact model IDs — wrong IDs return `ValidationException`:
- `eu.anthropic.claude-sonnet-4-6` — fast, great for daily coding
- `eu.anthropic.claude-opus-4-6-v1` — complex tasks, architecture decisions
- `eu.anthropic.claude-opus-4-7` — may require separate access approval

The `eu.` prefix means EU cross-region routing — data stays in the EU.

### Gotchas
- Opus models reject `temperature`, `topP`, `topK` parameters — don't set them
- Use `maxTokens` to prevent runaway outputs
- First Bedrock request after gateway restart sometimes returns empty — retry once
- Secret keys with `+`, `/`, `=` characters get mangled by double quotes — always use single quotes

---

## Azure OpenAI

Use GPT models via Azure OpenAI Service.

### Setup

**Step 1 — Add provider to OpenClaw config:**

Edit `openclaw.json` directly (stop the gateway first):
```json
{
  "models": {
    "providers": {
      "azure-openai": {
        "baseUrl": "https://YOUR-REGION.api.cognitive.microsoft.com/openai/v1",
        "api": "openai-completions",
        "authHeader": false,
        "headers": {
          "api-key": "YOUR_AZURE_API_KEY"
        },
        "models": [
          {
            "id": "gpt-5-4",
            "name": "GPT-5.4 (Azure)",
            "api": "openai-completions",
            "reasoning": true,
            "input": ["text", "image"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 272000,
            "maxTokens": 16384,
            "compat": { "supportsStore": false }
          }
        ]
      }
    }
  }
}
```

**Step 2 — Set as primary or fallback:**
```bash
# As primary
openclaw config set agents.defaults.model.primary "azure-openai/gpt-5-4"

# As fallback (after Bedrock)
openclaw config set agents.defaults.model.fallbacks '["azure-openai/gpt-5-4"]'
```

### Gotchas
- Auth: use `authHeader: false` + `headers.api-key` — Azure rejects `Authorization: Bearer` on the v1 endpoint
- Set `compat.supportsStore: false` — Azure's v1 route doesn't honor `store=true`
- GPT-5.x models expose reasoning — don't set `temperature` or `topK`
- Edit `openclaw.json` only while the gateway is stopped — it may overwrite your changes from in-memory state

---

## Multi-provider fallback chain

Set up a fallback chain so if your primary provider is down, the agent automatically tries the next one:

```bash
openclaw config set agents.defaults.model.primary "amazon-bedrock/eu.anthropic.claude-sonnet-4-6"
openclaw config set agents.defaults.model.fallbacks '["amazon-bedrock/eu.anthropic.claude-opus-4-6-v1", "azure-openai/gpt-5-4", "azure-gpt/gpt-5-3"]'
```

The gateway tries each model in order until one succeeds. This gives you:
1. Sonnet 4.6 (fast, daily coding) → primary
2. Opus 4.6 (complex tasks) → first fallback
3. GPT-5.4 Azure (non-Anthropic backup) → second fallback
4. GPT-5.3 Azure (last resort) → third fallback

---

## Verifying your setup

After configuring any provider:

```bash
# Start the gateway
nohup openclaw gateway > /tmp/openclaw-gateway.log 2>&1 & disown

# Check the model is loaded
sleep 5
grep "agent model" /tmp/openclaw-gateway.log

# Check plugins (should NOT include "codex" unless you want it)
grep "plugins:" /tmp/openclaw-gateway.log

# Test via Slack or CLI
# Send 5+ messages to verify stability
```
