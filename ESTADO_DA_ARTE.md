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
| **pi_agent_rust** | Rust | Agente em Rust, prova de conceito |
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
