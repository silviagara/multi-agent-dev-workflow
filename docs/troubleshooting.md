# Troubleshooting

Hard-won lessons from real production debugging. Each issue below cost days or weeks to solve — documented here so you don't repeat them.

---

## Issue 1: "OAuth token refresh failed for openai-codex" even when Codex is not configured

**Symptoms:**
- Gateway starts fine, first message works
- Second message fails with `OAuth token refresh failed for openai-codex`
- Your config has zero references to Codex/OpenAI
- Cleaning auth files, sessions, and model cache doesn't help — the error comes back

**Root cause:**
OpenClaw has a built-in plugin called `codex` (not `openai-codex`) that auto-registers an OpenAI Codex provider on every gateway startup. This plugin writes Codex models into `~/.openclaw/agents/main/agent/models.json` regardless of your config, pointing them at `https://chatgpt.com/backend-api`.

The critical detail: **the plugin ID is `codex`, not `openai-codex`.** If you set `plugins.entries.openai-codex.enabled = false`, OpenClaw silently ignores it because no plugin with that ID exists. The gateway logs may show `"plugin not found: openai-codex (stale config entry ignored)"` but this is easy to miss.

**Fix:**
```json
// In openclaw.json → plugins → entries
"codex": { "enabled": false }
```

**Why this is hard to find:**
- The error message says "openai-codex" but the plugin ID is "codex"
- `openclaw config set plugins.entries.openai-codex.enabled false` succeeds silently but does nothing
- `models.json` regenerates on every gateway restart, undoing manual cleanups
- The plugin registers before config-level overrides are checked in older versions

**Additional steps if you have multiple OpenClaw installations:**
If you upgraded OpenClaw but the old version still exists (e.g., `/usr/lib/node_modules/openclaw/` alongside `~/.local/lib/node_modules/openclaw/`), the gateway may load plugins from both. Remove the old installation:
```bash
sudo rm -rf /usr/lib/node_modules/openclaw
sudo rm -f /usr/bin/openclaw
```

---

## Issue 2: "Could not load credentials from any providers" with AWS Bedrock

**Symptoms:**
- Gateway starts, shows `agent model: amazon-bedrock/eu.anthropic.claude-sonnet-4-6`
- Messages fail with `Could not load credentials from any providers`
- AWS credentials ARE in your `openclaw.json` under the `env` section

**Root cause:**
The AWS SDK used by OpenClaw's Bedrock plugin reads credentials from the standard AWS credential chain — environment variables, `~/.aws/credentials`, IAM roles — but **not** from OpenClaw's `env` config section. Your credentials are in `openclaw.json` but the SDK never looks there.

**Fix:**
Create the standard AWS credentials file:
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

Then restart the gateway. The SDK finds credentials immediately.

**Alternative:** Export credentials as environment variables before starting the gateway:
```bash
export AWS_ACCESS_KEY_ID=your_key_id
export AWS_SECRET_ACCESS_KEY=your_secret
export AWS_DEFAULT_REGION=eu-west-1
nohup openclaw gateway > /tmp/openclaw-gateway.log 2>&1 & disown
```

**Why this is confusing:**
- OpenClaw's `env` section works for some integrations (GitHub tokens, Linear API keys) but not for the AWS SDK
- The error message doesn't tell you WHERE it looked for credentials
- If you also have the Codex issue (Issue 1), the credential error gets masked by the OAuth error

---

## Issue 3: "InvalidSignatureException" with AWS Bedrock

**Symptoms:**
- Gateway starts, Bedrock is configured, credentials are in `~/.aws/credentials`
- Messages fail with `InvalidSignatureException: The request signature we calculated does not match`

**Root cause (check in order):**

1. **Secret key corrupted during copy-paste.** AWS secret keys often contain `+`, `/`, `=` characters that get mangled by shell interpretation (double quotes), Python scripts, or text editors. Always use single quotes when setting the key:
   ```bash
   # WRONG — shell interprets special characters
   openclaw config set env.AWS_SECRET_ACCESS_KEY "key+with/special=chars"

   # RIGHT — single quotes preserve the literal value
   openclaw config set env.AWS_SECRET_ACCESS_KEY 'key+with/special=chars'
   ```

2. **VM clock drift.** AWS rejects signatures if the request timestamp differs by more than 5 minutes from AWS's clock:
   ```bash
   date                        # check current time
   timedatectl status          # check if NTP is synced
   sudo timedatectl set-ntp true  # fix if needed
   ```

3. **Credentials rotated.** Someone rotated the IAM access keys without updating your config.

