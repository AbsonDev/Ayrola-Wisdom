# ESTADO_DA_ARTE — Papers, Repositórios, Tendências e Benchmarks

Consolidação de toda a pesquisa: papers, repos em produção, tendências 2025-2027, e gap analysis.

---

## 1. Papers Fundamentais

### 1.1 Recursive Language Models (RLM) — Alex Zhang (Dez 2025)
**arXiv:** 2512.24601 | **Repo:** github.com/alexzhang13/rlm

LLM invoca a si mesmo recursivamente para decompor tarefas complexas. Subprocessos recursivos melhoram performance de 71.75% → 81.36% sobre baseline.

**Relevância:** Base teórica para o RLM Engine do Ayrola.

### 1.2 Recursive Agent Harnesses (RAH) — Lumer (2026)
**arXiv:** 2606.13643

+9.6 pontos percentuais sobre Codex puro. Valida que subagentes recursivos são o paradigma de 2026.

### 1.3 Continual Harness — Seth Karten (2026)
**arXiv:** 2605.09998

Adaptação online sem esquecimento catastrófico. Nível 4 de auto-melhoria.

### 1.4 Self-Harness — Zhang (2026)
**arXiv:** 2606.09498 | **Repo:** github.com/qzzqzzb/Self-Harness

Agente reescreve o próprio código do harness. Nível 3 de auto-melhoria. 45 citações.

### 1.5 Harness Engineering — Lilian Weng (OpenAI, 2026)
**Blog:** lilianweng.github.io/posts/2026-07-04-harness/

"Harness engineering, não model engineering." O harness é tão importante quanto o modelo.

---

## 2. Repositórios em Produção

### 2.1 Pioneiros

| Harness | Stars | Linguagem | Destaque |
|---|---|---|---|
| **Prime Agent** | — | Python | Melhor auto-refinamento (`/refine`), subagentes recursivos, kernel IPython |
| **OpenClaw** | ~390k | TypeScript | #1 GitHub 2026, multi-canal (Discord/Slack/WhatsApp) |
| **DeepSeek Harness** | ~95k | Python | Plugin-first, 95k stars em 2 dias |
| **OpenCode** | — | TypeScript | MIT, simples, hot reload de skills, Zen lane zero-cost |
| **Claude Code** | — | TypeScript | Sandbox OS-level (Seatbelt/bubblewrap) |
| **Cursor** | — | TypeScript | IDE-first, sem kernel persistente |

### 2.2 Runtimes Emergentes

| Runtime | Linguagem | Destaque |
|---|---|---|
| **pi_agent_rust** | Rust | **Agente Rust mais maduro existente** (469 .rs arquivos, 871KB agent.rs, tools/providers/LSP/browser/MCP/subagents/extensions/swarm/compaction/session-store). Runtime: `asupersync` (custom, single-maintainer) — NÃO Tokio. |
| **conkernel / clikernel** | Rust | Kernel persistente como biblioteca plugável (AnswerDotAI) |
| **Mastra** | TypeScript | Framework de agentes TS-first |
| **GitHub Copilot** | Rust | Migrou 800k+ linhas para Rust (Q2 2026) |

### 2.3 Orquestração & CI/CD

- **Aeon + GitHub Actions:** Agentes sem humanos no loop
- **Claude Code subagents:** Spawning nativo de subagentes
- **Copilot runtime:** Orquestração multi-backend

### 2.4 Auto-Melhoria

- **Self-Harness:** Agente modifica o próprio harness (nível 3)
- **leezythu/Awesome-Harness-Self-Improvement:** Curadoria de técnicas
- **Prime Agent `/refine`:** Nível 2 (skill/harness refinement)
- **RHI (Reinforcement from Human Feedback):** Nível 1-2

---

## 3. Tendências 2025-2027

### 3.1 O Harness como Camada de Inteligência
Até 2025: harness = tooling ao redor do modelo. A partir de 2026: **harness é tão importante quanto o modelo**.

**Evidências:** Self-Harness paper, Lilian Weng (OpenAI), HN "The harness is all you need (mostly)".

