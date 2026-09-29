# Ayrola-Wisdom

> Pesquisa estruturada para o **Ayrola Harness** — um agente Rust-native, open-source, com memória event-sourced, subagentes sub-100ms, auto-melhoria nível 3, e decision layer ensemble.

---

## Decisões Fechadas

| Decisão | Escolha | Por quê |
|---|---|---|
| **Linguagem runtime** | **Rust puro** — zero Python, zero FFI, zero LangChain | Sub-100ms spawn, ecossistema Rust |
| **Decision layer** | **Ensemble 3 tiers** (cache → heuristic → LLM) | Tier0 0ms, Tier1 heuristico, Tier2 9Router local (free) |
| **Kernel** | Tokio async + event store | Sub-100ms spawning, event-sourced memory |
| **Auto-melhoria** | Nível 3 (harness self-modification) | Diferencial competitivo |
| **Sandbox** | Linux namespaces (per-agent) | Zero overhead vs Docker |
| **Multi-modelo** | Sim, com fallback | Resiliência |
| **Open-source** | Sim (Apache 2.0 ou MIT) | Sem lock-in |

---

## Estrutura do Repositório

| Arquivo | O que é |
|---|---|
| **README.md** | Este arquivo — visão + decisões + índice |
| **MANIFESTO.md** | Por que existimos, 4 pilares, filosofia |
| **DECISOES.md** | 9 decisões técnicas (runtime, base, RLM, auto-melhoria, decision, sandbox) |
| **ESTADO_DA_ARTE.md** | Papers, repos, tendências, benchmarks, gap analysis |
| **PROPOSTA.md** | O que construir, features, roadmap, métricas |
| **VEREDITO.md** | **Resposta à auditoria — 3 decisões revertidas, novos eixos** |
| **RESEARCH-PATTERNS.md** | 13 papers mapeados aos 4 pilares |
| **PLANO_IMPLEMENTACAO.md** | 8 ADRs derivados de papers (nenhum testado) |
| **PLANO_MIGRACAO.md** | ⚠️ Histórico — plano do fork, parcialmente revertido |
| **phase0-kernel/** | Phase 1 atual (5642 linhas, real, 165 testes) — **substituído pelo kernel novo** |

---

## Como usar

1. **Entender o projeto:** Leia `MANIFESTO.md`
2. **Decisões técnicas:** Leia `DECISOES.md`
3. **Contexto de mercado:** Leia `ESTADO_DA_ARTE.md`
4. **Começar a construir:** Leia `PROPOSTA.md`
5. **Entender os pivots:** Leia `VEREDITO.md`

---

**Status:** ✅ S1-S21 concluídas — 208 testes, clippy clean, spawn p50 0.010ms

**Kernel repo (novo):** https://github.com/AbsonDev/ayrola-kernel (branch `ayrola-kernel-new`)
**Commit:** `894b5e9` — kernel do zero (~8.8k linhas, 24 arquivos, 208 testes, 99 commits)

**Decisões fechadas:** Rust puro, Tokio 1.53.1, ensemble 3 tiers, Laya apenas Tier 2.
**Concluído:** Kernel + RLM + benchmarks + decision ensemble + MemoryIndex TF-IDF + MCP 10 tools + Agent::decide migrado.

**Documentos-chave:**
- `VEREDITO.md` — **comece por aqui.** Auditoria honesta: 3 decisões revertidas, novos eixos (ensemble, ayrola-bench, certificação), roadmap de 4 semanas
- `RESEARCH-PATTERNS.md` — 13 papers mapeados aos 4 pilares
- `PLANO_IMPLEMENTACAO.md` — 8 ADRs derivados de papers (nenhum testado)
- `ESTADO_DA_ARTE.md` §7 — 55 papers descobertos via `orx`

*Última atualização: 2026-09-29*


## 🔗 Repositórios

| Repo | URL | Status |
|---|---|---|
| **Ayrola-Wisdom** (pesquisa, manifesto, ADRs) | https://github.com/AbsonDev/Ayrola-Wisdom | ✅ Ativo |
| **ayrola-kernel** (fork pi_agent_rust, Rust kernel) | https://github.com/AbsonDev/ayrola-kernel | ✅ Fase 0+ |
