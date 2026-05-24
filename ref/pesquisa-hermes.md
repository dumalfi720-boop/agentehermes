# Hermes Agent — Consolidated Technical Research

**Date:** May 10, 2026
**Sources:** Official documentation, GitHub, Hostinger VPS

---

## 1. Hermes Agent — Technical Fundamentals

### Official Repository

- **URL:** https://github.com/NousResearch/hermes-agent
- **License:** MIT
- **Organization:** NousResearch (Nous Research)
- **Slogan:** "The agent that grows with you"
- **Stars:** 141,404
- **Forks:** 21,979
- **Open issues:** 9,626
- **Commits:** 7,938
- **Latest version:** v0.13.0 (released May 7, 2026) — called "The Tenacity Release"
- **Created on:** July 22, 2025 — growth of ~141k stars in less than 1 year
- **Last commit:** May 10, 2026 (updated on the same day as this search)

### Language and Dependencies

- **Main language:** Python 88.7%
- **Frontend/TUI:** TypeScript 7.9%
- **Scripts:** Shell 0.5%, Nix 0.3%
- **Key dependencies:** see`.env.example` e `requirements*.txt`in the repository. The stack includes asyncio, SQLite (sessions + FTS5), Docker for isolation, and messaging platform adapters.
- **Official documentation:** https://hermes-agent.nousresearch.com/docs

### General Architecture

The core is the class`AIAgent` em `agent/run_agent.py`— the synchronous orchestration loop that handles: provider selection, prompt construction, tool execution, retries, fallback, callbacks, compression, and persistence. Supports three API modes for different backends.

Repository directory structure:```
agent/          → loop principal, run_agent.py
tools/          → 40+ ferramentas registradas via tools/registry.py
cron/           → agendador de tarefas
acp_adapter/    → integração ACP (Agent Communication Protocol)
acp_registry/   → catálogo de agentes
tui_gateway/    → terminal UI + gateway de mensagens
ui-tui/         → frontend TUI em TypeScript
web/            → interface web
website/        → documentação Docusaurus
```Data flow in three paths:
1. **CLI Session** →`cli.py` → `AIAgent`→ tool loop → response
2. **Gateway Message** (Telegram, Discord etc.) →`tui_gateway/` → `AIAgent`→ delivery
3. **Cron Job** →`cron/`scheduler →`AIAgent`fresh session → delivery

### How the Agent Loads Context

Hermes uses a hierarchical context file system. They all reside in`~/.hermes/`:

| Archive | Purpose | Scope |
|---------|-----------|--------|
|`SOUL.md`| Agent primary identity — occupies system prompt slot #1 | Global (`~/.hermes/SOUL.md`) |
| `.hermes.md` / `HERMES.md`| Design instructions (highest priority) | Go up to git root |
|`AGENTS.md`| Design instructions, code conventions | Subdirectory recursive walk |
|`CLAUDE.md`| Claude Code context (also detected) | CWD only |
|`.cursorrules`| IDE Cursor Conventions | CWD only |
|`.cursor/rules/*.mdc`| Cursor rules modules | CWD only |

Priority rule: Only **one** project context type is loaded per session, in the order:`.hermes.md` → `AGENTS.md` → `CLAUDE.md` → `.cursorrules`. THE`SOUL.md`is always loaded independently.

**Persistent memory** — two files in`~/.hermes/memories/`:
- `MEMORY.md`— agent's personal notes (environment, facts learned). Limit: 2,200 chars (~800 tokens).
-`USER.md`— user profile (preferences, communication style). Limit: 1,375 chars (~500 tokens).

Both are injected into the system prompt as a frozen snapshot at the start of each session. The agent manages its own memory via the tool`memory`(add, replace, remove). When full, it consolidates entries to make room.

Furthermore,`session_search`allows search in conversation history stored in SQLite with FTS5 (`~/.hermes/state.db`).

### Anatomy of SKILL.md — YAML Front Matter

