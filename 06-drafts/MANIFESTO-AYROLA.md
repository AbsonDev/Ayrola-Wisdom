# Ayrola Harness — Manifesto de Inovação

> **Não estamos construindo mais um agent harness.**
> Estamos construindo o primeiro agente que verdadeiramente merece o nome de **"inteligência artificial recursiva auto-aprimorável com memória perfeita"**.

---

## 1. Visão de Longo Prazo

### O Problema que Ninguém Está Resolvendo

Os agentes de hoje (Prime Agent, OpenCode, Claude Code, DeepSeek Harness) são **ferramentas**, não **seres digitais**.

Eles executam comandos, chamam LLMs, escrevem código. Mas quando você fecha a sessão, **tudo morre**. A memória some. O contexto some. O agente esquece o que aprendeu.

É como ter um funcionário genial que sofre de amnesia todos os dias.

### A Revolução: O Que Vamos Construir

Um agente que:

1. **Nunca esquece** — memória event-sourced com replay completo
2. **Pensa em paralelo** — subagentes spawnados em 10-50ms (não 2s)
3. **Se modifica** — reescreve o próprio código do harness quando aprende algo novo
4. **Viaja no tempo** — fork timelines, replay de sessões, debugging de decisões passadas
5. **Vive em segurança** — cada subagente em seu próprio sandbox, zero overhead

### Por Que "Ayrola"?

**Ayrola** vem do protoindo-europeu \*h₂er- (arar, cultivar). Um agente que cultiva conhecimento ao longo do tempo, que cresce com cada interação, que **aprende a aprender**.

Não é um assistente. É um **jardim de inteligência**.

---

## 2. Os 4 Pilares da Inovação

Cada pilar é uma ruptura com o estado da arte atual.

---

### Pilar 1: Memória Perfeita (Event-Sourced + Time-Travel)

**Hoje:** O agente esquece tudo quando a sessão termina. Memória é "best-effort" — snippets de contexto, embeddings, vector DB. Perde-se informação constantemente.

**Nós faremos:** Event sourcing completo.

```
Toda ação do agente é um evento imutável:
  - Tool calls
  - Decisões tomadas
  - Resultados recebidos
  - Erros ocorridos
  - Pensamentos do modelo
```

Esses eventos são append-only, ordenados, e permitem:

| Recurso | Como funciona |
|---|---|
| **Replay completo** | "Mostra exatamente o que o agente fez nas últimas 3 horas" |
| **Fork de timelines** | "E se o agente tivesse tomado outra decisão no passo 5?" |
| **Debug de decisões** | "Por que o agente escolheu X ao invés de Y?" — resposta: o evento exato |
| **Hot-reload de memória** | Adicione novos eventos sem reiniciar o kernel |
| **Compressão inteligente** | Eventos antigos são compactados, mas nunca perdidos |

**Implementação:** Event store em Rust, com:
- Append-only log (memmap para performance)
- Índice invertido para busca rápida
- Snapshots periódicos para evitar replay do zero
- Compressão delta (apenas diferenças entre snapshots)

---

### Pilar 2: Subagentes como Cidadãos de Primeira Classe (Sub-100ms Spawning)

**Hoje:** Spawn de subagente custa 500ms-2s (Python interpreter + imports + GC). RLM é "teoricamente possível, mas na prática caro".

**Nós faremos:** Subagentes spawnados em **10-50ms**.

```
Tarefa complexa
    ↓
Agente principal decompõe em 10 subtarefas
    ↓
Spawn paralelo de 10 subagentes (10ms cada = 100ms total)
    ↓
Cada subagente pode spawnar mais subagentes (depth-bounded)
    ↓
Agregação de resultados
    ↓
Tarefa completa em ~500ms (vs 5-20s em Python)
```

**Impacto:**
- RLM de verdade, não só teoria
- Agente pensa em paralelo, não sequencialmente
- Depth-bounded recursion (configurável, default: 3)
- Orquestração automática (modelo decide quem spawnar)

