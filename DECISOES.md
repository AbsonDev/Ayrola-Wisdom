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

**Decisão:** Rust para o kernel. Python via FFI para harness definition (LangChain/LlamaIndex/MCP).

**Evidência:** GitHub Copilot migrôu 800k+ linhas para Rust (Q2 2026). Benchmark: Rust é 97x mais rápido que Python (CPU), 3-4x em I/O.

---

## 2. Kernel Persistente

| Opção | Prós | Contras | Status |
|---|---|---|---|
| IPython REPL (Prime Agent) | Estado real, Jupyter | Pesado, startup lento | ❌ |
| Shell session (OpenCode) | Leve | Menos poderoso | ❌ |
| **conkernel/clikernel** | Biblioteca, plugável | Dependência externa | ✅ Referência |
| **Tokio (propio)** | Zero custo, integrado | Precisa construir | ✅ **CHOSEN** |

**Decisão:** Tokio runtime com event store embutido — estado entre turnos via eventos append-only, não via REPL.

**Evidência:** conkernel abstraiu o conceito (2026). Tokio é maduro em Rust.

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

## 5. Decision Layer (Laya — CONFIRMADO ✅)

| Opção | Prós | Contras | Status |
|---|---|---|---|
| **Laya (ONNX local)** | $0, 33ms, open weights, fine-tunable, Rust-native | 421M params, precisa GPU p/ peak | ✅ **CHOSEN** |
| Jev (TypeSafe API) | $0 via 9Router, 136ms | Closed weights, dependência externa | ❌ Descartado |
| System One local | Offline, $0 | Setup inicial | ❌ Descartado |
| LLM para tudo | Simples | 100-200x mais caro | ❌ Descartado |

**Decisão:** Laya — modelo de decisão open-source (Apache 2.0), 421M parameters, ~33ms por decisão, roda local via ONNX Runtime.

**Justificativa:**
- **4x mais rápido** que Jev (33ms vs 136ms)
- **Open weights** — você possui o modelo, pode auditar e fine-tunar
- **Rust-native** — integra via `ort` crate (ONNX Runtime bindings), sem HTTP
- **Bate Jev 26-1 no Tetris** — benchmark público
- **Fine-tunable** — RLCD (Reinforcement Learning from Compare-and-Verify Distillation) para decisões Ayrola-specific
- **Zero custo absoluto** — sem dependência de API

**Integração Rust:**
```toml
[dependencies]
ort = "0.47"  # ONNX Runtime bindings
```

**Casos de uso:**
- Decisões de subagent spawning (RLM)
- Validação de patches de auto-melhoria (nível 3)
- Roteamento de requisições
- Gating de comandos perigosos
- Commit approval

---

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

## 7. Multi-Modelo

| Opção | Prós | Contras | Status |
|---|---|---|---|
| Multi-modelo com fallback | Resiliência, custo otimizado | Complexidade de roteamento | ✅ **CHOSEN** |
| Modelo único | Simples | Ponto único de falha | ❌ |

**Decisão:** Sim, com fallback automático (OpenRouter, Anthropic, 9Router local).

---

## 8. Plugin System

| Opção | Prós | Contras | Status |
|---|---|---|---|
| Plugin-first (DeepSeek) | Extensível, comunidade | Overhead de abstração | ✅ Inspiração |
| Built-in (Prime Agent) | Simples, performático | Menos extensível | ❌ |
| MCP nativo | Padrão emergente | Ecossistema ainda maduro | ✅ **CHOSEN** |

**Decisão:** MCP (Model Context Protocol) como padrão de plugins.

**Evidência:** DeepSeek Harness é plugin-first (95k stars em 2 dias). MCP é o padrão emergente (Anthropic, OpenCode, Prime Agent).

---

## 9. Multi-Canal

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
| 1 | Linguagem runtime | Rust (kernel) + Python (definição) |
| 2 | Kernel persistente | Tokio (próprio) |
| 3 | RLM | Nativo (depth-bounded) |
| 4 | Auto-melhoria | Nível 3 |
| 5 | Decision layer | Laya (ONNX local) |
| 6 | Sandbox | Linux namespaces |
| 7 | Multi-modelo | Sim (fallback automático) |
| 8 | Plugins | MCP nativo |
| 9 | Multi-canal | Terminal-first (roadmap: v2) |
