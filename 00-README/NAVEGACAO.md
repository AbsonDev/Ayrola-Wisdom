# 🗺️ Navegação por Tema

Guia de navegação rápida. Cada link aponta para o arquivo de detalhe.

---

## 1. Papers Fundamentais

| Paper | Link | Status |
|---|---|---|
| **Recursive Language Models** (Alex Zhang, arXiv Dec 2025) | 01-papers/01-rlm-alex-zhang.md | ✅ Resumido |
| **Recursive Agent Harnesses (RAH)** (Lumer, arXiv Jun 2026) | 01-papers/02-rah-lumer.md | ✅ Resumido |
| **Continual Harness** (Seth Karten, arXiv May 2026) | 01-papers/03-continual-harness.md | ✅ Resumido |
| **Self-Harness** (Zhang, arXiv Jun 2026) | 01-papers/04-self-harness.md | ✅ Resumido |
| **Harness Engineering** (Lilian Weng, Jul 2026) | 01-papers/05-harness-engineering-weng.md | ✅ Resumido |

---

## 2. Repositórios Mapeados

| Categoria | Repos | Link |
|---|---|---|
| **Pioneiros (produção)** | Prime Agent, OpenClaw, DeepSeek Harness | 02-repositories/01-pioneiros.md |
| **Runtime (Rust/Go/TS)** | pi_agent_rust, conkernel, Mastra | 02-repositories/02-runtimes.md |
| **Orquestração/CI-CD** | Aeon, Claude Code, Codex | 02-repositories/03-orquestracao.md |
| **Auto-melhoria** | Self-Harness, Awesome-Harness-Self-Improvement | 02-repositories/04-auto-melhoria.md |

---

## 3. Tendências Emergentes

| Tendência | Link |
|---|---|
| RLM como paradigma padrão | 03-trends/01-tendencias-2026.md#rlm |
| Harness como camada de inteligência | 03-trends/01-tendencias-2026.md#harness-inteligente |
| Kernel REPL persistente | 03-trends/01-tendencias-2026.md#kernel-repl |
| Decisão barata (Jev/System One) | 03-trends/01-tendencias-2026.md#decisao-barata |
| Sandbox real como default | 03-trends/01-tendencias-2026.md#sandbox |
| Runtime diversificação | 03-trends/01-tendencias-2026.md#runtime |
| Auto-melhoria estrutural | 03-trends/01-tendencias-2026.md#auto-melhoria |
| Multi-agent como default | 03-trends/01-tendencias-2026.md#multiagent |
| **Escolha da linguagem runtime** | **03-trends/03-runtime-language-decision.md** |

---

## 4. Comparativo de Features

| Feature | Prime Agent | OpenCode | DeepSeek Harness | Claude Code | Cursor |
|---|---|---|---|---|---|
| RLM (recursão nativa) | ✅ | ✅ | ❓ | ❌ | ❌ |
| Kernel REPL persistente | ✅ | ✅ | ❌ | ❌ | ❌ |
| Auto-refinamento harness | ✅ | ✅ | ❌ | ❌ | ❌ |
| Subagentes recursivos | ✅ | ✅ | ❌ | ✅ (limitado) | ❌ |
| Multi-modelo (fallback) | ✅ | ✅ | ❌ | ✅ | ✅ |
| Sandbox real | ❌ (Docker) | ✅ | ❌ | ✅ | ✅ |
| Zero custo para decisões | ✅ (Jev) | ✅ (Zen lane) | ❌ | ❌ | ❌ |
| Plugin-first | ❌ | ❌ | ✅ | ❌ | ❌ |

📄 `04-comparative/FEATURE_COMPARISON.md`

---

## 5. Para Definir a Proposta do Ayrola Harness

Comece aqui para decidir a arquitetura:

1. **03-trends/01-tendencias-2026.md** — 8 tendências estruturais
2. **03-trends/02-decisoes-tecnicas.md** — tabela de decisões (Python/Rust/TS, sandbox, RLM, etc.)
3. **04-comparative/FUNCIONALIDADES.md** — gap analysis
4. **06-drafts/00-template-proposta.md** — template para definir sua proposta
5. **03-trends/03-runtime-language-decision.md** — decisao de linguagem com benchmarks
