# agent-skills

> **你的 OpenClaw 可能每天在白白烧钱。**

每次对话，所有 workspace 文件都会被完整加载。一个没人在维护的 AGENTS.md，可能每天消耗你几千 token，什么都没干。

这个 repo 收录了真实跑过、踩坑验证的 skills。装上就用，立刻生效。

---

## 安装方式

```bash
npx skills add learsea/agent-skills@<skill-name>
```

---

## Skills

### 🔧 openclaw-optimizer

**"我以为配置好了，结果工具根本没开。"**

OpenClaw 有一个坑：默认 `tools.profile` 是 `"coding"`，会静默禁用 web_search、browser 等大部分工具。没有报错，没有提示，就是不能用。

这个 skill 帮你：

- ✂️ **砍掉 60%+ 的固定 token 消耗** — 每 1000 token = ~$45/月（Sonnet × 100 次/天）
- ⚡ **开启 Prompt Caching** — 相同上下文最高省 90%
- 🔍 **接入免费 Gemini 搜索 + 中文语义记忆** — 替换 Brave，修复中文 memory recall 失效
- 🔒 **安全加固** — 一键核查公网暴露、权限、凭证，对齐工信部安全建议
- 🛠️ **修掉 tools.profile 这个坑** — 解锁所有工具
- 📋 **避坑清单** — 哪些 config 字段会报 `Unrecognized key`，哪些 skills 装了没用

不是理论，是每一条都踩过的教训。

```bash
npx skills add learsea/agent-skills@openclaw-optimizer
```

触发词：`优化 openclaw` · `降低 token 消耗` · `工具不能用` · `搜索坏了` · `安全检查`

---

## Contributing

Skills 遵循标准 `SKILL.md` 格式。欢迎 PR。
