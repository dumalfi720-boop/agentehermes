# Hermes Agent — Pesquisa Técnica Consolidada

**Data:** 10 de maio de 2026
**Fontes:** Documentação oficial, GitHub, Hostinger VPS

---

## 1. Hermes Agent — Fundamentos Técnicos

### Repositório Oficial

- **URL:** https://github.com/NousResearch/hermes-agent
- **Licença:** MIT
- **Organização:** NousResearch (Nous Research)
- **Slogan:** "The agent that grows with you"
- **Estrelas:** 141.404
- **Forks:** 21.979
- **Issues abertas:** 9.626
- **Commits:** 7.938
- **Versão mais recente:** v0.13.0 (lançada 7 de maio de 2026) — chamada "The Tenacity Release"
- **Criado em:** 22 de julho de 2025 — crescimento de ~141k estrelas em menos de 1 ano
- **Último commit:** 10 de maio de 2026 (atualizado no mesmo dia desta pesquisa)

### Linguagem e Dependências

- **Linguagem principal:** Python 88,7%
- **Frontend/TUI:** TypeScript 7,9%
- **Scripts:** Shell 0,5%, Nix 0,3%
- **Dependências-chave:** veja `.env.example` e `requirements*.txt` no repositório. A stack inclui asyncio, SQLite (sessions + FTS5), Docker para isolamento, e adaptadores de plataformas de mensagem.
- **Documentação oficial:** https://hermes-agent.nousresearch.com/docs

### Arquitetura Geral

O núcleo é a classe `AIAgent` em `agent/run_agent.py` — o loop síncrono de orquestração que lida com: seleção de provedor, construção de prompt, execução de ferramentas, retries, fallback, callbacks, compressão e persistência. Suporta três modos de API para backends distintos.

Estrutura de diretórios do repositório:
```
agent/          → loop principal, run_agent.py
tools/          → 40+ ferramentas registradas via tools/registry.py
cron/           → agendador de tarefas
acp_adapter/    → integração ACP (Agent Communication Protocol)
acp_registry/   → catálogo de agentes
tui_gateway/    → terminal UI + gateway de mensagens
ui-tui/         → frontend TUI em TypeScript
web/            → interface web
website/        → documentação Docusaurus
```

Fluxo de dados em três caminhos:
1. **CLI Session** → `cli.py` → `AIAgent` → tool loop → resposta
2. **Gateway Message** (Telegram, Discord etc.) → `tui_gateway/` → `AIAgent` → delivery
3. **Cron Job** → `cron/` scheduler → `AIAgent` fresh session → delivery

### Como o Agente Carrega Contexto

O Hermes usa um sistema hierárquico de arquivos de contexto. Todos residem em `~/.hermes/`:

| Arquivo | Propósito | Escopo |
|---------|-----------|--------|
| `SOUL.md` | Identidade primária do agente — ocupa o slot #1 do system prompt | Global (`~/.hermes/SOUL.md`) |
| `.hermes.md` / `HERMES.md` | Instruções de projeto (maior prioridade) | Sobe até git root |
| `AGENTS.md` | Instruções de projeto, convenções de código | Walk recursivo de subdiretórios |
| `CLAUDE.md` | Contexto Claude Code (também detectado) | CWD apenas |
| `.cursorrules` | Convenções Cursor IDE | CWD apenas |
| `.cursor/rules/*.mdc` | Módulos de regras Cursor | CWD apenas |

Regra de prioridade: apenas **um** tipo de contexto de projeto é carregado por sessão, na ordem: `.hermes.md` → `AGENTS.md` → `CLAUDE.md` → `.cursorrules`. O `SOUL.md` é sempre carregado independentemente.

**Memória persistente** — dois arquivos em `~/.hermes/memories/`:
- `MEMORY.md` — notas pessoais do agente (ambiente, fatos aprendidos). Limite: 2.200 chars (~800 tokens).
- `USER.md` — perfil do usuário (preferências, estilo de comunicação). Limite: 1.375 chars (~500 tokens).

