# Decisões Técnicas para o Ayrola Harness

Tabela de decisões com trade-offs. Use para definir a proposta.

---

## Decisão 1: Linguagem do Runtime

| Opção | Prós | Contras | Recomendação |
|---|---|---|---|
| **Python** | Ecossistema maduro, prototipagem rápida, bibliotecas de agente abundantes | GIL, startup lento, memória alta | ✅ Curto prazo |
| **Rust** | Performance, segurança, zero unsafe | Curva de aprendizado, ecossistema menor | 🔄 Longo prazo (kernel) |
| **TypeScript** | Type safety, serverless, Next.js | Runtime Node.js, menos maduro para agentes | 🔄 Se for serverless-first |
| **Go** | Concorrência nativa, simples, deploy fácil | Ecossistema de agentes menor | 🔄 Se for concorrência-first |

**Recomendação:** Python para o harness definition, Rust para o kernel (se necessário).

---

## Decisão 2: Kernel Persistente

| Opção | Prós | Contras |
|---|---|---|
| **IPython REPL** (Prime Agent) | Estado real, Jupyter integration | Pesado, startup lento |
| **Shell session** (OpenCode) | Leve, simples | Menos poderoso que REPL |
| **conkernel/clikernel** | Biblioteca independente, plugável | Dependência externa |
| **Sem kernel** (Claude Code) | Simples, sem estado | Tarefas longas recomeçam |

**Recomendação:** Use `conkernel` ou implemente um kernel leve próprio.

---

## Decisão 3: RLM (Recursive Language Model)

| Opção | Prós | Contras |
|---|---|---|
| **RLM nativo** | Paradigma 2026, +9.6% sobre baseline | Complexidade, custo de inferência |
| **Subagentes manuais** | Simples, previsível | Menos eficiente para tarefas complexas |
| **Híbrido** (decide quando recursar) | Flexível | Mais complexo |

**Recomendação:** Híbrido — o modelo decide quando recursar, com depth limit configurável.

---

## Decisão 4: Auto-Melhoria

| Opção | Prós | Contras |
|---|---|---|
| **Nível 1** (prompt optimization) | Simples, seguro | Ganho limitado |
| **Nível 2** (skill/harness refinement) | Comprovado, mensurável | Requer validação |
| **Nível 3** (harness self-modification) | Diferencial de pesquisa | Risco de instabilidade |

**Recomendação:** Nível 2 como default. Nível 3 como feature experimental.

---

## Decisão 5: Decisão Barata (Jev/System One)

| Opção | Prós | Contras |
|---|---|---|
| **Jev nativo** | $0, 400ms, integração direta | Dependência de serviço externo |
| **System One local** | Offline, $0 | Setup inicial |
| **LLM para tudo** | Simples | 100-200x mais caro |

**Recomendação:** Jev ou System One local. ROI óbvious.

---

## Decisão 6: Sandbox

| Opção | Prós | Contras |
|---|---|---|
| **Docker** | Simple, isolamento bom | Overhead de container |
| **E2B** | MicroVM, gerenciado | Custo |
| **Anthropic srt** | OS-level, free | macOS/Linux only |
| **Sem sandbox** | Simple | Inseguro |

**Recomendação:** Docker para desenvolvimento, E2B/srt para produção.

---

## Decisão 7: Multi-Modelo

| Opção | Prós | Contras |
|---|---|---|
| **Multi-modelo com fallback** | Resiliência, custo otimizado | Complexidade de roteamento |
| **Modelo único** | Simple | Ponto único de falha |

**Recomendação:** Multi-modelo com fallback automático.

---

## Decisão 8: Plugin System

| Opção | Prós | Contras |
|---|---|---|
| **Plugin-first** (DeepSeek) | Extensível, comunidade | Overhead de abstração |
| **Built-in** (Prime Agent) | Simple, performático | Menos extensível |
| **MCP nativo** | Padrão emergente | Ecossistema ainda maduro |

**Recomendação:** MCP nativo + plugins internos.

---

## Resumo de Decisões Recomendadas

| Decisão | Recomendação |
|---|---|
| Linguagem | Python (curto prazo), Rust (longo prazo) |
| Kernel | conkernel ou kernel leve próprio |
| RLM | Híbrido (modelo decide quando recursar) |
| Auto-melhoria | Nível 2 (skill/harness refinement) |
| Decisão barata | Jev ou System One local |
| Sandbox | Docker (dev), E2B/srt (prod) |
| Multi-modelo | Sim, com fallback |
| Plugins | MCP nativo + plugins internos |
