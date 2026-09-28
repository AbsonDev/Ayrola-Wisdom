# DECISOES — Decisões Técnicas do Ayrola Harness

Tabela de decisões com trade-offs. Cada linha = uma decisão arquitectonica fechada.

---

## 1. Linguagem do Runtime

| Opção | Prós | Contras | Status |
|---|---|---|---|
| **Python** | Ecossistema AI maduro (LangChain, LlamaIndex) | GIL, startup lento, 500ms-2s por spawn | ✅ Short-term (FFI) |
| **Rust** | Performance, segurança, zero-cost, zero GC | Curva de aprendizado, ecossistema AI jovem | ✅ **CHOSEN (kernel)** |
| **TypeScript** | Type safety, serverless, Next.js | Node.js GC, menos maduro para agents | ❌ |
| **Go** | Goroutines simples, GC eficiente | Ecossistema AI menor | 🔄 Longo prazo (plano B) |

**Decisão:** **Rust para o kernel, desde o dia 1. Zero Python no kernel. Zero LangChain, zero LlamaIndex, zero FFI.**

**Por que Rust-first (não Python-first):**
- Sub-100ms spawning é impossível em Python (GIL + startup 500ms-2s)
- Event store append-only com memmap só é performante em Rust
- Sandbox-per-agent via namespaces Linux é natural em Rust (`nix` crate)
- Laya ONNX integra nativamente via `ort` crate — sem HTTP, sem Python
- GitHub Copilot provou o caminho: 800k+ linhas Rust (Q2 2026)

**Por que ZERO LangChain/LlamaIndex (revertido):**
- LangChain é heavy e opinionated; API mudou 3x nos últimos 2 anos
- LlamaIndex é framework RAG, não kernel de agentes
- FFI boundary = latência + complexidade de tipos + bugs de lifetime
- **O principal:** FFI com Python reintroduz o GIL — mata a paralelo real de 10+ subagentes
- A thesis "Rust kernel + Laya embedded" é **incompatível** com Python no caminho crítico

**Evidência:** Rust é 97x mais rápido que Python (CPU), 3-4x em I/O. Tokio tasks spawnam em microssegundos.

**Caminho revolucionário confirmado:** Rust kernel + Laya desde o Fase 0. Sem Python no kernel, sem HTTP para decision layer, sem dependência externa, sem FFI.

---

## 2. Kernel Persistente

| Opção | Prós | Contras | Status |
|---|---|---|---|
| IPython REPL (Prime Agent) | Estado real, Jupyter | Pesado, startup lento | ❌ |
| Shell session (OpenCode) | Leve | Menos poderoso | ❌ |
| **conkernel/clikernel** | Biblioteca, plugável | Dependência externa | ✅ Referência |
| **Tokio (propio)** | Zero custo, integrado | Precisa construir | ✅ **CHOSEN** |

**Decisão:** **Tokio** runtime com event store embutido — estado entre turnos via eventos append-only, não via REPL.

**Alternativa avaliada: `asupersync` (runtime do `pi_agent_rust`) — DESCARTADA**

O `pi_agent_rust` (agente Rust mais maduro, 469 .rs arquivos) **não usa Tokio**. Ele usa `asupersync`, um async runtime customizado do próprio autor (v0.5.0, single-maintainer).

| Critério | **Tokio** | **asupersync** |
|---|---|---|
| Maturidade | 6+ anos, padrão da indústria | Crate próprio, v0.5.0 |
| Ecossistema | Milhares de crates compatíveis | Isolado — só o que o autor mantém |
| Integração Laya/ONNX (`ort`) | Nativa | Requer bridge/adaptador |
| Sandbox namespaces (`nix`) | Nativa | Requer adaptação |
| Risco de bus factor | Bilhões de downloads | Single-maintainer |
| Contratação de devs | Trivial | Zero no mercado |

**Por que descartado:** `asupersync` resolve problemas específicos do `pi` (TUI Bubble Tea, TLS custom, reactor próprio). O Ayrola não precisa disso e perderia acesso ao ecossistema Rust. **Tokio é padrão, battle-tested e integra nativamente com todas as crates que o Ayrola precisa.**

**Evidência:** conkernel abstraiu o conceito de kernel persistente (2026). Tokio é maduro em Rust. `asupersync` provou que um runtime custom é viável mas não necessário.

---

## 3. RLM (Recursive Language Model)