Ambos são injetados no system prompt como snapshot frozen no início de cada sessão. O agente gerencia a própria memória via ferramenta `memory` (add, replace, remove). Quando cheio, consolida entradas para abrir espaço.

Além disso, `session_search` permite busca em histórico de conversas armazenadas em SQLite com FTS5 (`~/.hermes/state.db`).

### Anatomia do SKILL.md — YAML Front Matter

Campos exatos do front matter:

```yaml
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
```

**Progressive Disclosure** — padrão token-eficiente em 3 níveis:
- `Level 0: skills_list()` → lista `[{name, description, category}, ...]` (~3k tokens)
- `Level 1: skill_view(name)` → conteúdo completo + metadados
- `Level 2: skill_view(name, path)` → arquivo de referência específico

O agente só carrega o conteúdo completo quando realmente precisa da skill.

**Estrutura de diretórios de uma skill:**
```
~/.hermes/skills/
├── mlops/
│   └── axolotl/
│       ├── SKILL.md          # Instruções principais (obrigatório)
│       ├── references/       # Docs adicionais
│       ├── templates/        # Formatos de output
│       ├── scripts/          # Scripts auxiliares
│       └── assets/           # Arquivos suplementares
```

Skills seguem o padrão aberto **agentskills.io** (https://agentskills.io/specification).

### Sistema de Crons

O Hermes expõe gerenciamento de cron via ferramenta única `cronjob` com operações no estilo action:

**Criação via chat:**
```
/cron add 30m "Remind me to check the build"
/cron add "every 2h" "Check server status"
/cron add "every 1h" "Summarize new feed items" --skill blogwatcher
/cron add "every 1h" "Use both skills" --skill blogwatcher --skill maps
```

**CLI standalone:**
```bash
hermes cron create "every 1d at 09:00" \
  "Audit open PRs, summarize CI health, and post to #eng" \
  --workdir /home/me/projects/acme
```

**Flags principais:**

| Flag | Equivalente cron | Função |
|------|-----------------|--------|
| `--workdir <path>` | `WORKDIR` | Define diretório de trabalho — carrega AGENTS.md/CLAUDE.md dali |
| `--no-agent` | `NOAGENT` | Modo script-only, pula o LLM completamente |
| `--deliver <platform>` | `CONTEXTFROM` / entrega | Define destino de entrega (telegram, discord, all, origin) |
| `--skill <name>` | skill injection | Injeta skill no início da sessão fresh |

**Modo no-agent (NOAGENT):** Para jobs que não precisam de LLM — watchdogs, alertas de disco/memória, heartbeats:
```bash
hermes cron create "every 5m" \
  --no-agent \
  --script memory-watchdog.sh \
  --deliver telegram \
  --name "memory-watchdog"
```
- stdout vazio → tick silencioso (sem entrega)
- saída não-zero → alerta de erro entregue
- scripts `.sh/.bash` rodam sob `/bin/bash`; outros rodam no Python interpreter

**Gateway scheduler behavior** (como funciona em cada tick):
1. Carrega jobs de `~/.hermes/cron/jobs.json`
2. Verifica `next_run_at` vs horário atual
3. Inicia sessão fresh de `AIAgent` para cada job vencido
4. Injeta skills anexadas na sessão
5. Executa o prompt até conclusão
6. Entrega a resposta final
7. Atualiza metadados e próximo horário agendado

File lock em `~/.hermes/cron/.tick.lock` previne double-run.

**Nota sobre WAT (Workflow-Agent-Tools):** O termo "WAT framework" não aparece explicitamente na documentação oficial do Hermes como uma sigla separada. A documentação descreve o padrão como "Skills System + Cron + Tools" sem essa nomenclatura específica.

### Skills Hub

- **URL:** https://agentskills.io
- **Padrão aberto:** https://agentskills.io/specification
- **Discord do hub:** https://discord.gg/MKPE9g8aUy

O Skills Hub é um registry independente de skills compatíveis com Hermes. Existem múltiplas fontes de instalação:

1. `official` — skills bundled no repositório hermes-agent (`optional-skills/`)
2. `skills-sh` — hub externo agentskills.io
3. GitHub (usuário/repo/path)
4. URL direta (`https://example.com/SKILL.md`)
5. `well-known` — auto-descoberta via `/.well-known/agent-skills`

**Skills bundled** (ativas por padrão em `~/.hermes/skills/`) — categorias:
- apple, autonomous-ai-agents, creative, data-science, devops, dogfood, email, gaming, github, mcp, media, mlops, note-taking, productivity, red-teaming, research, smart-home, social-media, software-development, yuanbao

**Skills opcionais oficiais** (em `optional-skills/`, instalação explícita) — 16 categorias:
- autonomous-ai-agents, blockchain, communication, creative, devops, dogfood, email, finance, health, mcp, migration, mlops, productivity, research, security, web-development

O número exato de skills não está publicado como um único número na documentação; a contagem cresce a cada release. No v0.13.0 foram adicionadas 6 novas skills opcionais (Shopify Admin+Storefront, here.now, shop-app, Anthropic financial-services bundle, kanban-video-orchestrator, searxng-search).

### Provedores de Inferência Suportados

| Provider | Como usar | Custo |
|----------|-----------|-------|
| Codex OAuth | `provider: "codex"` — usa token OAuth da conta ChatGPT, sem API key | Incluso no plano ChatGPT Plus/Pro |
| OpenRouter | `OPENROUTER_API_KEY` + `provider: "openrouter"` — roteia para qualquer modelo | Pay-per-token variável |
| Nous Portal | `hermes auth` + `provider: "nous"` | Via conta NousResearch |
| OpenAI API | `OPENAI_API_KEY` + `OPENAI_BASE_URL` | Pay-per-token |
| MiniMax OAuth | `provider: "minimax-oauth"` — login via browser | Sem API key |
| Local/Custom | `base_url: "http://localhost:1234/v1"` — qualquer endpoint OpenAI-compatible | Gratuito (hardware local) |
| GMI | `GMI_API_KEY` | Pay-per-token |

Modelos reconhecidos automaticamente (tool-use enforcement auto): `gpt`, `codex`, `gemini`, `gemma`, `grok`.

Modelos sem enforcement (confiados nativamente): Claude, DeepSeek, Qwen, LLaMA.

Modelos adicionados no v0.13.0: `deepseek/deepseek-v4-pro`, `x-ai/grok-4.3`, `openrouter/owl-alpha` (free), `tencent/hy3-preview`.

### Plataformas de Mensagem Suportadas

O gateway (`hermes gateway setup` + `hermes gateway start`) suporta:

Telegram, Discord, Slack, WhatsApp, Signal, SMS, Email, Home Assistant, Mattermost, Matrix, DingTalk, Feishu/Lark, WeCom, WeCom Callback, Weixin, **BlueBubbles (iMessage)**, QQBot, Yuanbao, Microsoft Teams, Google Chat, e browser.

Cada plataforma tem seu próprio toolset configurável. Plataformas como Signal (que não suportam edição de mensagem) têm controles granulares de verbosidade:
```yaml
display:
  platforms:
    signal:
      tool_progress: 'off'
    telegram:
      tool_progress: verbose
```

---

## 2. Comparações — Hermes vs Concorrentes

### Hermes vs Claude Code (Anthropic CLI)

1. **Gateway de mensagens:** Hermes tem gateway nativo para Telegram/Discord/WhatsApp/iMessage/20+ plataformas. Claude Code opera exclusivamente no terminal — sem gateway de mensagens.
2. **Cron nativo:** Hermes inclui agendador de tarefas com entrega em plataformas. Claude Code não tem cron embutido.
3. **Skills persistentes:** Hermes mantém skills como documentos YAML estruturados com progressive disclosure. Claude Code usa CLAUDE.md e system prompts livres, sem catálogo indexado.
4. **Provedores:** Hermes conecta a OpenAI, Anthropic, OpenRouter, local, MiniMax OAuth, Codex OAuth. Claude Code é acoplado ao modelo Claude via API Anthropic.
5. **Multi-agente Kanban:** v0.13.0 introduz orquestração de múltiplos agentes com heartbeat/reclaim/zombie detection. Claude Code não tem coordenação multi-agente nativa.

**Hermes ganha:** assistência pessoal 24/7 via mensagem, automações agendadas, usuários não-técnicos que preferem WhatsApp/Telegram.

**Claude Code ganha:** qualidade de raciocínio de código com Claude, integração profunda com IDE, review de PRs, refactoring complexo onde o modelo Claude é superior.

### Hermes vs OpenCode

1. **Gateway:** Hermes tem gateway completo para plataformas de mensagem; OpenCode é CLI puro de coding.
2. **Skills Hub:** Hermes tem ecosystem de skills em agentskills.io; OpenCode foca exclusivamente em tarefas de desenvolvimento de software.
3. **Memória:** Hermes tem MEMORY.md + USER.md persistentes com self-management. OpenCode não tem sistema de memória equivalente.
4. **Multi-provedor:** Hermes suporta qualquer backend OpenAI-compatible. OpenCode também suporta múltiplos providers, mas sem o nível de gerenciamento de OAuth/subscription.
5. **Integração:** Hermes tem skill bundled `opencode` para delegar tarefas de coding ao OpenCode CLI — os dois coexistem.

**Hermes ganha:** workflow de vida completa (não só código), automações, comunicação via mensagem.

**OpenCode ganha:** tarefas de engenharia de software puras onde o foco no código é vantagem.

### Hermes vs OpenAI Codex CLI

1. **Dependência:** Codex CLI requer conta OpenAI/ChatGPT para autenticação. Hermes usa Codex como *um dos* provedores opcionais via OAuth, podendo também rodar local.
2. **Skills:** Hermes tem sistema de skills extensível. Codex CLI não tem equivalente.
3. **Plataformas:** Hermes entrega via 20+ plataformas de mensagem. Codex CLI é terminal-only.
4. **Cron:** Hermes tem agendador integrado. Codex CLI não tem.
5. **Delegação:** Hermes tem skill bundled `codex` para delegar tasks ao Codex CLI como subagente.

**Hermes ganha:** automação, integração com mensageiros, multi-provedor.

**Codex CLI ganha:** uso direto no terminal para tarefas de código, sem infraestrutura adicional.

### Hermes vs Cursor

1. **Paradigma:** Cursor é IDE com copilot embutido. Hermes é agente de linha de comando com gateway — roda em servidor headless.
2. **Contexto de projeto:** Hermes detecta e respeita `.cursorrules` e `.cursor/rules/*.mdc` — os dois são complementares.
3. **Automação headless:** Hermes pode rodar em VPS sem interface gráfica, respondendo via Telegram. Cursor requer desktop.
4. **Skills:** Hermes tem procedimentos reutilizáveis com versionamento. Cursor usa regras e prompts de projeto.
5. **Custo:** Hermes é open source, roda com Codex subscription ($20/mês) ou modelos locais. Cursor tem plano pago separado.

**Hermes ganha:** ambientes sem interface gráfica, automações 24/7, custos com modelos alternativos.

**Cursor ganha:** desenvolvimento interativo no IDE, autocompleção inline, UX integrada ao editor.

### Hermes vs n8n (orquestração visual)

1. **Paradigma:** n8n usa workflows visuais com nós conectados. Hermes usa linguagem natural + skills — sem interface de desenho de fluxo.
2. **Raciocínio dinâmico:** Hermes usa LLM para decidir próximos passos em runtime. n8n executa sequências fixas pré-definidas.
3. **Infraestrutura:** n8n requer banco de dados, webhook server e configuração de nós. Hermes roda com `pip install` + uma API key.
4. **Curva de aprendizado:** n8n requer entendimento de cada nó e conector. Hermes aceita instruções em linguagem natural via chat.
5. **Hermes + n8n:** Hostinger lista n8n como aplicação one-click separada de Hermes; podem coexistir, com Hermes como camada de inteligência sobre automações n8n.

**Hermes ganha:** flexibilidade de raciocínio, configuração rápida, usuários que preferem chat a UI visual.

**n8n ganha:** workflows determinísticos complexos, integrações com centenas de serviços via nós oficiais, audit trail visual.

### Hermes vs LangChain / LangGraph

1. **Nível de abstração:** LangChain/LangGraph são frameworks Python para desenvolvedores construírem aplicações LLM. Hermes é um agente pronto para uso com instalador e interface.
2. **Usuário alvo:** LangGraph: desenvolvedor que quer definir grafos de agentes em código. Hermes: usuário que quer um agente funcionando via `hermes` no terminal.
3. **Skills:** SKILL.md são procedimentos em Markdown. LangChain usa chains e tools em Python — muito mais poder programático, mas requer código.
4. **Extensão:** Hermes tem plugin system para hooks. LangChain tem ecosystem de 500+ integrações.
5. **Deploy:** Hermes roda standalone com gateway. LangChain requer servidor próprio e infraestrutura.

**Hermes ganha:** setup zero-code, usuários não-desenvolvedores, agente pessoal sempre on.

**LangChain/LangGraph ganha:** aplicações customizadas complexas, controle fino de fluxo, integração em produtos de software.

### Hermes vs CrewAI

1. **Orchestração:** CrewAI coordena múltiplos agentes com papéis fixos (Researcher, Writer, etc.). Hermes v0.13.0 introduz Kanban multi-agente com workers dinâmicos.
2. **Persistência:** Hermes persiste sessões em SQLite com FTS5. CrewAI não tem sistema de persistência equivalente por padrão.
3. **Interface:** CrewAI é framework Python puro. Hermes tem TUI, CLI e gateway de mensagens.
4. **Skills:** Hermes tem SKILL.md como procedimentos reutilizáveis. CrewAI usa tools como funções Python.
5. **Instalação:** Hermes: `pip install hermes-agent && hermes setup`. CrewAI requer código Python para definir agentes e crew.

**Hermes ganha:** uso pessoal, configuração sem código, multi-plataforma de mensagem.

**CrewAI ganha:** orquestração de múltiplos agentes especializados em pipelines definidos por código.

### Hermes vs AutoGen (Microsoft)

1. **Paradigma:** AutoGen foca em conversações multi-agente onde agentes dialogam entre si. Hermes usa Kanban para coordenar workers com ownership de tarefas.
2. **Interface:** AutoGen é framework Python sem interface de usuário. Hermes tem TUI + gateway.
3. **Memória:** Hermes tem MEMORY.md/USER.md gerenciadas automaticamente. AutoGen depende do desenvolvedor implementar persistência.
4. **Integração enterprise:** AutoGen tem integração com Azure OpenAI e Teams. Hermes tem integração nativa com Teams via gateway.
5. **Open source:** Ambos MIT. AutoGen tem suporte corporativo Microsoft. Hermes tem comunidade NousResearch + fork `openclaw` migrado.

**Hermes ganha:** produto pronto para uso pessoal/pequenas equipes, sem desenvolvimento necessário.

**AutoGen ganha:** aplicações enterprise complexas, pesquisa em sistemas multi-agente, integração Azure.

### Hermes vs Aider

1. **Foco:** Aider é especialista em pair-programming com LLM, com deep git integration. Hermes é agente generalista com capacidade de coding.
2. **Git workflow:** Aider faz commits automáticos, mantém diff view, rastreia mudanças. Hermes tem ferramentas de git mas não o workflow dedicado de Aider.
3. **Modelos:** Aider suporta múltiplos modelos. Hermes também, com a vantagem do Codex OAuth.
4. **Plataformas:** Hermes entrega via mensageiros. Aider é terminal-only.
5. **Skills:** Hermes tem procedimentos para coding (skills bundled: `software-development/`). Aider não tem sistema de skills.

**Hermes ganha:** uso geral além de código, automações via mensagem, multi-provedor com controle de custo.

**Aider ganha:** pair-programming puro com foco em qualidade de diff e commits, integração git superior.

---

## 3. Custos e Modelos

### Por que Codex via ChatGPT Plus/Pro é mais barato

O Codex OAuth usa a conta ChatGPT como provedor de inferência — sem chamadas à API OpenAI. Isso significa:

- **ChatGPT Plus ($20/mês):** acesso a GPT-4o, sem limite por token — uso ilimitado dentro do plan rate limit.
- **ChatGPT Pro ($200/mês):** acesso a o3, o4-pro, GPT-5 com limites mais altos.
- **Sem API key necessária** — o Hermes autentica via OAuth browser e usa o token da sessão.

Em comparação, via API direta da OpenAI:
- GPT-4o: ~$2,50/1M tokens input + $10/1M output
- Para uso intenso (ex: 10 conversas longas/dia com contexto de 32k tokens cada) o custo mensal pode ultrapassar $100.

Com ChatGPT Plus ($20/mês fixo), o mesmo uso é incluído no plano — economizando potencialmente 5x ou mais para usuários ativos.

**OpenRouter** oferece roteamento para múltiplos modelos com preços competitivos:
- `openrouter/owl-alpha`: gratuito (modelo grátis no OpenRouter)
- Gemini Flash: ~$0,075/1M tokens (opção ultra-barata)
- Claude via OpenRouter: variável por modelo

**Modelos recomendados em 2026 (mencionados na documentação do Hermes v0.13.0):**
- `gpt-5.3-codex` — padrão para Codex OAuth (suporta vision)
- `deepseek/deepseek-v4-pro` — adicionado no v0.13.0
- `x-ai/grok-4.3` — adicionado no v0.13.0
- `MiniMax-M2.7` — via OAuth sem API key
- `tencent/hy3-preview` — adicionado por contribuidor no v0.13.0
- Modelos locais via Ollama ou qualquer endpoint OpenAI-compatible

**Nota:** Preços de GPT-5.5 e "Claude 4" especificamente não aparecem na documentação do Hermes v0.13.0. O modelo Claude mencionado nos exemplos de config é `claude-opus-4.6` via GMI ou OpenRouter.

---

## 4. Hostinger one-click Hermes

### Como funciona o auto-deploy

A Hostinger oferece VPS com deploy one-click de aplicações via sua interface de gerenciamento. O Hermes Agent (da NousResearch) **não estava listado explicitamente** como aplicação one-click no catálogo público da Hostinger na data desta pesquisa. O catálogo inclui n8n, Docker, e outras aplicações, mas não "Hermes Agent" com nome exato.

O método documentado pela comunidade (ex: repositório `paras200/hermes-anywhere`) usa Terraform + Docker Compose para deploy em múltiplas clouds. No caso da Hostinger especificamente, o fluxo seria:
1. Provisionar KVM via painel Hostinger
2. SSH no servidor
3. Instalar Docker (`one-click Docker app` disponível no Hostinger)
4. Clonar `NousResearch/hermes-agent` e rodar via `docker compose up`

### Especificações dos KVMs (preços em mai/2026 com desconto)

| Plano | vCPUs | RAM | NVMe | Banda | Preço c/ desconto | Renovação |
|-------|-------|-----|------|-------|-------------------|-----------|
| KVM 1 | 1 core | 4 GB | 50 GB | 4 TB | $6,49/mês | $11,99/mês |
| KVM 2 | 2 cores | 8 GB | 100 GB | 8 TB | $8,99/mês | $14,99/mês |
| KVM 4 | 4 cores | 16 GB | 200 GB | 16 TB | $12,99/mês | $28,99/mês |
| KVM 8 | 8 cores | 32 GB | 400 GB | 32 TB | $25,99/mês | $49,99/mês |

Fonte: https://www.hostinger.com/vps-hosting (consultado 10/05/2026)

Para Hermes Agent rodando com gateway de mensagens (Telegram + Discord) e crons, o **KVM 2** (8 GB RAM, 2 vCPUs) é recomendável para uso pessoal. O **KVM 4** para múltiplos perfis ou kanban multi-agente.

Todos os planos usam: hardware HPE/Dell, AMD EPYC processors, NVMe SSD, KVM virtualization com isolamento completo.

---

## 5. Troubleshooting Comum

### Telegram não responde

1. Verificar se o gateway está rodando: `hermes gateway status`
2. Confirmar configuração: `hermes gateway setup` (re-executa o wizard)
3. Checar `~/.hermes/logs/gateway.log` para erros de conexão
4. Verificar token do bot Telegram em `~/.hermes/.env` (`TELEGRAM_TOKEN`)
5. Confirmar `TELEGRAM_HOME_CHANNEL` configurado (usado pela entrega de cron)
6. Em Docker: verificar que o container tem acesso à internet (firewall/iptables)

### Dashboard não abre

O Hermes tem interface web (`web/` no repositório). Se rodando em VPS sem IP público direto:
1. Usar SSH tunnel: `ssh -L 8080:localhost:8080 user@vps` para acessar localmente
2. Alternativa: configurar nginx como reverse proxy
3. Checar se o processo `hermes` está rodando na porta correta

### .env do container vs root no Docker

Problema clássico de Docker: o `.env` do host não é automaticamente disponibilizado no container.

- O `.env` correto fica em `~/.hermes/.env` **dentro do container** (no volume montado)
- No `docker-compose.yml`, montar o volume: `~/.hermes:/root/.hermes` (ou equivalente)
- **Nunca** colocar API keys no `.env` da raiz do projeto sem montar corretamente
- Verificar com `docker exec -it hermes env | grep TELEGRAM` que as vars estão visíveis
- Logs do Hermes têm auto-redação de secrets: `~/.hermes/logs/errors.log`

### Compressão Automática de Contexto

Hermes comprime automaticamente conversas longas antes de atingir o limite de contexto do modelo. O compressor é uma chamada LLM separada (configurável para provedor diferente do principal):

```yaml
compression:
  provider: "openrouter"
  model: "google/gemini-2.5-flash"   # modelo barato para compressão
```

O comando `/compress` força compressão manual. `/usage` mostra tokens usados. A contagem de compressões aparece na status bar do TUI (adicionado no v0.13.0).

Para usar compressor alternativo sem LLM (plugin LCM — lossless context management):
```yaml
context:
  engine: "lcm"
```

### Timezone: container UTC vs local

O container Docker roda em UTC por padrão. Hermes resolve isso via configuração explícita em `config.yaml`:

```yaml
timezone: "America/Sao_Paulo"   # IANA timezone string
```

Isso afeta: timestamps em logs, agendamento de cron, e a hora injetada no system prompt. Sem essa configuração, crons agendados para "09:00" dispararão às 09:00 UTC — que é 06:00 em Brasília (UTC-3).

Alternativa via Docker: definir `TZ=America/Sao_Paulo` nas environment variables do container.

---

## 6. Casos de Uso Reais

A seguir, padrões documentados pela comunidade e menções identificadas em repositórios e forks em 2025-2026:

### Caso 1: "Chief-of-Staff" pessoal

Repositório: `kbanc85/claudia-autonomous` (fork Hermes v0.7.0, MIT, rodando 100% local via Ollama + SQLite)

Uso: agente de assistência pessoal com memória confiável, sem API key. Responde via CLI e armazena decisões em SQLite local. Padrão frequente: SOUL.md personalizado com persona de "assistente executivo", MEMORY.md com contexto de projetos e reuniões.

### Caso 2: Deploy multi-cloud com Terraform

Repositório: `paras200/hermes-anywhere`

Deploy do Hermes em 5 clouds diferentes (Hetzner, Oracle Always Free, GCP, AWS Lightsail, DigitalOcean) via Terraform modules + um único docker compose. Demonstra o padrão de hospedar o gateway permanentemente na nuvem mais barata (Oracle Free Tier é mencionado).

### Caso 3: Ponte WeChat

Repositório: `AaronWong1999/hermesclaw` (HermesClaw)

Mencionado na documentação oficial do Hermes como "Community WeChat bridge: Run Hermes Agent and OpenClaw on the same WeChat account." Permite usar Hermes dentro de grupos e chats do WeChat na China.

### Caso 4: Migração OpenClaw → Hermes

Skill oficial: `official/migration/openclaw-migration`

O Hermes inclui wizard automático de migração de OpenClaw (predecessor): importa SOUL.md, MEMORY.md, USER.md, skills, API keys, config do Telegram/Discord, e workspace instructions. Evidencia que Hermes é um sucessor direto do OpenClaw com base de usuários migrada.

### Caso 5: Kanban multi-agente (v0.13.0)

Documentado nos release notes do v0.13.0:

> "Spin up a durable board, drop tasks on it, and let multiple Hermes workers pick them up, hand off, and close them out. Heartbeats, reclaim, zombie detection, retry budgets, and a hallucination gate keep the team honest."

Padrão: um perfil orquestrador cria board Kanban, define tarefas, e múltiplos workers (outros perfis Hermes) pegam e completam tarefas. Usado para projetos de geração de conteúdo, análise de dados e automação de código em paralelo.

### Caso 6: Skill de automação de blog / RSS

Padrão recorrente na documentação de cron:
```
/cron add "every 1h" "Summarize new feed items" --skill blogwatcher
```

A skill `blogwatcher` monitora feeds RSS, sumariza novos items e entrega via Telegram. Variação com múltiplas skills:
```
/cron add "every 6h" "Combine local events and nearby places into a brief" --skill blogwatcher --skill maps
```

### Caso 7: Watchdog de servidor

Padrão no-agent documentado oficialmente:
```bash
hermes cron create "every 5m" \
  --no-agent \
  --script memory-watchdog.sh \
  --deliver telegram \
  --name "memory-watchdog"
```
Script monitora memória/disco e entrega alerta só quando algo está errado. Padrão usado em VPS de produção para monitoring sem custo de tokens.

**Nota sobre YouTube/blogs/posts X:** Não foram encontrados vídeos específicos do YouTube ou posts verificáveis no X/Twitter com URLs diretas na pesquisa desta sessão. O repositório foi criado em julho de 2025 e cresceu para 141k estrelas, indicando ampla adoção, mas o conteúdo de vídeo/blog não foi indexado nas fontes consultadas. Recomenda-se buscar no YouTube por "hermes agent nousresearch tutorial" e no X por "@NousResearch hermes agent".

---

## Fontes

1. Repositório oficial: https://github.com/NousResearch/hermes-agent
2. API GitHub stats: https://api.github.com/repos/NousResearch/hermes-agent
3. Documentação de Skills: https://hermes-agent.nousresearch.com/docs/user-guide/features/skills
4. Documentação de Memória: https://hermes-agent.nousresearch.com/docs/user-guide/features/memory
5. Documentação de Cron: https://hermes-agent.nousresearch.com/docs/user-guide/features/cron
6. Documentação de Configuração: https://hermes-agent.nousresearch.com/docs/user-guide/configuration
7. Documentação de Gateway: https://hermes-agent.nousresearch.com/docs/user-guide/messaging
8. Documentação de Arquitetura: https://hermes-agent.nousresearch.com/docs/developer-guide/architecture
9. Catálogo de Skills Bundled: https://hermes-agent.nousresearch.com/docs/reference/skills-catalog
10. Catálogo de Skills Opcionais: https://hermes-agent.nousresearch.com/docs/reference/optional-skills-catalog
11. Release Notes v0.13.0: https://github.com/NousResearch/hermes-agent/blob/main/RELEASE_v0.13.0.md
12. Skills Hub: https://agentskills.io
13. Hostinger VPS (preços KVM): https://www.hostinger.com/vps-hosting
14. Fork hermes-anywhere (deploy Terraform): https://github.com/paras200/hermes-anywhere
15. Fork claudia-autonomous (local Ollama): https://github.com/kbanc85/claudia-autonomous
16. HermesClaw (WeChat bridge): https://github.com/AaronWong1999/hermesclaw
17. Skill Vault (fork do skills_guard): https://github.com/aibuild-lab/skill-vault
18. Documentação de Context Files: https://hermes-agent.nousresearch.com/docs/user-guide/features/context-files