Exact front matter fields:```yaml
---
name: my-skill
description: Brief description of what this skill does
version: 1.0.0
platforms: [macos, linux]     # Opcional — restringe a OS específicos
metadata:
  hermes:
    tags: [python, automation]
    category: devops
    fallback_for_toolsets: [web]    # Opcional — ativação condicional
    requires_toolsets: [terminal]   # Opcional — ativação condicional
    config:                         # Opcional — configurações config.yaml
      - key: my.setting
        description: "What this controls"
        default: "value"
        prompt: "Prompt for setup"
---
# Skill Title

## When to Use
Trigger conditions for this skill.

## Procedure
1. Step one
2. Step two

## Pitfalls
- Known failure modes and fixes

## Verification
How to confirm it worked.
```**Progressive Disclosure** — token-efficient standard at 3 levels:
-`Level 0: skills_list()`→ list`[{name, description, category}, ...]`(~3k tokens)
-`Level 1: skill_view(name)`→ full content + metadata
-`Level 2: skill_view(name, path)`→ specific reference file

The agent only loads the complete content when it really needs the skill.

**Directory structure of a skill:**```
~/.hermes/skills/
├── mlops/
│   └── axolotl/
│       ├── SKILL.md          # Instruções principais (obrigatório)
│       ├── references/       # Docs adicionais
│       ├── templates/        # Formatos de output
│       ├── scripts/          # Scripts auxiliares
│       └── assets/           # Arquivos suplementares
```Skills follow the **agentskills.io** open standard (https://agentskills.io/specification).

### Crons System

Hermes exposes cron management via single tool`cronjob`with action-style operations:

**Creation via chat:**```
/cron add 30m "Remind me to check the build"
/cron add "every 2h" "Check server status"
/cron add "every 1h" "Summarize new feed items" --skill blogwatcher
/cron add "every 1h" "Use both skills" --skill blogwatcher --skill maps
```**Standalone CLI:**```bash
hermes cron create "every 1d at 09:00" \
  "Audit open PRs, summarize CI health, and post to #eng" \
  --workdir /home/me/projects/acme
```**Main flags:**

| Flag | Cron equivalent | Function |
|------|-----------------|--------|
|`--workdir <path>` | `WORKDIR`| Sets working directory — loads AGENTS.md/CLAUDE.md from there |
|`--no-agent` | `NOAGENT`| Script-only mode, skips LLM completely |
|`--deliver <platform>` | `CONTEXTFROM`/ delivery | Define delivery destination (telegram, discord, all, origin) |
|`--skill <name>`| skill injection | Injects skill at the beginning of the fresh session |

**No-agent mode (NOAGENT):** For jobs that do not require LLM — watchdogs, disk/memory alerts, heartbeats:```bash
hermes cron create "every 5m" \
  --no-agent \
  --script memory-watchdog.sh \
  --deliver telegram \
  --name "memory-watchdog"
```- empty stdout → silent tick (no delivery)
- non-zero output → error alert delivered
- scripts`.sh/.bash`rotate under`/bin/bash`; others run on the Python interpreter

**Gateway scheduler behavior** (how it works on each tick):
1. Load jobs from`~/.hermes/cron/jobs.json`2. Check`next_run_at`vs current time
3. Start a fresh session`AIAgent`for each job won
4. Injects skills attached to the session
5. Run the prompt until completion
6. Deliver the final answer
7. Updates metadata and next scheduled time

File lock on`~/.hermes/cron/.tick.lock`prevents double-run.

**Note about WAT (Workflow-Agent-Tools):** The term "WAT framework" does not appear explicitly in the official Hermes documentation as a separate acronym. The documentation describes the pattern as "Skills System + Cron + Tools" without that specific nomenclature.

### Skills Hub

- **URL:** https://agentskills.io
- **Open standard:** https://agentskills.io/specification
- **Hub Discord:** https://discord.gg/MKPE9g8aUy

Skills Hub is an independent registry of Hermes-compatible skills. There are multiple installation sources:

1.`official`— skills bundled in the hermes-agent repository (`optional-skills/`)
2.`skills-sh`— external hub agentskills.io
3. GitHub (user/repo/path)
4. Direct URL (`https://example.com/SKILL.md`)
5.`well-known`— self-discovery via`/.well-known/agent-skills`**Skills bundled** (active by default on`~/.hermes/skills/`) — categories:
- apple, autonomous-ai-agents, creative, data-science, devops, dogfood, email, gaming, github, mcp, media, mlops, note-taking, productivity, red-teaming, research, smart-home, social-media, software-development, yuanbao

**Official optional skills** (in`optional-skills/`, explicit installation) — 16 categories:
- autonomous-ai-agents, blockchain, communication, creative, devops, dogfood, email, finance, health, mcp, migration, mlops, productivity, research, security, web-development

The exact number of skills is not published as a single number in the documentation; the count grows with each release. In v0.13.0, 6 new optional skills were added (Shopify Admin+Storefront, here.now, shop-app, Anthropic financial-services bundle, kanban-video-orchestrator, searxng-search).

### Supported Inference Providers

| Provider | How to use | Cost |
|----------|-----------|-------|
| OAuth Codex |`provider: "codex"`— uses OAuth token from ChatGPT account, without API key | Included in the ChatGPT Plus/Pro plan |
| OpenRouter |`OPENROUTER_API_KEY` + `provider: "openrouter"`— routes to any model | Variable pay-per-token |
| Nous Portal |`hermes auth` + `provider: "nous"`| Via NousResearch account |
| OpenAI API |`OPENAI_API_KEY` + `OPENAI_BASE_URL`| Pay-per-token |
| MiniMax OAuth |`provider: "minimax-oauth"`— login via browser | No API key |
| Local/Custom |`base_url: "http://localhost:1234/v1"`— any OpenAI-compatible endpoint | Free (on-premises hardware) |
| GMI |`GMI_API_KEY`| Pay-per-token |

Automatically recognized models (tool-use enforcement auto):`gpt`, `codex`, `gemini`, `gemma`, `grok`.

Models without enforcement (natively trusted): Claude, DeepSeek, Qwen, LLaMA.

Models added in v0.13.0:`deepseek/deepseek-v4-pro`, `x-ai/grok-4.3`, `openrouter/owl-alpha`(free),`tencent/hy3-preview`.

### Supported Messaging Platforms

The gateway (`hermes gateway setup` + `hermes gateway start`) supports:

Telegram, Discord, Slack, WhatsApp, Signal, SMS, Email, Home Assistant, Mattermost, Matrix, DingTalk, Feishu/Lark, WeCom, WeCom Callback, Weixin, **BlueBubbles (iMessage)**, QQBot, Yuanbao, Microsoft Teams, Google Chat, and browser.

Each platform has its own configurable toolset. Platforms like Signal (which don't support message editing) have granular verbosity controls:```yaml
display:
  platforms:
    signal:
      tool_progress: 'off'
    telegram:
      tool_progress: verbose
```---

## 2. Comparisons — Hermes vs Competitors

### Hermes vs Claude Code (Anthropic CLI)

1. **Messaging gateway:** Hermes has a native gateway for Telegram/Discord/WhatsApp/iMessage/20+ platforms. Claude Code operates exclusively on the terminal — without a messaging gateway.
2. **Native Cron:** Hermes includes task scheduler with cross-platform delivery. Claude Code does not have built-in cron.
3. **Persistent skills:** Hermes maintains skills as YAML documents structured with progressive disclosure. Claude Code uses CLAUDE.md and free system prompts, without an indexed catalog.
4. **Providers:** Hermes connects to OpenAI, Anthropic, OpenRouter, Local, MiniMax OAuth, Codex OAuth. Claude Code is coupled to the Claude model via the Anthropic API.
5. **Multi-agent Kanban:** v0.13.0 introduces multi-agent orchestration with heartbeat/reclaim/zombie detection. Claude Code does not have native multi-agent coordination.

**Hermes wins:** 24/7 personal assistance via messaging, scheduled automations, non-technical users who prefer WhatsApp/Telegram.

**Claude Code wins:** quality of code reasoning with Claude, deep integration with IDE, PR review, complex refactoring where the Claude model is superior.

### Hermes vs OpenCode

1. **Gateway:** Hermes has a complete gateway for messaging platforms; OpenCode is pure CLI coding.
2. **Skills Hub:** Hermes has a skills ecosystem at agentskills.io; OpenCode focuses exclusively on software development tasks.
3. **Memory:** Hermes has MEMORY.md + USER.md persistent with self-management. OpenCode has no equivalent memory system.
4. **Multi-provider:** Hermes supports any OpenAI-compatible backend. OpenCode also supports multiple providers, but without the OAuth/subscription management level.
5. **Integration:** Hermes has skill bundled`opencode`to delegate coding tasks to the OpenCode CLI — the two coexist.

**Hermes wins:** full life workflow (not just code), automations, communication via messaging.

**OpenCode wins:** pure software engineering tasks where code focus is an advantage.

### Hermes vs OpenAI Codex CLI

1. **Dependency:** Codex CLI requires OpenAI/ChatGPT account for authentication. Hermes uses Codex as *one of* optional providers via OAuth, and can also run locally.
2. **Skills:** Hermes has an extensible skill system. Codex CLI has no equivalent.
3. **Platforms:** Hermes delivers via 20+ messaging platforms. Codex CLI is terminal-only.
4. **Cron:** Hermes has an integrated scheduler. Codex CLI does not have it.
5. **Delegation:** Hermes has skill bundled`codex`to delegate tasks to the Codex CLI as a sub-agent.

**Hermes wins:** automation, integration with messengers, multi-provider.

**Codex CLI gains:** direct use in the terminal for code tasks, without additional infrastructure.

### Hermes vs Cursor

1. **Paradigm:** Cursor is IDE with built-in copilot. Hermes is a command line agent with a gateway — it runs on a headless server.
2. **Project context:** Hermes detects and respects`.cursorrules` e `.cursor/rules/*.mdc`— the two are complementary.
3. **Headless automation:** Hermes can run on a VPS without a graphical interface, responding via Telegram. Cursor requires desktop.
4. **Skills:** Hermes has reusable procedures with versioning. Cursor uses design rules and prompts.
5. **Cost:** Hermes is open source, runs with Codex subscription ($20/month) or local models. Cursor has a separate paid plan.

**Hermes wins:** environments without a graphical interface, 24/7 automation, costs with alternative models.

**Cursor wins:** interactive development in the IDE, inline autocompletion, UX integrated into the editor.

### Hermes vs n8n (visual orchestration)

1. **Paradigm:** n8n uses visual workflows with connected nodes. Hermes uses natural language + skills — no flow design interface.
2. **Dynamic reasoning:** Hermes uses LLM to decide next steps at runtime. n8n performs pre-defined fixed sequences.
3. **Infrastructure:** n8n requires database, webhook server and node configuration. Hermes rolls with`pip install`+ an API key.
4. **Learning Curve:** n8n requires understanding each node and connector. Hermes accepts natural language instructions via chat.
5. **Hermes + n8n:** Hostinger lists n8n as a separate one-click application from Hermes; can coexist, with Hermes as an intelligence layer on top of n8n automations.

**Hermes wins:** flexibility of reasoning, quick configuration, users who prefer chat to visual UI.

**n8n wins:** complex deterministic workflows, integrations with hundreds of services via official nodes, visual audit trail.

### Hermes vs LangChain/LangGraph

1. **Level of abstraction:** LangChain/LangGraph are Python frameworks for developers to build LLM applications. Hermes is a ready-to-use agent with installer and interface.
2. **Target user:** LangGraph: developer who wants to define agent graphs in code. Hermes: user who wants an agent working via`hermes`in the terminal.
3. **Skills:** SKILL.md are Markdown procedures. LangChain uses chains and tools in Python — a lot more programmatic power, but requires code.
4. **Extension:** Hermes has a plugin system for hooks. LangChain has an ecosystem of 500+ integrations.
5. **Deploy:** Hermes runs standalone with gateway. LangChain requires its own server and infrastructure.

**Hermes wins:** zero-code setup, non-developer users, personal agent always on.

**LangChain/LangGraph gains:** complex custom applications, fine flow control, integration into software products.

### Hermes vs CrewAI

1. **Orchestration:** CrewAI coordinates multiple agents with fixed roles (Researcher, Writer, etc.). Hermes v0.13.0 introduces multi-agent Kanban with dynamic workers.
2. **Persistence:** Hermes persists sessions in SQLite with FTS5. CrewAI does not have an equivalent persistence system by default.
3. **Interface:** CrewAI is pure Python framework. Hermes has TUI, CLI and messaging gateway.
4. **Skills:** Hermes has SKILL.md as reusable procedures. CrewAI uses tools as Python functions.
5. **Installation:** Hermes:`pip install hermes-agent && hermes setup`. CrewAI requires Python code to define agents and crew.

**Hermes wins:** personal use, no-code setup, cross-platform messaging.

**CrewAI wins:** orchestration of multiple specialized agents in code-defined pipelines.

### Hermes vs AutoGen (Microsoft)

1. **Paradigm:** AutoGen focuses on multi-agent conversations where agents talk to each other. Hermes uses Kanban to coordinate workers with task ownership.
2. **Interface:** AutoGen is Python framework without user interface. Hermes has TUI + gateway.
3. **Memory:** Hermes has MEMORY.md/USER.md managed automatically. AutoGen relies on the developer to implement persistence.
4. **Enterprise integration:** AutoGen has integration with Azure OpenAI and Teams. Hermes has native integration with Teams via gateway.
5. **Open source:** Both MIT. AutoGen has Microsoft enterprise support. Hermes has NousResearch community + fork`openclaw`migrated.

**Hermes wins:** product ready for personal use/small teams, no development required.

**AutoGen wins:** complex enterprise applications, research into multi-agent systems, Azure integration.

### Hermes vs Aider

1. **Focus:** Aider specializes in pair-programming with LLM, with deep git integration. Hermes is a generalist agent with coding skills.
2. **Git workflow:** Aider makes automatic commits, maintains diff view, tracks changes. Hermes has git tools but not Aider's dedicated workflow.
3. **Templates:** Aider supports multiple templates. Hermes too, with the advantage of Codex OAuth.
4. **Platforms:** Hermes delivers via couriers. Aider is terminal-only.
5. **Skills:** Hermes has procedures for coding (skills bundled:`software-development/`). Aider does not have a skill system.

**Hermes wins:** general use beyond code, automations via message, multi-provider with cost control.

**Aider wins:** pure pair-programming with focus on diff and commit quality, superior git integration.

---

## 3. Costs and Models

### Why Codex via ChatGPT Plus/Pro is cheaper

Codex OAuth uses the ChatGPT account as the inference provider — no OpenAI API calls. This means:

- **ChatGPT Plus ($20/month):** access to GPT-4o, no limit per token — unlimited use within the plan rate limit.
- **ChatGPT Pro ($200/month):** access to o3, o4-pro, GPT-5 with higher limits.
- **No API key required** — Hermes authenticates via OAuth browser and uses the session token.

In comparison, via OpenAI's direct API:
- GPT-4o: ~$2.50/1M tokens input + $10/1M output
- For intense use (e.g. 10 long conversations/day with context of 32k tokens each) the monthly cost can exceed $100.

With ChatGPT Plus ($20/month flat), the same usage is included in the plan — potentially saving 5x or more for active users.

**OpenRouter** offers routing for multiple models at competitive prices:
-`openrouter/owl-alpha`: free (free model on OpenRouter)
- Gemini Flash: ~$0.075/1M tokens (ultra-cheap option)
- Claude via OpenRouter: variable per model

**Recommended models in 2026 (mentioned in Hermes v0.13.0 documentation):**
-`gpt-5.3-codex`— standard for Codex OAuth (supports vision)
-`deepseek/deepseek-v4-pro`— added in v0.13.0
-`x-ai/grok-4.3`— added in v0.13.0
-`MiniMax-M2.7`— via OAuth without API key
-`tencent/hy3-preview`— added by contributor in v0.13.0
- Local models via Ollama or any OpenAI-compatible endpoint

**Note:** Pricing for GPT-5.5 and "Claude 4" specifically does not appear in the Hermes v0.13.0 documentation. The Claude model mentioned in the config examples is`claude-opus-4.6`via GMI or OpenRouter.

---

## 4. Hostinger one-click Hermes

### How auto-deploy works

Hostinger offers VPS with one-click deployment of applications via its management interface. Hermes Agent (by NousResearch) was **not explicitly listed** as a one-click application in Hostinger's public catalog at the time of this search. The catalog includes n8n, Docker, and other applications, but not "Hermes Agent" with exact name.

The method documented by the community (e.g. repository`paras200/hermes-anywhere`) uses Terraform + Docker Compose to deploy to multiple clouds. In the case of Hostinger specifically, the flow would be:
1. Provision KVM via Hostinger panel
2. SSH into the server
3. Install Docker (`one-click Docker app`available on Hostinger)
4. Clone`NousResearch/hermes-agent`and run via`docker compose up`### KVM specifications (prices in May/2026 with discount)

| Plan | vCPUs | RAM | NVMe | Band | Price with discount | Renovation |
|-------|-------|-----|------|-------|-------------------|-----------|
| KVM 1 | 1 core | 4GB | 50GB | 4TB | $6.49/month | $11.99/month |
| KVM 2 | 2 colors | 8GB | 100GB | 8TB | $8.99/month | $14.99/month |
| KVM 4 | 4 colors | 16GB | 200GB | 16TB | $12.99/month | $28.99/month |
| KVM 8 | 8 colors | 32GB | 400GB | 32TB | $25.99/month | $49.99/month |

Source: https://www.hostinger.com/vps-hosting (consulted 10/05/2026)

For Hermes Agent running with messaging gateway (Telegram + Discord) and crons, **KVM 2** (8 GB RAM, 2 vCPUs) is recommended for personal use. **KVM 4** for multiple profiles or multi-agent kanban.

All plans use: HPE/Dell hardware, AMD EPYC processors, NVMe SSD, KVM virtualization with full isolation.

---

## 5. Common Troubleshooting

### Telegram does not respond

1. Check if the gateway is running:`hermes gateway status`2. Confirm configuration:`hermes gateway setup`(re-runs the wizard)
3. Check`~/.hermes/logs/gateway.log`for connection errors
4. Check Telegram bot token in`~/.hermes/.env` (`TELEGRAM_TOKEN`)
5. Confirm`TELEGRAM_HOME_CHANNEL`configured (used by cron delivery)
6. In Docker: check that the container has access to the internet (firewall/iptables)

### Dashboard does not open

Hermes has a web interface (`web/`in the repository). If running on VPS without direct public IP:
1. Use SSH tunnel:`ssh -L 8080:localhost:8080 user@vps`to access locally
2. Alternative: configure nginx as reverse proxy
3. Check if the process`hermes`is running on the correct port

### .env of container vs root in Docker

Classic Docker Problem:`.env`from the host is not automatically made available in the container.

- The`.env`correct is in`~/.hermes/.env`**inside the container** (in the assembled volume)
- No`docker-compose.yml`, mount the volume:`~/.hermes:/root/.hermes`(or equivalent)
- **Never** place API keys in`.env`from the root of the project without mounting correctly
- Check with`docker exec -it hermes env | grep TELEGRAM`that the vars are visible
- Hermes logs have self-writing secrets:`~/.hermes/logs/errors.log`### Automatic Context Compression

Hermes automatically compresses long conversations before reaching the model's context limit. Compressor is a separate LLM call (configurable for provider other than the main one):```yaml
compression:
  provider: "openrouter"
  model: "google/gemini-2.5-flash"   # modelo barato para compressão
```The command`/compress`manual compression force.`/usage`shows used tokens. The compression count appears in the TUI status bar (added in v0.13.0).

To use alternative compressor without LLM (LCM plugin — lossless context management):```yaml
context:
  engine: "lcm"
```### Timezone: UTC container vs local

The Docker container runs in UTC by default. Hermes solves this via explicit configuration in`config.yaml`:

```yaml
timezone: "America/Sao_Paulo"   # IANA timezone string
```This affects: timestamps in logs, cron scheduling, and the time injected into the system prompt. Without this configuration, crons scheduled for "09:00" will fire at 09:00 UTC — which is 06:00 in Brasilia (UTC-3).

Alternative via Docker: define`TZ=America/Sao_Paulo`in the container's environment variables.

---

## 6. Real Use Cases

The following are community-documented patterns and identified mentions in repositories and forks in 2025-2026:

### Case 1: Personal "Chief-of-Staff"

Repository:`kbanc85/claudia-autonomous`(Hermes fork v0.7.0, MIT, running 100% local via Ollama + SQLite)

Usage: Personal assistance agent with reliable memory, no API key. Responds via CLI and stores decisions in local SQLite. Frequent pattern: SOUL.md customized with "executive assistant" persona, MEMORY.md with projects and meetings context.

### Case 2: Multi-cloud deployment with Terraform

Repository:`paras200/hermes-anywhere`Deploy Hermes on 5 different clouds (Hetzner, Oracle Always Free, GCP, AWS Lightsail, DigitalOcean) via Terraform modules + a single docker compose. Demonstrates the pattern of hosting the gateway permanently on the cheapest cloud (Oracle Free Tier is mentioned).

### Case 3: WeChat Bridge

Repository:`AaronWong1999/hermesclaw`(HermesClaw)

Mentioned in the official Hermes documentation as "Community WeChat bridge: Run Hermes Agent and OpenClaw on the same WeChat account." Allows you to use Hermes within WeChat groups and chats in China.

### Case 4: OpenClaw Migration → Hermes

Official Skill:`official/migration/openclaw-migration`Hermes includes automatic migration wizard from OpenClaw (predecessor): imports SOUL.md, MEMORY.md, USER.md, skills, API keys, Telegram/Discord config, and workspace instructions. It shows that Hermes is a direct successor to OpenClaw with a migrated user base.

### Case 5: Multi-agent Kanban (v0.13.0)

Documented in the v0.13.0 release notes:

> "Spin up a durable board, drop tasks on it, and let multiple Hermes workers pick them up, hand off, and close them out. Heartbeats, reclaim, zombie detection, retry budgets, and a hallucination gate keep the team honest."

Standard: an orchestrator profile creates Kanban board, defines tasks, and multiple workers (other Hermes profiles) pick up and complete tasks. Used for content generation, data analysis, and code automation projects in parallel.

### Case 6: Blog / RSS automation skill

Recurring pattern in cron documentation:```
/cron add "every 1h" "Summarize new feed items" --skill blogwatcher
```The skill`blogwatcher`monitors RSS feeds, summarizes new items and delivers via Telegram. Variation with multiple skills:```
/cron add "every 6h" "Combine local events and nearby places into a brief" --skill blogwatcher --skill maps
```### Case 7: Server Watchdog

Officially documented no-agent pattern:```bash
hermes cron create "every 5m" \
  --no-agent \
  --script memory-watchdog.sh \
  --deliver telegram \
  --name "memory-watchdog"
```Script monitors memory/disk and delivers alerts only when something is wrong. Standard used in production VPS for monitoring without token costs.

**Note about YouTube/blogs/X posts:** No specific YouTube videos or verifiable X/Twitter posts with direct URLs were found in this session's search. The repository was created in July 2025 and has grown to 141k stars, indicating widespread adoption, but video/blog content was not indexed in the sources consulted. It is recommended to search on YouTube for "hermes agent nousresearch tutorial" and on X for "@NousResearch hermes agent".

---

## Sources

1. Official repository: https://github.com/NousResearch/hermes-agent
2. GitHub stats API: https://api.github.com/repos/NousResearch/hermes-agent
3. Skills Documentation: https://hermes-agent.nousresearch.com/docs/user-guide/features/skills
4. Memory Documentation: https://hermes-agent.nousresearch.com/docs/user-guide/features/memory
5. Cron Documentation: https://hermes-agent.nousresearch.com/docs/user-guide/features/cron
6. Configuration Documentation: https://hermes-agent.nousresearch.com/docs/user-guide/configuration
7. Gateway Documentation: https://hermes-agent.nousresearch.com/docs/user-guide/messaging
8. Architecture Documentation: https://hermes-agent.nousresearch.com/docs/developer-guide/architecture
9. Bundled Skills Catalog: https://hermes-agent.nousresearch.com/docs/reference/skills-catalog
10. Optional Skills Catalog: https://hermes-agent.nousresearch.com/docs/reference/optional-skills-catalog
11. Release Notes v0.13.0: https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.13.0.md
12. Skills Hub: https://agentskills.io
13. Hostinger VPS (KVM Pricing): https://www.hostinger.com/vps-hosting
14. Fork hermes-anywhere (Deploy Terraform): https://github.com/paras200/hermes-anywhere
15. Fork claudia-autonomous (Ollama site): https://github.com/kbanc85/claudia-autonomous
16. HermesClaw (WeChat bridge): https://github.com/AaronWong1999/hermesclaw
17. Skill Vault (skills_guard fork): https://github.com/aibuild-lab/skill-vault
18. Context Files Documentation: https://hermes-agent.nousresearch.com/docs/user-guide/features/context-files