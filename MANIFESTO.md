# MANIFESTO — Ayrola Harness

> **Não estamos construindo mais um agent harness.**
> Estamos construindo o primeiro agente que verdadeiramente merece o nome de **"inteligência artificial recursiva auto-aprimorável com memória perfeita"**.

---

## 1. O Problema

Os agentes de hoje (Prime Agent, OpenCode, Claude Code, DeepSeek Harness) são **ferramentas**, não **seres digitais**.

Eles executam comandos, chamam LLMs, escrevem código. Mas quando a sessão termina, **tudo morre**. Memória some. Contexto some. O agente esquece o que aprendeu.

O melhor de hoje (`pi_agent_rust`) tem 16k+ arquivos, tools, providers, browser, LSP — mas ainda é memória volátil. Evento não é imutável. Decisão não é replayável.

Ayrola muda o jogo: memória event-sourced, time-travel, sub-100ms spawn, sandbox-per-agent, decision layer ensemble — tudo em Rust puro.

---

## 2. A Revolução

Um agente que:

1. **Nunca esquece** — memória event-sourced com replay completo
2. **Pensa em paralelo** — subagentes spawnados em 10-50ms (não 2s)
3. **Se modifica** — reescreve o próprio código quando aprende algo novo
4. **Viaja no tempo** — fork timelines, replay de sessões, debugging de decisões passadas
5. **Vive em segurança** — cada subagente em seu próprio sandbox, zero overhead

**Ayrola** vem do protoindo-europeu \*h₂er- (arar, cultivar). Um agente que cultiva conhecimento ao longo do tempo.

Não é um assistente. É um **jardim de inteligência**.

---

## 3. Os 4 Pilares

### Pilar 1: Memória Perfeita (Event-Sourced + Time-Travel)

Toda ação do agente é um evento imutável (append-only log):
- Tool calls, decisões, resultados, erros, pensamentos

Permite: replay completo, fork de timelines, debug de decisões, hot-reload de memória.

**Implementação:** Event store em Rust, memmap para performance, snapshots periódicos.

### Pilar 2: Subagentes como Cidadãos de Primeira Classe

Subagentes spawnados em **10-50ms** (vs 500ms-2s em Python).

```
Tarefa complexa
  → Decompõe em 10 subtarefas
  → Spawn paralelo (10ms cada = 100ms total)
  → Depth-bounded recursion (default: 3)
  → Agregação de resultados
```

**Impacto:** RLM de verdade. Agente pensa em paralelo, não sequencialmente.

**Implementação:** Tokio async runtime (`rt-multi-thread`), Tokio tasks (não processos), cancellation tokens.

**Referência:** `pi_agent_rust` prova o padrão de tools/providers/subagents em Rust. Ayrola usa Tokio (padrão da indústria) + os patterns de agente do `pi`, sem herdar seu runtime custom (`asupersync`).

### Pilar 3: Auto-Melhoria Nível 3 (Harness Self-Modification)

O agente **reescreve o próprio código do harness**.

```
Detecta padrão de erro recorrente
  → Propõe patch no código Rust
  → Laya valida segurança
  → Cargo check valida type safety
  → Hot-reload aplica o patch (libloading)
  → Agente continua com o novo código
```

**Implementação:** Self-Harness paper (Zhang, 2026) + ensemble decision layer (validação de comportamento, não apenas tipo) + golden set imutável + rollback automático.

### Pilar 4: Sandbox-Per-Agent (Zero Overhead Isolation)

Cada subagente em seu próprio **namespace Linux leve**, criado em microssegundos.

- 10 agentes = 10 namespaces, sem overhead de containers
- Se um crasha, os outros continuam
- Isolamento real (não só "confie no agente")

**Implementação:** Linux namespaces via `nix` crate, cgroups, seccomp, landlock.

---

## 4. Filosofia

1. **Memória é sagrada** — nenhum evento é perdido, nenhuma decisão é esquecida.
2. **Velocidade é feature** — sub-100ms spawning não é otimização, é requisito.
3. **Auto-melhoria é obrigatória** — um agente que não melhora a si mesmo está morto.
4. **Segurança por design** — sandbox-per-agent não é opcional.
5. **Rust não é negociável** — as propriedades que queremos só são possíveis em Rust.

---

## 5. Arquitetura (Visão Geral)

```
┌──────────────────────────────────────────────────────────────┐
│                    AYROLA HARNESS                             │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  RUST KERNEL                                           │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │   │
│  │  │  Tokio      │  │  RLM Engine │  │  Event      │  │   │
│  │  │  Runtime    │  │  (recursive)│  │  Store      │  │   │
│  │  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  │   │
│  │         │                │                │          │   │
│  │  ┌──────▼────────────────▼────────────────▼──────┐   │   │
│  │  │  Laya Decision Layer (embedded, $0, 33ms)      │   │   │
│  │  └────────────────────────────────────────────────┘   │   │
│  │                                                        │   │
│  │  Subagentes (10-50ms spawn, sandbox-per-agent)        │   │
│  │  Self-Improvement Engine (nível 3)                     │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ▲                              ▲                            │
│  │                              │                            │
│  Toolchain (MCP, LSP,           Dashboard                     │
│  providers, tools)               (time-travel debugger)        │
└──────────────────────────────────────────────────────────────┘
```

---

## 6. Diferenciais Competitivos

| Diferencial | Estado da Arte (2026) | Ayrola |
|---|---|---|
| Sub-100ms spawning | ~100ms (pi_agent_rust/asupersync) | **10-50ms (Rust/Tokio)** |
| Event-sourced memory | Best-effort snippets | **Append-only, time-travel** |
| Self-modification (nível 3) | Pesquisa | **Produção com validação** |
| Sandbox-per-agent | Docker (50-100ms) | **Namespaces (1-10ms)** |
| Decision layer | Jev (136ms, closed) | **Laya (33ms, open, fine-tunable)** |

---

*Versão 1.0 — 2026-09-27*