### 3.2 RLM como Paradigma Padrão
RLM saiu de paper (Dez 2025) para produção (2026) em <9 meses. LLM decide *quando* spawnar subagentes.

**Prova:** RAH paper: +9.6pp sobre Codex puro.

### 3.3 Kernel REPL Persistente
Saiu de novidade (Prime Agent) para biblioteca independente (conkernel/clikernel). Estado entre turnos é padrão.

### 3.4 Camada de Decisão Barata (Laya)
Decisões simples custam 100-200x mais em LLM do que deveriam. **Laya** resolve: 33ms, $0, open-source, fine-tunable.

### 3.5 Sandbox Real como Default
Todo coding agent de 2026 roda em sandbox. Sandbox-as-a-Service é commodity. Diferencial volta para o harness.

### 3.6 Runtime Diversificação
Python domina hoje. Em 2027, agentes de produção rodam em Rust/Go/TS. Python fica na camada de *definição de harness*.

### 3.7 Auto-Melhoria Estrutural
Nível 2 é produção. Nível 3 é pesquisa (paper validado). Nível 4 (continual learning) é horizonte.

### 3.8 Multi-Agent como Default
Tarefas simples = agente único. Tarefas complexas = múltiplos agentes. Spawning de subagentes é primitiva nativa.

---

## 4. Laya vs Jev — Decision Layer

### TL;DR
**Laya é o Jev open-source que roda local, custa $0, e bate o Jev de graça.**

| Característica | **Laya** | **Jev** |
|---|---|---|
| Preço | $0 (Apache 2.0) | $0 via 9Router, pago via TypeSafe |
| Weights | Open (você possui) | Closed (hosted API) |
| Runtime | ONNX Runtime (sem Python) | Python / API |
| Latência | **~33ms** (16ms multilingual) | ~136ms |
| Benchmark | **Bate Jev 26-1 no Tetris** | Perdeu |
| Fine-tuning | Sim (RLCD) | Não possível |
| Integração Rust | `ort` crate (ONNX) — nativo | Requer HTTP client |

### Arquitetura Laya
```
Input (state + questions)
  → ModernBERT-large encoder (312M params)
  → 2-layer decision head
  → Typed output (choice/score/yes-no)
```

### Integração Rust
```toml
[dependencies]
ort = "0.47"  # ONNX Runtime bindings
```

### Fine-Tuning para Ayrola
1. **Dataset:** Trajetórias do Prime Agent (Qdrant)
2. **Labels:** Laya verdicts como ground truth
3. **Método:** RLCD (Reinforcement Learning from Compare-and-Verify Distillation)
4. **Validação:** Benchmark Oolong-Synthetic

### Benchmarks
| Benchmark | Laya | Jev |
|---|---|---|
| Tetris (lines) | 26 | 1 |
| Hard-label accuracy | 76.6% | ~70% |
| Routed stack | Bate | Perde |

### Referências
- Laya: github.com/NandhaKishorM/laya
- Laya ONNX: github.com/receptron/laya
- Laya HF: huggingface.co/convaiinnovations/laya
- Debate: reddit.com/r/LocalLLaMA/comments/1wlfmgq/
- Benchmark: alphamatch.ai/blog/jev-vs-laya-system-one-2026

---

## 5. Gap Analysis — Oportunidades para Ayrola

### Gap 1: Memória Event-Sourced
**Problema:** Nenhum harness em produção tem memória event-sourced com time-travel.
**Oportunidade:** Ayrola pode ser o primeiro. Diferencial enorme para debugging e auditoria.

### Gap 2: Sub-100ms Spawning
**Problema:** Python-based harnesses levam 500ms-2s por spawn.
**Oportunidade:** Rust/Tokio permite 10-50ms. 10-20x mais rápido.

### Gap 3: Auto-Melhoria Nível 3 em Produção
**Problema:** Self-Harness é paper, não produção.
**Oportunidade:** Ayrola pode ser o primeiro harness em produção com nível 3.