**Implementação:** Tokio async runtime em Rust, com:
- Spawn de tarefas lightweight (Tokio tasks, não processos)
- Depth tracking nativo
- Cancellation tokens para abortar subagentes desnecessários
- Load balancing automático (quem está livre?)

---

### Pilar 3: Auto-Melhoria Nível 3 (Harness Self-Modification)

**Hoje:** O agente pode melhorar skills e memórias (nível 2 — Prime Agent `/refine`). Mas não pode modificar o próprio código.

**Nós faremos:** O agente **reescreve o próprio código do harness**.

```
Agente detecta padrão recorrente de erro
    ↓
Agente propõe patch no código Rust do harness
    ↓
Laya valida: "Esse patch não quebra nada?"
    ↓
Cargo check valida type safety
    ↓
Hot-reload aplica o patch (libloading / dynamic reload)
    ↓
Agente continua rodando com o novo código
```

**Níveis de auto-melhoria:**

| Nível | O que é | Status |
|---|---|---|
| 1 | Prompt optimization | Produção (DSPy, etc) |
| 2 | Skill/harness refinement | Produção (Prime Agent `/refine`) |
| **3** | **Harness self-modification** | **PESQUISA (nosso diferencial)** |
| 4 | Continual learning (sem catástrofe) | Pesquisa |

**Implementação:**
- Self-Harness paper (Zhang, 2026) como base
- Hot-reload via `libloading` crate (carrega novo código sem restart)
- Validação dupla: Laya (lógica) + Cargo check (type safety)
- Rollback automático se patch causar regressão

---

### Pilar 4: Sandbox-Per-Agent (Zero Overhead Isolation)

**Hoje:** Um sandbox para todos os agentes (Docker, E2B, Anthropic srt). Isolamento bom, mas overhead alto.

**Nós faremos:** Cada subagente em seu próprio **namespace Linux leve**, criado em microssegundos.

```
Agente principal
    ├── Subagente 1 → namespace A (cgroup + seccomp)
    ├── Subagente 2 → namespace B (cgroup + seccomp)
    ├── Subagente 3 → namespace C (cgroup + seccomp)
    └── Subagente 4 → namespace D (cgroup + seccomp)
```

**Propriedades:**
- 10 agentes = 10 namespaces, sem overhead de containers
- Se um agente crashar, os outros continuam
- Isolamento real de segurança (não só "confie no agente")
- Zero overhead em Rust (vs 50-100ms por Docker container)

**Implementação:**
- Linux namespaces (via `nix` crate)
- Cgroups para resource limits
- Seccomp para syscall filtering
- `landlock` para filesystem isolation

---

## 3. Arquitetura Técnica (Visão Geral)

```
┌──────────────────────────────────────────────────────────────┐
│                    AYROLA HARNESS                             │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  RUST KERNEL (o coração)                              │   │
│  │                                                        │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │   │
│  │  │  Tokio      │  │  RLM Engine │  │  Event      │  │   │
│  │  │  Runtime    │  │  (recursive)│  │  Store      │  │   │
│  │  │  (async)    │  │  depth: 3   │  │  (append)   │  │   │
│  │  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  │   │
│  │         │                │                │          │   │
│  │  ┌──────▼────────────────▼────────────────▼──────┐   │   │
│  │  │  Laya Decision Layer (embedded, $0, 33ms ONNX) │   │   │
│  │  └────────────────────────────────────────────────┘   │   │
│  │                                                        │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │   │
│  │  │ Subagent 1  │  │ Subagent 2  │  │ Subagent 3  │  │   │
│  │  │ (10ms)      │  │ (10ms)      │  │ (10ms)      │  │   │
│  │  │ sandbox A   │  │ sandbox B   │  │ sandbox C   │  │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  │   │
│  │                                                        │   │
│  │  ┌─────────────────────────────────────────────────┐ │   │
│  │  │  Self-Improvement Engine (level 3)               │ │   │
│  │  │  — analisa trajetórias                           │ │   │
│  │  │  — propõe patches no código Rust                 │ │   │
│  │  │  — aplica via hot-reload (libloading)            │ │   │
│  │  └─────────────────────────────────────────────────┘ │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ▲                              ▲                            │
│  │                              │                            │
│  ┌─────────────┐          ┌─────────────┐                   │
│  │ Python FFI  │          │ TypeScript  │                   │
│  │ (orquestração│          │ (dashboard  │                   │
│  │  planejamento│          │  time-travel│                   │
│  │  LLM calls)  │          │  debugger)  │                   │
│  └─────────────┘          └─────────────┘                   │
└──────────────────────────────────────────────────────────────┘
```