| Opção | Prós | Contras | Status |
|---|---|---|---|
| RLM nativo | Paradigma 2026, +9.6% sobre baseline | Complexidade | ✅ **CHOSEN** |
| Subagentes manuais | Simples | Menos eficiente | ❌ |
| Híbrido | Flexível | Complexidade | 🔄 Em análise |

**Decisão:** RLM nativo com depth-bounded recursion (default: 3, configurável).

**Evidência:** RAH paper (Lumer, 2026) — subprocessos recursivos melhoram performance de 71.75% → 81.36% sobre Codex baseline. RLM (Alex Zhang, 2025) validou a abordagem.

---

## 4. Auto-Melhoria

| Nível | O que é | Exemplo | Status |
|---|---|---|---|
| 1 — Prompt optimization | Melhora o prompt | DSPy | Produção |
| 2 — Skill refinement | Melhora skills/memórias | Prime Agent `/refine` | Produção |
| 3 — Harness self-modification | Reescreve código do harness | Self-Harness paper | **🎯 Ayrola (nível 3)** |
| 4 — Continual learning | Aprende sem catástrofe | AgentCL | Pesquisa |

**Decisão:** Nível 3 como diferencial competitivo. Nível 2 (`/refine` equivalente) como MVP.

**Evidência:** Self-Harness paper (Zhang, 2026, 45 citações). Prime Agent `/refine` é nível 2.

---

## 5. Decision Layer (REAVALIADO — ensemble 3 tiers)

| Opção | Prós | Contras | Status |
|---|---|---|---|
| **Ensemble 3 tiers** (cache → pre-filter → LLM) | Cache 0ms, pre-filter ~5ms, LLM só quando necessário | Complexidade | ✅ **CHOSEN** |
| **Laya** (ONNX local) | Open weights, ~33ms, fine-tunable | Release set/2026, **sem crate Rust oficial**, Python-only, benchmark desaconselha uso head-to-head | **Candidato tier 2** |
| Jev (TypeSafe) | ~$0/call, local, sem Python | Closed weights (SystemOne) | Candidato tier 2 |
| LLM para tudo | Flexível | 500ms-2s, caro, overkill | ❌ |

**Decisão:** **Ensemble 3 tiers** — cache semântico → classificador ONNX pequeno (pre-filter) → LLM completo só quando confiança < threshold.

**Por que não Laya solo (revertido):**
- Distribuição oficial **Python** (`pip install laya`), sem crate Rust oficial
- `receptron/laya` no GitHub é Node/TypeScript, não Rust
- Exportar ONNX de modelo não-autoregressivo com router quebra exportadores
- Blog de comparação (alphamatch.ai): "Benchmark honesty — checkpoints base ficam perto de acaso sem especialização"
- Decisão tomada com 9 dias de evidência sobre modelo de 1 dia de release

**Novo desenho (3 tiers):**
```
Tier 0: Cache semântico (similaridade > 0.95) → 0ms, 0 custo
Tier 1: Classificador ONNX pequeno (~5ms) → descarta candidatos
Tier 2: LLM completo (Laya, Jev, ou outro) → só quando tiers 0+1 falham
```

**Métrica:** custo/decisão reduzido com delta de acerto < 2%.

**ADR correspondente:** ADR-004 (atualizado).

**Ver também:** [VEREDITO.md](VEREDITO.md) para auditoria completa.

---

## 5. Decision Layer (REAVALIADO — ensemble 3 tiers)

| Opção | Prós | Contras | Status |
|---|---|---|---|
| **Ensemble 3 tiers** (cache → pre-filter → LLM) | Cache 0ms, pre-filter ~5ms, LLM só quando necessário | Complexidade | ✅ **CHOSEN** |
| **Laya** (ONNX local) | Open weights, ~33ms, fine-tunable | Release set/2026, **sem crate Rust oficial**, Python-only, benchmark desaconselha uso head-to-head | **Candidato tier 2** |
| Jev (TypeSafe) | ~$0/call, local, sem Python | Closed weights (System One) | Fallback tier 2 |

---

## **6. Shadow Executor + Golden Set** (NOVO — Semana 5)

| Opção | Prós | Contras | Status |
|---|---|---|---|
| **Shadow executor** (golden set + parallel validation) | Rollback automático, golden set imutável, circuit breaker | Exige VM isolada para produção | ✅ **CHOSEN** |
| Apenas testes unitários | Simples, rápido | Não pega regressões semânticas / integração | Rejeitado |
| Canary deploy | Real traffic | Risco em produção, lento | Rejeitado |

