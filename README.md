# Ayrola-Wisdom

> Pesquisa estruturada para o **Ayrola Harness** — um agente Rust-native, open-source, com memória event-sourced, subagentes sub-100ms, auto-melhoria nível 3, e decision layer Laya.

---

## Decisões Fechadas

| Decisão | Escolha | Por quê |
|---|---|---|
| **Linguagem runtime** | Rust (kernel) + Python (definição) | Performance + ecossistema AI |
| **Decision layer** | Laya (ONNX local) | 4x mais rápido que Jev, open-source, fine-tunable, Rust-native |
| **Kernel** | Tokio async + event store | Sub-100ms spawning, memória perfeita |
| **Auto-melhoria** | Nível 3 (harness self-modification) | Diferencial competitivo |
| **Sandbox** | Linux namespaces (per-agent) | Zero overhead vs Docker |
| **Multi-modelo** | Sim, com fallback | Resiliência |
| **Open-source** | Sim (Apache 2.0 ou MIT) | Sem lock-in |

---

## Estrutura do Repositório

| Arquivo | O que é |
|---|---|
| **README.md** | Este arquivo — visão + decisões + índice |
| **MANIFESTO.md** | Por que existimos, 4 pilares, filosofia, arquitetura |
| **DECISOES.md** | Todas as decisões técnicas detalhadas (linguagem, Laya, runtime, etc.) |
| **ESTADO_DA_ARTE.md** | Papers, repos, tendências, benchmarks, gap analysis |
| **PROPOSTA.md** | O que construir, features, roadmap, métricas |

---

## Como usar

1. **Entender o projeto:** Leia `MANIFESTO.md`
2. **Decisões técnicas:** Leia `DECISOES.md`
3. **Contexto de mercado:** Leia `ESTADO_DA_ARTE.md`
4. **Começar a construir:** Leia `PROPOSTA.md`

---

**Status:** 🚀 Fase 0 EM ANDAMENTO — kernel Rust compilando

**Repositório do kernel:** `~/ayrola-kernel`
**Commit inicial:** `e87cd93` — Phase 0: Rust + Laya + Tokio (9/9 tests)

*Última atualização: 2026-09-27*
