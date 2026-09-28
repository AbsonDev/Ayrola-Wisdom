# Ayrola-Wisdom

> Pesquisa estruturada para o **Ayrola Harness** — um agente Rust-native, open-source, com memória event-sourced, subagentes sub-100ms, auto-melhoria nível 3, e decision layer Laya.

---

## Decisões Fechadas

| Decisão | Escolha | Por quê |
|---|---|---|
| **Linguagem runtime** | **Rust puro** — zero Python, zero FFI, zero LangChain | Sub-100ms spawn, ecossistema Rust |
| **Decision layer** | TBD (stub only) | Laya precisa reavaliação — release muito recente (set/2026), nenhum crate Rust oficial |
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
5. **Entender os pivots:** Leia `VEREDITO.md`

---

**Status:** 🚀 Phase 0 concluída — stub kernel (6/6 tests)

**Kernel repo:** https://github.com/AbsonDev/ayrola-kernel
**Commit:** `077cccb` — fork pi_agent_rust (406 .rs files, 663k lines)

**Decisões fechadas:** Rust puro (zero Python/FFI/LangChain), Tokio (não asupersync).
**Pendente:** Laya ONNX (stub apenas), runtime migration (asupersync→Tokio).

**Novos documentos (2026-09-28):**
- `RESEARCH-PATTERNS.md` — 13 papers mapeados aos 4 pilares com detalhes
- `PLANO_IMPLEMENTACAO.md` — Mapa completo: papers → decisões de código → módulos Rust → ADRs → roadmap revisado
- `ESTADO_DA_ARTE.md` (atualizado) — Seção 7: Auto-Research Setup com 55 papers descobertos via `orx`

*Última atualização: 2026-09-27*


## 🔗 Repositórios

| Repo | URL | Status |
|---|---|---|
| **Ayrola-Wisdom** (pesquisa, manifesto, ADRs) | https://github.com/AbsonDev/Ayrola-Wisdom | ✅ Ativo |
| **ayrola-kernel** (fork pi_agent_rust, Rust kernel) | https://github.com/AbsonDev/ayrola-kernel | ✅ Fase 0+ |
