---
name: openclaw-optimizer
description: OpenClaw token cost reduction, performance tuning, and security hardening guide. Use when asked to optimize OpenClaw, reduce token costs, fix missing capabilities, tune model/cache/search settings, or harden security (firewall, permissions, credential audit).
---

# OpenClaw Optimizer

Battle-tested optimizations for OpenClaw instances. Reduce token costs, fix capability gaps, tune performance, harden security.

## The Token Cost Formula
`Token spend = (input + output) × calls/day × model price`

Every workspace file loaded at session start multiplies across every call. Keep them lean.

---

## Step 1 — Audit Current State

```bash
# Check workspace file token usage (macOS/Linux)
for f in AGENTS.md SOUL.md TOOLS.md IDENTITY.md USER.md HEARTBEAT.md MEMORY.md; do
  p="$HOME/.openclaw/workspace/$f"
  [ -f "$p" ] && echo "$f : ~$(($(wc -c < "$p") / 4)) tokens"
done
cat ~/.openclaw/openclaw.json
```

---

## Step 2 — Slim Down Workspace Files

Target: AGENTS.md ≤ 200 tokens · SOUL.md ≤ 200 tokens · MEMORY.md ≤ 2000 tokens

- AGENTS.md — keep only: session startup flow, memory structure, WAL protocol, safety rules. Remove duplicates already covered by system prompt (group chat, proactive work, etc.)
- SOUL.md — compress to concise bullet points
- MEMORY.md — remove outdated entries
- Periodically clean `memory/YYYY-MM-DD.md` logs older than 30 days

Saving 1000 tokens = ~$45/month at Sonnet × 100 calls/day.

---

## Step 3 — Key `openclaw.json` Settings

| Setting | Value | Why |
|---------|-------|-----|
| `cacheRetention` | `"long"` | Prompt Caching — saves up to 90% on repeated context |
| `contextPruning` | `cache-ttl / 55m` | Auto-clears history; align ttl with heartbeat interval |
| `compaction.memoryFlush` | enabled | Auto-saves key content before compaction |
| `heartbeat.every` | `"55m"` | Keeps cache warm between sessions |
| `memorySearch.provider` | `"gemini"` + `gemini-embedding-001` | Best semantic recall, especially for non-English |
| Web Search | `gemini-2.5-flash` | Free tier, replaces Brave |
| `tools.profile` | `"full"` | Unlocks web_search, browser, nodes and all tools |

**Get a free Gemini API Key:** https://aistudio.google.com/apikey (1500 requests/day free)

> ⚠️ `heartbeat.quiet` is not supported — throws `Unrecognized key` error. Do not add it.  
> ⚠️ `tools.profile` must be `"full"`. Defaults (`coding` / `messaging`) silently disable most tools.  
> ⚠️ Set `contextPruning.ttl` to match `heartbeat.every` (both `"55m"`) to keep cache warm.

---

## Step 4 — Security Hardening

### Checklist

| Item | Command / Location | Expected |
|------|-------------------|----------|
| gateway.bind | `openclaw.json` | `"loopback"` (not `"0.0.0.0"`) |
| gateway.auth.mode | `openclaw.json` | `"token"` |
| gateway.auth.token length | check config | ≥ 32 chars |
| openclaw.json permissions | `ls -la ~/.openclaw/openclaw.json` | `-rw-------` |
| macOS firewall | see below | Enabled |
| tailscale | `tailscale status` | Off unless intentional |

### Enable macOS Application Firewall

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on
# Verify:
/usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate
```

**Trade-off:** First run of any new app that listens on a port will trigger a system dialog. Allow or deny as needed. Can be disabled anytime:
```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate off
```

### Fix config file permissions if needed

```bash
chmod 600 ~/.openclaw/openclaw.json
chmod 700 ~/.openclaw/credentials
```

---

## Step 5 — Essential Skills

```bash
# Proactive behavior + self-improvement
npx skills add halthelobster/proactive-agent@proactive-agent -g -y
```

**Avoid:**
- `bdi-mental-states` — academic only, not useful for personal assistants
- `autonomous-agents` — reference manual, limited practical value

**Optional (after memory files exceed 5000+ tokens):**
```bash
npm install -g https://github.com/tobi/qmd
```

---

## Step 6 — Model Switching

```
/model opus    # Switch to Opus (complex / reasoning tasks)
/model sonnet  # Switch back to Sonnet (daily use)
```

Set model aliases in `openclaw.json`:
```json
"agents": {
  "defaults": {
    "models": {
      "amazon-bedrock/global.anthropic.claude-opus-4-6-v1": { "alias": "opus", "params": { "cacheRetention": "long" } },
      "amazon-bedrock/global.anthropic.claude-sonnet-4-6": { "alias": "sonnet", "params": { "cacheRetention": "long" } }
    }
  }
}
```

---

## Step 7 — Verify

```bash
# macOS/Linux
node -e "const fs=require('fs'); eval('('+fs.readFileSync(process.env.HOME+'/.openclaw/openclaw.json','utf8')+')'); console.log('Config valid ✅')"
```

Check Prompt Cache hit rate after a few conversations with `/status`.

---

## Priority Order (highest impact first)

1. **tools.profile → full** (broken without this)
2. Slim workspace files
3. Prompt Caching (`cacheRetention: long`)
4. Gemini web search (fixes search)
5. Gemini embeddings (fixes non-English memory recall)
6. **Security hardening** (firewall + permissions audit)
7. Context Pruning + Compaction (ttl aligned to heartbeat)
8. Install proactive-agent
9. qmd (after files accumulate)
