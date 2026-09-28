# Orquestração & CI/CD — Agentes sem Humanos no Loop

---

## 1. Aeon (aeonfun/aeon)

**Repo:** https://github.com/aeonfun/aeon  
**Stars:** Crescimento rápido  
**Linguagem:** TypeScript/Markdown  
**Foco:** GitHub Actions

### O que é
Framework de agente autônomo mais recente. Rode sem atenção na sua GitHub Actions, com auto-healing de skills, e orquestre Claude Code, Codex, Grok e mais.

### Inovações
- **GitHub Actions nativo** — deploy zero-infra
- **Auto-healing de skills** — agente se recupera automaticamente de falhas
- **Markdown skills** — cada capability é um arquivo Markdown
- **Orquestra backend múltiplo** — Claude Code, Codex, Grok como backends plugáveis

### Arquitetura
```
GitHub Actions (CI/CD)
    ↓
Aeon Framework
    ├── Markdown Skills (self-contained)
    ├── Claude Code Backend
    ├── Codex Backend
    └── Grok Backend
```

### Por que importa
- Elimina o operador humano do loop
- Baixa barreira de entrada (Markdown skills)
- Orquestração multi-backend é o futuro

---

## 2. GitHub Copilot Agent Runtime

**Repo:** Não open-source  
**Linguagem:** Rust (migrado em 2026)  
**Foco:** CLI + API

### O que é
Runtime agente que roda o Copilot CLI. Agora com 800k+ linhas de Rust.

### Por que importa
- Mais usado como runtime para soluções Microsoft/GitHub/ecossistema
- Sandbox OS-level (Seatbelt macOS / bubblewrap Linux)
- Proxy de rede filtrando (security)

---

## 3. Claude Code Subagentes

**Repo:** Não open-source  
**Data:** 2026 (atualização)  
**Inovações:**
- Subagentes param spawnar mais subagentes por padrão (desativado)
- Cap de 20 subagentes concorrentes
- Budget caps integrados
- Depth limit configurável

### Documentação
- https://www.reddit.com/r/ClaudeAI/comments/1titt3o/
- https://hidekazu-konishi.com/entry/claude_code_subagents/

---

## Análise Comparativa

| Framework | CI/CD | Auto-healing | Multi-backend | Self-hosting |
|---|---|---|---|---|
| Aeon | ✅ GitHub Actions | ✅ | ✅ | ✅ |
| Claude Code | ❌ (local) | ❌ | ❌ | Parcial |
| OpenCode | ❌ (local) | ❌ | ❌ | ✅ |
| Prime Agent | ❌ (local) | ✅ (`/`refine) | ✅ | ✅ |