**Rationale:** Auto-melhoria Nível 3 exige validação semântica antes de promoção. Shadow executor roda candidato e sistema atual em paralelo contra golden set imutável. Se TODOS passam → promove; qualquer falha → rollback + registra. Circuit breaker para falhas consecutivas.

---

## 7. Sandbox (was 6)
## 6. Sandbox

| Opção | Prós | Contras | Status |
|---|---|---|---|
| Anthropic srt | OS-level, free | macOS/Linux only | ❌ |
| Docker | Isolamento bom | 50-100ms overhead | ✅ Short-term |
| **Linux namespaces** | Zero overhead, fine-grained | Complexidade de implementação | ✅ **CHOSEN** |
| E2B | MicroVM, gerenciado | Custo | 🔄 Produção |

**Decisão:** Linux namespaces (via `nix` crate) para sandbox-per-agent. Docker como fallback no desenvolvimento.

**Evidência:** GitHub Copilot usa OS-level sandbox (Seattle/bubblewrap). Docker é 50-100ms por container — muito para subagentes.

---

## 8. Multi-Modelo

| Opção | Prós | Contras | Status |
|---|---|---|---|
| Multi-modelo com fallback | Resiliência, custo otimizado | Complexidade de roteamento | ✅ **CHOSEN** |
| Modelo único | Simples | Ponto único de falha | ❌ |

**Decisão:** Sim, com fallback automático (OpenRouter, Anthropic, 9Router local).

---

## 9. Plugin System

| Opção | Prós | Contras | Status |
|---|---|---|---|
| Plugin-first (DeepSeek) | Extensível, comunidade | Overhead de abstração | ✅ Inspiração |
| Built-in (Prime Agent) | Simples, performático | Menos extensível | ❌ |
| MCP nativo | Padrão emergente | Ecossistema ainda maduro | ✅ **CHOSEN** |

**Decisão:** MCP (Model Context Protocol) como padrão de plugins.

**Evidência:** DeepSeek Harness é plugin-first (95k stars em 2 dias). MCP é o padrão emergente (Anthropic, OpenCode, Prime Agent).

---

## 10. Multi-Canal

| Opção | Prós | Contras | Status |
|---|---|---|---|
| Terminal-first (OpenCode) | Foco em coding | Limitado a CLI | ❌ MVP |
| Multi-canal (OpenClaw) | Discord, Slack, WhatsApp | Complexidade extra | 🔄 Roadmap |

**Decisão:** Terminal-first no MVP. Multi-canal no roadmap (v2).

**Evidência:** OpenClaw (#1 GitHub 2026) domina multi-canal. Mas coding agents ainda são terminal-first (Claude Code, OpenCode, Prime Agent).

---

## Decisões Fechadas

| # | Decisão | Escolha |
|---|---|---|
| 1 | Linguagem runtime | **Rust puro** — zero Python, zero FFI, zero LangChain |
| 2 | Kernel persistente | **Tokio** (não asupersync, não próprio) |
| 3 | Estratégia base | **Kernel do zero**, pi_agent_rust via MCP backend |
| 4 | Sandbox | Linux namespaces + cgroups (não Docker) |
| 5 | Decision layer | **Ensemble 3 tiers** (cache → pre-filter ONNX → LLM) |
| 6 | Auto-melhoria N3 | Shadow executor + golden set imutável + rollback |

**Ver também:** `RESEARCH-PATTERNS.md` (papers × pilares) e `PLANO_IMPLEMENTACAO.md` (ADRs)

---

### Detalhes completos por seção
| Sec | Tema | Decisão |
|---|---|---|
| §1 | Runtime | Rust puro (escolhido); Python/TypeScript/Go rejeitados |
| §2 | Kernel | Tokio (escolhido); asupersync rejeitado; conkernel referência |
| §3 | RLM | Recursive unit = harness completo, não só model call |
| §4 | Auto-melhoria | Nível 3 = shadow executor, não `cargo check` sozinho |
| §5 | Decision layer | Ensemble 3 tiers; Laya candidato tier 2 (revertido) |
| §6 | Sandbox | Linux namespaces via `nix`; seccomp; landlock |
| §7 | Multi-modelo | Sim, fallback automático |
| §8 | Plugins | MCP nativo |
| §9 | Multi-canal | Terminal-first (v1); multi-canal no v2 |
