---
name: openclaw-optimizer
description: OpenClaw token cost reduction and performance tuning guide. Use when asked to optimize OpenClaw, reduce token costs, fix missing capabilities, or tune model/cache/search settings. Covers workspace file slimming, Prompt Caching, model config, tool permissions, essential skills, and memory search tuning.
---

# OpenClaw Optimizer

Battle-tested optimizations for OpenClaw instances. Reduce token costs, fix capability gaps, tune performance.

## The Token Cost Formula
`Token spend = (input + output) × calls/day × model price`

Every workspace file loaded at session start multiplies across every call. Keep them lean.

---

## Step 1 — Audit Current State

```powershell
# Check workspace file token usage
$files = @("AGENTS.md","SOUL.md","TOOLS.md","IDENTITY.md","USER.md","HEARTBEAT.md","MEMORY.md")
foreach ($f in $files) {
  $p = "$env:USERPROFILE\.openclaw\workspace\$f"
  if (Test-Path $p) {
    $c = Get-Content $p -Raw
    Write-Host "$f : $([math]::Round($c.Length/4)) tokens"
  }
}
Get-Content "$env:USERPROFILE\.openclaw\openclaw.json"
```

---

## Step 2 — Slim Down Workspace Files

Target: AGENTS.md ≤ 200 tokens · SOUL.md ≤ 200 tokens · MEMORY.md ≤ 2000 tokens

Prompt to use:
```
Help me slim down my OpenClaw context files to reduce token costs:
1) AGENTS.md — keep only session startup flow, memory structure, safety rules, format rules. Move the rest to skills.
2) SOUL.md — compress to concise bullet points.
3) MEMORY.md — remove outdated entries.
4) Periodically clean memory/YYYY-MM-DD.md logs older than 30 days.
```

Saving 1000 tokens = ~$45/month at Sonnet × 100 calls/day.

---

## Step 3 — Key `openclaw.json` Settings

| Setting | Value | Why |
|---------|-------|-----|
| `cacheRetention` | `"long"` | Prompt Caching — saves up to 90% on repeated context |
| `contextPruning` | `cache-ttl / 55m` | Auto-clears history older than 55 minutes |
| `compaction.memoryFlush` | enabled | Auto-saves key content before compaction |
| `heartbeat.every` | `"55m"` | Keeps cache warm between sessions |
| `memorySearch.provider` | `"gemini"` + `gemini-embedding-001` | Best semantic recall, especially for non-English |
| Web Search | `gemini-2.5-flash` | Free tier, replaces Brave |
| `tools.profile` | `"full"` | Unlocks web_search, browser, nodes and all tools |

**Get a free Gemini API Key:** https://aistudio.google.com/apikey (1500 requests/day free)

> ⚠️ `heartbeat.quiet` is not supported — throws `Unrecognized key` error. Do not add it.  
> ⚠️ `tools.profile` must be `"full"`. Defaults (`coding` / `messaging`) silently disable most tools.

---

## Step 4 — Essential Skills

```bash
# Web search (free Gemini-powered, replaces Brave)
npx skills add tavily-ai/skills@search -g -y

# Proactive behavior + self-improvement
npx skills add halthelobster/proactive-agent@proactive-agent -g -y
```

Both pass Socket + Snyk security checks.

**Avoid:**
- `bdi-mental-states` — academic only, not useful for personal assistants
- `autonomous-agents` — reference manual, limited practical value

**Optional (after memory files exceed 5000+ tokens):**
```bash
npm install -g https://github.com/tobi/qmd
```

---

## Step 5 — Model Switching

```
/model opus    # Switch to Opus (complex / reasoning tasks)
/model sonnet  # Switch back to Sonnet (daily use)
```

Set model aliases in `openclaw.json`:
```json
"models": {
  "aliases": {
    "opus": "amazon-bedrock/global.anthropic.claude-opus-4-6-v1",
    "sonnet": "amazon-bedrock/global.anthropic.claude-sonnet-4-6"
  }
}
```

---

## Step 6 — Verify

```powershell
Get-Content "$env:USERPROFILE\.openclaw\openclaw.json" | ConvertFrom-Json | Out-Null
Write-Host "Config valid ✅"
```

Check Prompt Cache hit rate after a few conversations with `/status`.

---

## Priority Order (highest impact first)

1. **tools.profile → full** (broken without this)
2. Slim workspace files
3. Prompt Caching (`cacheRetention: long`)
4. Gemini web search (fixes search)
5. Gemini embeddings (fixes non-English memory recall)
6. Install proactive-agent
7. Context Pruning + Compaction
8. qmd (after files accumulate)