### Gap 4: Sandbox-Per-Agent Zero Overhead
**Problema:** Docker tem 50-100ms overhead por container.
**Oportunidade:** Linux namespaces: 1-10ms. 10x mais rápido.

### Gap 5: Decision Layer Open-Source
**Problema:** Jev é closed-weights, dependência de API.
**Oportunidade:** Laya é open-source, fine-tunable, Rust-native.

### Gap 6: Laya-style Decisions sem Dependência Externa
**Problema:** Jev depende de TypeSafe/9Router.
**Oportunidade:** Laya roda 100% local, $0, sem API.

### Gap 7: Runtime Padrão (Tokio) vs Runtime Custom (asupersync)
**Problema:** `pi_agent_rust` prova que Rust viabiliza agentes completos, mas usa `asupersync` (runtime custom, single-maintainer). Isso cria risco de bus factor e isolamento do ecossistema.
**Oportunidade:** Ayrola usa Tokio (padrão da indústria) + patterns do `pi_agent_rust` reimplementados. Melhor dos dois mundos: base sólida + ecossistema maduro.

---

## 6. Feature Comparison — Estado da Arte 2026

| Feature | Prime Agent | OpenCode | Claude Code | DeepSeek | **Ayrola** |
|---|---|---|---|---|---|
| Kernel persistente | ✅ IPython | ✅ Shell | ❌ | ❌ | ✅ Tokio |
| Subagentes nativos | ✅ | ✅ | ✅ | ✅ | ✅ (10-50ms) |
| RLM (recursive) | ✅ | ✅ Zen lane | ❌ | ❌ | ✅ (depth-bounded) |
| Auto-melhoria | Nível 2 | Nível 1 | Nível 1 | Nível 2 | **Nível 3** |
| Memória event-sourced | ❌ | ❌ | ❌ | ❌ | ✅ |
| Time-travel | ❌ | ❌ | ❌ | ❌ | ✅ |
| Sandbox-per-agent | ❌ | ❌ | ✅ OS-level | ❌ | ✅ namespaces |
| Decision layer | ✅ Laya | ✅ Zen lane | ❌ | ❌ | ✅ Laya |
| Multi-modelo | ✅ | ✅ | ✅ | ✅ | ✅ |
| Open-source | ❌ | ✅ MIT | ❌ | ❌ | ✅ Apache 2.0 |

---

## 7. Bibliografia Curada

| Recurso | Tipo | Link |
|---|---|---|
| RLM (Alex Zhang) | Paper | arxiv.org/abs/2512.24601 |
| RAH (Lumer) | Paper | arxiv.org/abs/2606.13643 |
| Continual Harness (Karten) | Paper | arxiv.org/abs/2605.09998 |
| Self-Harness (Zhang) | Paper | arxiv.org/abs/2606.09498 |
| Harness Engineering (Weng) | Blog | lilianweng.github.io/posts/2026-07-04-harness/ |
| Laya | Model | github.com/NandhaKishorM/laya |
| Laya ONNX | Runtime | github.com/receptron/laya |
| conkernel | Lib | github.com/AnswerDotAI/conkernel |
| OpenClaw | Harness | github.com/anthropics/openclaw |
| DeepSeek Harness | Harness | github.com/deepseek-ai/harness |
| OpenCode | Harness | github.com/opencode-ai/opencode |
| Jev / TypeSafe | Decision | typesafe.ai/jev |
| Laya vs Jev debate | Discussion | reddit.com/r/LocalLLaMA/comments/1wlfmgq/ |
| Laya vs Jev benchmark | Blog | alphamatch.ai/blog/jev-vs-laya-system-one-2026 |

---

*Consolidado em 2026-09-27 a partir de 22 arquivos originais.*
---

## 7. Auto-Research Setup (2026-09-28)