**Verification script (test credentials outside OpenClaw):**
```bash
export AWS_ACCESS_KEY_ID=your_key_id
export AWS_SECRET_ACCESS_KEY='your_secret'
export AWS_DEFAULT_REGION=eu-west-1
python3 -c "
import boto3
client = boto3.client('bedrock-runtime', region_name='eu-west-1')
print('Connection OK — credentials are valid')
"
```
- If this fails with `InvalidSignatureException` → credentials are wrong
- If this succeeds (or fails with a different error like `ValidationException`) → credentials are valid, the issue is how OpenClaw passes them

---

## Issue 4: `models.json` keeps regenerating with unwanted providers

**Symptoms:**
- You clean `~/.openclaw/agents/main/agent/models.json`
- Restart the gateway
- The file immediately has providers you removed (e.g., codex, github-copilot)

**Root cause:**
`models.json` is a **cache**, not a config file. The gateway regenerates it on every startup from its loaded plugins. Cleaning the file is a temporary fix — the plugin that registers the provider will just add it back.

**Fix:**
Don't clean `models.json`. Instead, disable the plugin that's registering the unwanted provider:
```bash
# Find which plugin is adding the provider
# Common culprits:
openclaw config set plugins.entries.codex.enabled false          # Codex
openclaw config set plugins.entries.github-copilot.enabled false # GitHub Copilot
openclaw config set plugins.entries.copilot-proxy.enabled false  # Copilot Proxy
```

**How to find the plugin ID:**
```bash
# List all extensions
ls ~/.local/lib/node_modules/openclaw/dist/extensions/

# Check a specific extension's plugin ID
cat ~/.local/lib/node_modules/openclaw/dist/extensions/codex/openclaw.plugin.json | python3 -c "import json,sys; print(json.load(sys.stdin)['id'])"
```

---

## Issue 5: `gh` CLI not authenticated in the gateway process

**Symptoms:**
- You ran `gh auth login` successfully in your terminal
- But the orchestration agent says `gh` isn't authenticated
- `gh auth status` works in your terminal but not in the agent's shell

**Root cause:**
The gateway process runs in its own shell context. `gh auth login` saves credentials to your user session, but the gateway doesn't inherit them because it was started before the login, or in a different shell context.

**Fix:**
Set the token as an environment variable that the gateway inherits:
```bash
# Get your token
gh auth token

# Add to bashrc so every process inherits it
echo 'export GH_TOKEN=ghp_your_token_here' >> ~/.bashrc
echo 'export GITHUB_TOKEN=ghp_your_token_here' >> ~/.bashrc
source ~/.bashrc

# Restart the gateway so it picks up the new env vars
killall -9 openclaw-gateway 2>/dev/null
sleep 3
nohup openclaw gateway > /tmp/openclaw-gateway.log 2>&1 & disown
```

---

## Issue 6: Slack socket mode keeps disconnecting

**Symptoms:**
- Gateway logs show repeated `socket disconnected (disconnect). retry X/12`
- `A pong wasn't received from the server before the timeout of 5000ms`
- Messages sometimes get lost during disconnects

**Root cause:**
Slack socket mode connections are fragile on VMs with variable network quality. The connection drops, reconnects automatically, but messages sent during the disconnect window are lost.

**Mitigation:**
- The gateway auto-reconnects (up to 12 retries)
- If sending a message and getting no response, wait 30 seconds and retry
- Check the gateway log for `socket mode connected` before sending important commands
- For critical commands, verify via the gateway log that your message was processed

---

## General debugging approach

When something isn't working:

1. **Check the gateway log** — not just the startup log, but the runtime log:
   ```bash
   tail -30 /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log
   ```

2. **Check if the gateway is running:**
   ```bash
   ps aux | grep openclaw | grep -v grep
   ```

3. **Check the current config state:**
   ```bash
   cat ~/.openclaw/openclaw.json | python3 -m json.tool
   ```

4. **Check what plugins are loaded:**
   Look at the gateway startup log for the line:
   ```
   [gateway] http server listening (N plugins: plugin1, plugin2, ...)
   ```

5. **Check `models.json` for unwanted providers:**
   ```bash
   cat ~/.openclaw/agents/main/agent/models.json | python3 -c "import json,sys; print(list(json.load(sys.stdin).get('providers',{}).keys()))"
   ```

6. **When in doubt, restart the gateway:**
   ```bash
   killall -9 openclaw-gateway 2>/dev/null
   sleep 3
   nohup openclaw gateway > /tmp/openclaw-gateway.log 2>&1 & disown
   ```
