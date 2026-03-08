# agent-skills

A collection of agent skills for [OpenClaw](https://openclaw.ai) and compatible AI assistant frameworks.

Install skills via the [Skills CLI](https://skills.sh):

```bash
npx skills add learsea/agent-skills@<skill-name>
```

---

## Skills

### 🔧 openclaw-optimizer

> Reduce OpenClaw token costs and fix missing capabilities in under 30 minutes.

Battle-tested optimizations for OpenClaw instances. Covers:

- **Token cost reduction** — slim workspace files, calculate savings
- **Prompt Caching** — up to 90% cost reduction on repeated context
- **Free Gemini integration** — replace paid Brave search, fix non-English memory recall
- **Tool permissions** — fix silently disabled tools (`tools.profile: full`)
- **Model switching** — Sonnet for daily use, Opus for complex tasks
- **Essential skills** — which ones are worth installing, which to avoid
- **Config traps** — undocumented gotchas that cause silent failures

**Install:**
```bash
npx skills add learsea/agent-skills@openclaw-optimizer
```

**Trigger phrases:** "optimize openclaw", "reduce token costs", "fix missing tools", "why is search broken", "tune performance", "openclaw slow", "too expensive"

---

## Contributing

Skills follow the standard `SKILL.md` format. PRs welcome.