### Ferramentas instaladas
- **orx (OpenResearch CLI) v0.2.11** — `~/.local/bin/orx` — pesquisa automática, discover embedding/keyword, paper fetch, dashboard local
- **alphaxiv-py v0.7.0** — `~/Library/Python/3.14/bin/alphaxiv` — SDK Python para alphaXiv API
- **alphaXiv MCP server** — `https://api.alphaxiv.org/mcp/v1` — 19 ferramentas (discover, paper content, GitHub code, researchers, library)

### Papers recuperados via orx discover embedding (6 queries × 10 = 60 resultados, 55 únicos)

| arXiv | Título | Votos | Categoria |
|---|---|---|---|
| 2608.23552 | Prime Agent: A Self-Improving RLM Harness | 357 | recursive agent harness |
| 2609.11873 | The Last AI Built by Humans: Toward Genuine RSI | 305 | self-improving agent |
| 2609.14858 | Dream-RSI: Recursive Self-Improvement through Evolving Worlds | 244 | self-improving agent |
| 2609.24972 | RRSI: Regularized Recursive Self-Improvement of Agent Harnesses | 218 | recursive + self-improving |
| 2608.spec-ptc | Speculative Programmatic Tool Calling | 190 | recursive + subagent spawning |
| 2609.20519 | SoL-Pi: Recursively Scaling Auto-Research Loops | 181 | recursive + self-improving |
| 2609.15364 | RSIAgent: Autonomous Exploration for RSI | 130 | recursive + self-improving |
| 2608.24876 | Recursive Experiential-Working Memory Evolution | 85 | recursive agent harness |
| 2605.06639 | Recursive Agent Optimization | 81 | subagent spawning parallel |
| 2609.08183 | NeoHorse-1: Towards RSI via Agentic Post-training | 71 | recursive + self-improving |
| 2609.26457 | Recursive self-improvement of AI research agents | 59 | self-improving agent |
| 2609.06396 | MetaRSI / RSI2: A Meta-Recursive Self-Improving System | 46 | self-improving agent |
| 2609.26781 | Agensh: Scaling Organizational Intelligence to 1,024 Agents | 46 | subagent spawning parallel |
| 2608.21690 | Context as an Environment: Programmatic Context Management | 26 | event sourcing agent memory |
| 2607.27773 | ChronoMem: Version Control and Semantic Rollback for LLM Memory | 11 | event sourcing agent memory |
| 2609.00595 | SoK: When Safe Agents Fail Together: Security of Multi-Agent Systems | 10 | agent sandbox isolation |
| 2607.12406 | Isolation as a First-Class Principle for LLM-Agent System Safety | 8 | agent sandbox isolation |
| 2609.27279 | EnSIMem: Entity-Structured Indexing for Long-Term Agent Memory | 7 | event sourcing agent memory |
| 2608.06745 | MemPrism: Task-Conditioned Relational Memory Views | 6 | event sourcing agent memory |
| 2605.24486 | AgentFugue: Agent Scaling for Long-Horizon Tasks | 6 | subagent spawning parallel |

### Cross-cutting papers (2+ categorias) — highest signal for Ayrola
1. **RRSI (2609.24972)** — recursive harness + self-improving → directly relevant to Pilar 4
2. **Speculative Programmatic Tool Calling (2608.spec-ptc)** — recursive + subagent spawning → Pilar 2
3. **SoL-Pi (2609.20519)** — recursive + self-improving → Pilar 4
4. **RSIAgent (2609.15364)** — recursive + self-improving → Pilar 4
5. **NeoHorse-1 (2609.08183)** — recursive + self-improving → Pilar 4

### Diretório de papers
`/Users/absondutragalvao/ayrola-research-papers/`

### Comandos úteis
```bash
# Pesquisa semântica
orx discover embedding "query" --limit 20 --prioritize recency

# Busca por keyword
orx discover keyword "query" --limit 20

# Baixar paper completo
orx paper <arxiv-id> --full

# Baixar PDF
alphaxiv paper pdf download <arxiv-id> ./paper.pdf

# Extrair texto
alphaxiv paper text <arxiv-id>

# Papers similares
alphaxiv paper similar <arxiv-id>

# Dashboard local de autoresearch
orx up
```