---

## 4. Diferenciais Competitivos (Por Que Ayrola Vai Ganhar)

Não estamos competindo com o Prime Agent, OpenCode ou DeepSeek Harness em features. Estamos competindo em **propriedades fundamentais que eles não podem copiar facilmente**.

| Diferencial | Estado da Arte (2026) | Ayrola | Por Que é Incopiável |
|---|---|---|---|
| **Sub-100ms subagent spawning** | 500ms-2s (Python) | **10-50ms (Rust)** | Python tem GIL + startup overhead inerente |
| **Event-sourced memory com replay** | Best-effort snippets | **Append-only, time-travel** | Requer re-arquitetura completa do kernel |
| **Harness self-modification (nível 3)** | Pesquisa (Self-Harness paper) | **Produção com validação** | Rust type safety + hot-reload é única combinação viável |
| **Sandbox-per-agent zero overhead** | Docker/E2B (50-100ms) | **Linux namespaces (1-10ms)** | Rust é a única linguagem que consegue isso sem C |
| **RLM production-ready** | Teoria + experimentos | **Profundidade configurável, depth-bounded** | Precisa de kernel rápido para ser usável |

---

## 5. Roadmap de Revolução (18 Meses)

### Fase 0: Fundação (Meses 1-2)

**Objetivo:** Provar que Rust pode ser a base de um agent harness.

- [ ] Tokio runtime com event loop básico
- [ ] Tool calling via MCP (Python FFI para LangChain/LlamaIndex)
- [ ] Primeiro "Hello World" de agente funcional
- [ ] Benchmark: spawn de subagente em < 100ms

**Marco:** Um agente Rust que executa tool calls em menos de 100ms de startup.

---

### Fase 1: RLM Engine (Meses 3-5)

**Objetivo:** Provar que recursão real é viável em produção.

- [ ] RLM engine com depth-bounded recursion (default: 3)
- [ ] Spawn paralelo de subagentes via Tokio tasks
- [ ] Agregação de resultados parciais
- [ ] Load balancing de subagentes
- [ ] Cancellation tokens para abortar subagentes

**Marco:** Agente que decompõe uma tarefa complexa em 10 subtarefas, executa em paralelo, e agrega resultados em < 1s.

---

### Fase 2: Event Store (Meses 6-7)

**Objetivo:** Memória perfeita com replay.

- [ ] Append-only event log (memmap)
- [ ] Índice invertido para busca
- [ ] Snapshots periódicos
- [ ] Replay engine
- [ ] API de query: "Mostra o que o agente fez entre 14:00 e 15:00"

**Marco:** Replay completo de uma sessão de 2 horas em < 5s.

---

### Fase 3: Self-Improvement Nível 3 (Meses 8-11)

**Objetivo:** Agente que modifica o próprio código.

- [ ] Análise de trajetórias para detectar padrões de erro
- [ ] Geração de patches Rust (via LLM)
- [ ] Validação: Laya + Cargo check
- [ ] Hot-reload via `libloading`
- [ ] Rollback automático em caso de regressão

**Marco:** Agente que identifica um padrão de erro, gera um patch no próprio código, valida, aplica, e continua rodando com o novo comportamento.

---

### Fase 4: Sandbox-Per-Agent (Meses 12-13)

**Objetivo:** Isolamento real, zero overhead.

