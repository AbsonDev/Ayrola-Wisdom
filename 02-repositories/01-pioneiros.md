# Pioneiros — Harnesses em Produção

Os projetos que estão definindo o estado da arte em 2026.

---

## 1. Prime Agent (PrimeIntellect-ai)

**Repo:** https://github.com/PrimeIntellect-ai/prime-agent  
**Stars:** ~8k-15k  
**Linguagem:** Python  
**Licença:** Open-source

### O que é
Harness open-source para coding e pesquisa, construído em torno de duas abstrações: **RLM** (Recursive Language Model) e **Continual Harness** (auto-aprimoramento).

### Inovações
- **Kernel IPython REPL persistente** — estado entre turnos
- **Subagentes recursivos nativos** — `rlm.spawn()` é primitiva da API
- **Auto-refinamento do harness** — `/refine` atualiza skills/memórias/rotas
- **Multi-modelo com fallback** — OpenRouter, Anthropic, 9Router local
- **Jev decision layer** — decisões baratas ($0) antes do LLM caro

### Limitações
- Runtime Python (GIL, startup lento)
- Ainda em beta (estabilidade, vazamento de processos MCP)
- Ecossistema pequeno vs Claude Code

### Papers base
- RLM (Alex Zhang, Dec 2025)
- RAH (Lumer, Jun 2026)
- Continual Harness (Karten, May 2026)
- Self-Harness (Zhang, Jun 2026)

---

## 2. OpenClaw

**Repo:** https://github.com/openclaw/openclaw  
**Stars:** ~390k+ (o #1 do GitHub em 2026)  
**Linguagem:** TypeScript  
**Licença:** Open-source

### O que é
Assistente AI self-hosted que roda no seu computador e se conecta aos canais que você já usa (Discord, Slack, Telegram, WhatsApp, iMessage).

### Inovações
- **Multi-canal nativo** — um agente, todos os canais
- **200+ agent templates** — `mergisi/awesome-open-claw-agents`
- **Memória persistente multi-ferramenta**
- **94 repositórios oficiais** — é uma plataforma, não só um agente

### Diferencial
- Foco em automação de vida/trabalho (não coding-first)
- Patterns de harness transferíveis para outros domínios
- Comunidade massiva

---

## 3. DeepSeek Harness

**Repo:** https://github.com/deepseek-ai/deepseek-harness  
**Stars:** ~95k em 2 dias (crescimento explosivo)  
**Linguagem:** TypeScript  
**Licença:** MIT

### O que é
Harness open-source da DeepSeek AI, focado no princípio **"Everything is a Plugin"**.

### Inovações
- **Plugin-first radical** — cada capability é um plugin plugável, não built-in
- **Developer preview** — iterando rapidamente
- **5 projetos derivados da comunidade:** SpecFlow, GitFlow, Guardian, Code Intel, VS Code extension

### Por que acompanhar
- Primeiro harness major chinês open-source
- Se adotar o modelo DeepSeek, pode virar o "OpenCode do Leste Asiático"
- MIT license = adoção corporativa fácil

---

## 4. OpenCode

**Repo:** https://github.com/anomalyco/opencode  
**Stars:** ~161k  
**Linguagem:** TypeScript  
**Licença:** MIT

### O que é
Terminal-first coding agent open-source, resposta da comunidade ao Claude Code.

### Inovações
- **Zen lane** — zero custo via systemone/jev-1.13-free
- **Hot reload de skills** — sem reiniciar sessão
- **Sessão shell persistente** — estado entre prompts
- **MCP nativo** — Model Context Protocol como padrão

### Diferencial
- Mais próximo do Prime Agent em filosofia (open-source, terminal-first)
- Comunidade ativa, crescendo rápido
- MIT license

---

## 5. Claude Code (Anthropic)

**Repo:** Não open-source (binário)  
**Stars:** N/A  
**Linguagem:** TypeScript  
**Licença:** Proprietário

### O que é
CLI terminal-first da Anthropic, referência de facto para coding agents.

### Inovações
- **MCP nativo** — Model Context Protocol como padrão de tools
- **Sandbox real** — OS-level (Seatbelt/bubblewrap)
- **Subagentes limitados** — spawning com depth cap (2026)
- **39% de adoção** entre devs profissionais (Maio-Julho 2026)

### Diferencial
- Integração nativa com modelos Anthropic
- Ecossistema MCP maduro
- Não open-source = limitado para customização profunda

---

## 6. Cursor

**Repo:** Não open-source  
**Stars:** N/A  
**Linguagem:** TypeScript  
**Licença:** Proprietário

### O que é
IDE completa com agente embutido. Define o padrão de UX para coding agents.

### Inovações
- **IDE-first** — agente integrado ao editor, não CLI
- **Composer** — multi-file editing com preview
- **Cloud agents** — execução remota

### Diferencial
- UX superior para desenvolvedores
- Não open-source = caixa preta