- [ ] Linux namespaces via `nix` crate
- [ ] Cgroups para resource limits
- [ ] Seccomp para syscall filtering
- [ ] Landlock para filesystem isolation
- [ ] Teste: 10 agentes em paralelo, 1 crasha, 9 continuam

**Marco:** 10 agentes rodando em paralelo, isolados, sem overhead mensurável.

---

### Fase 5: Polish & Documentação (Meses 14-18)

**Objetivo:** Produção-ready.

- [ ] Python harness definition (FFI maduro)
- [ ] TypeScript dashboard (time-travel debugger visual)
- [ ] Documentação completa
- [ ] Testes de stress (100 agentes, 1000 subagentes)
- [ ] Benchmark público (Oolong-Synthetic ou similar)
- [ ] Release candidate

**Marco:** Ayrola Harness v1.0 — production-ready, open-source, com documentação e exemplos.

---

## 6. O Que Não Vamos Fazer

Revolução não é só escolher o que fazer. É **escolher o que NÃO fazer**.

- ❌ **Não vamos fazer IDE** — Cursor e Windsurf já fazem isso melhor
- ❌ **Não vamos fazer sandbox próprio** — Docker, E2B, Anthropic srt já existem
- ❌ **Não vamos suportar 20 modelos** — Foco em qualidade, não quantidade
- ❌ **Não vamos fazer Python-first** — Rust é o kernel, ponto final
- ❌ **Não vamos buscar adoção massiva cedo** — Primeiro provar que funciona, depois crescer

---

## 7. Métricas de Sucesso

Como medir que a revolução está funcionando?

| Métrica | Target (Mês 18) | Como medir |
|---|---|---|
| **Subagent spawn latency** | < 50ms p99 | Benchmark Tokio |
| **Event store throughput** | 1M events/s | Stress test |
| **Replay de sessão 2h** | < 5s | Benchmark real |
| **Auto-melhoria (nível 3)** | 1 patch/semana aplicado com sucesso | Métrica interna |
| **Sandbox overhead** | < 1ms por agente | Benchmark |
| **Memory footprint idle** | < 20MB total | /usr/bin/time |
| **Benchmark coding** | > 85% Oolong-Synthetic | Comparável a RAH paper |

---

## 8. A Filosofia

### O Princípio Norte

> **"O harness não é scaffolding. O harness é o produto."**

Até 2025, agentes eram "modelo + tools". O harness era uma afterthought.

Nós invertemos: **o kernel é o produto**. O LLM é só um componente.

### O Manifesto

1. **Memória é sagrada** — nenhum evento é perdido, nenhuma decisão é esquecida.
2. **Velocidade é特征 (feature)** — sub-100ms spawning não é otimização, é requisito.
3. **Auto-melhoria é obrigatória** — um agente que não melhora a si mesmo está morto.
4. **Segurança por design** — sandbox-per-agent não é opcional, é fundamental.
5. **Rust não é negociável** — as propriedades que queremos só são possíveis em Rust.

---

## 9. Próximos Passos (Imediatos)

1. **[ ]** Setup do repo Rust (`cargo init --name ayrola-kernel`)
2. **[ ]** Tokio runtime + event loop básico
3. **[ ]** Primeiro benchmark: spawn de task em < 100ms
4. **[ ]** MCP client via Rust (chamar Python FFI para LangChain)
5. **[ ]** Documentar arquitetura em `docs/ARCHITECTURE.md`
6. **[ ]** Publicar manifesto no GitHub do projeto

---

## 10. Referências

Papers que fundamentam esta visão:

- **RLM** (Alex Zhang, 2025) — a base teórica
- **RAH** (Lumer, 2026) — prova de conceito com +9.6% sobre baseline
- **Continual Harness** (Karten, 2026) — auto-melhoria reset-free
- **Self-Harness** (Zhang, 2026) — agente modifica o próprio harness
- **Harness Engineering** (Lilian Weng, 2026) — "harness é a camada mais importante"

---

*Documento gerado em 2026-09-27. Versão 1.0 — Manifesto de Inovação.*

*"Não estamos construindo um agente. Estamos construindo uma nova classe de inteligência."*
