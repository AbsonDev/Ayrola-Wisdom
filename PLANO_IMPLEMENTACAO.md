# PLANO DE IMPLEMENTAÇÃO — Papers → Kernel Ayrola

Cada paper da biblioteca foi mapeado a uma **decisão concreta de código**. Este documento
é o contrato entre a pesquisa e a implementação.

Gerado: 2026-09-28 | Papers: 55 (`orx discover`) + 13 (alphaXiv CLI) | Fonte: `~/ayrola-research-papers/`

---

## Pilar 1 — Memória event-sourced + time-travel

### Papers fonte
| arXiv | Título | Votos | O que ensina |
|---|---|---|---|
| `2608.24876` | Recursive Experiential-Working Memory Evolution | 85⭐ | Working memory evolutiva; como comprimir histórico sem perder causalidade |
| `2607.27773` | ChronoMem: Version Control + Semantic Rollback | 11⭐ | Rollback semântico — diff de estado, não de bytes |
| `2609.27279` | EnSIMem: Entity-Structured Indexing | 7⭐ | Indexar memória por entidade, não por timestamp |
| `2608.06745` | MemPrism: Task-Conditioned Relational Memory Views | 6⭐ | Views por tarefa sobre a mesma memória base |

### Decisões de código
1. **Event envelope estendido** — adicionar `causal_id` e `parent_event_id` ao `EventEnvelope<E>` atual.
   ```rust
   pub struct EventEnvelope<E> {
       pub seq: u64,
       pub timestamp: i64,
       pub agent_id: AgentId,
       pub causal_id: Uuid,        // NOVO: liga eventos pai↔filho
       pub parent_event_id: Option<Uuid>, // NOVO: cadeia de causalidade
       pub payload: E,
   }
   ```
2. **Snapshot + delta** — compaction a cada N eventos gera snapshot; delta fica em NDJSON.
   Snapshot em `events.snapshot.{seq}.json`, delta em `events.ndjson` (append-only).
3. **Rollback semântico** (ChronoMem) — `replay_to(seq)` reconstrói estado; diff entre dois
   snapshots mostra **o que mudou semanticamente**, não os bytes.
4. **Indexação por entidade** (EnSIMem) — tabela `event_entities(seq, entity_type, entity_id)`;
   permite `query_by_entity("ticket", "T-123")` sem varrer NDJSON.

### Onde implementa
- `ayrola-kernel/src/event_store.rs` — `EventEnvelope` estendido, snapshot, `replay_to()`
- Novo: `ayrola-kernel/src/memory/query.rs` — `query_by_entity()`, `replay_to()`

---

## Pilar 2 — Subagentes <100ms

### Papers fonte
| arXiv | Título | Votos | O que ensina |
|---|---|---|---|
| `2608.spec-ptc` | Speculative Programmatic Tool Calling | 190⭐ | Prever a próxima tool call e executar speculativamente |
| `2605.06639` | Recursive Agent Optimization | 81⭐ | Otimizar o *harness*, não o modelo |
| `2605.24486` | AgentFugue: Agent Scaling for Long-Horizon | 6⭐ | Delegação colecionada para tarefas longas |
| `2608.23552` | Prime Agent: Self-Improving RLM Harness | 357⭐ | IPython REPL como kernel; padrão de tool protocol |
| `2609.20519` | SoL-Pi: Recursively Scaling Auto-Research Loops | 181⭐ | 4 mecanismos que sobrevivem seleção: action execution, context compaction, observation handling, delegated reading |

### Decisões de código
1. **Spawn via Tokio `task::spawn` + workdir isolado** — já é o padrão do `Agent::spawn_subagent`
   atual. Cada subagente cria `~/.ayrola/work/{agent_id}/` (não processo, não PID).
2. **Speculative tool calling** (Spec-PTC) — speculative execution buffer: quando o LLM
   pede tool X, executa X e X+1 em paralelo; se X+1 não é usada, descarta.
   ```rust
   pub struct SpeculativeExecutor {
       inflight: DashMap<ToolCallId, JoinHandle<ToolResult>>,
       window: usize,  // quantas calls especular
   }
   ```
3. **Context compaction no subagente** (SoL-Pi) — subagente tem `max_context_tokens`; ao
   ultrapassar, resume e continua. Isso é diferente do compaction do agente pai.
4. **Delegated reading** (SoL-Pi) — subagente especial `type Reader` que só lê e resume;
   não tem tools de escrita. Mais rápido e seguro para documentos longos.
5. **RLM engine** (Fase 1) — trait `RecursiveAgent` com `max_depth: 3`.

### Onde implementa
- `ayrola-kernel/src/agent.rs` — `spawn_subagent()` com workdir isolado
- Novo: `ayrola-kernel/src/tools/speculative.rs` — `SpeculativeExecutor`
- Novo: `ayrola-kernel/src/rlm/mod.rs` — trait `RecursiveAgent`, `RlmEngine`

---

## Pilar 3 — Auto-melhoria (níveis 1→3)

### Papers fonte
| arXiv | Título | Votos | O que ensina | Nível |
|---|---|---|---|---|
| `2609.24972` | RRSI: Regularized RSI | 218⭐ | Pruner remove mudanças pequenas/caras; critic rejeita benchmark-specific | 2 |
| `2609.15364` | RSIAgent: Autonomous Exploration | 130⭐ | Exploração autônoma de espaço de mudanças | 2 |
| `2609.08183` | NeoHorse-1: RSI via Agentic Post-training | 71⭐ | RSI no nível de *post-training* do modelo | 3 |
| `2609.11873` | The Last AI Built by Humans | 305⭐ | O que falta para RSI genuíno | 3 |
| `2609.14858` | Dream-RSI: RSI via Evolving Worlds | 244⭐ | Mutar o *ambiente* de avaliação, não só o agente | 3 |
| `2608.23552` | Prime Agent `/refine` | 357⭐ | Nível 2: refinar skills/memórias/prompt notes | 2 |

### Decisões de código
1. **Nível 1 — Observabilidade** (já impl. parcialmente): todo `AgentEvent` no event store.
2. **Nível 2 — Refinement** — `ayrola-kernel/src/refine/` com:
   - `Critic`: Laya avalia se uma mudança proposed é *reutilizável* ou *benchmark-specific*
   - `Pruner`: remove mudanças com delta < threshold, custo > budget, ou sem ganho em N iterações
   - `Proposer`: gera candidatos com **budget temporalmente annealed** (RRSI)
3. **Nível 3 — Harness self-modification** — o harness edita os próprios prompts, tools e
   policies. Guardado: **reversible** (snapshot antes de cada mudança).
4. **Dream-RSI (nível 3+)** — mutar também o *benchmark suite* para evitar overfitting.
   `ayrola-kernel/src/refine/environment.rs`

### Onde implementa
- Novo diretório: `ayrola-kernel/src/refine/`
  - `mod.rs`, `critic.rs`, `pruner.rs`, `proposer.rs`, `environment.rs`
- Prompt note / skill / subagent spec entries: `rlm.harness.*`

---

## Pilar 4 — Sandbox-per-agent via Linux namespaces

### Papers fonte
| arXiv | Título | Votos | O que ensina |
|---|---|---|---|
| `2609.00595` | SoK: When Safe Agents Fail Together | 10⭐ | Agentes seguros individualmente, mas o *grupo* falha junto |
| `2607.12406` | Isolation as First-Class Principle | 8⭐ | Isolação como primitiva, não add-on |
| `2608.16157` | FreeToken: Edge-Native MoE Serving | 105⭐ | Serving local sem cloud |

### Decisões de código
1. **Namespace por subagente** — cada subagente roda em seu próprio
   `CLONE_NEWUSER | CLONE_NEWNS | CLONE_NEWPID | CLONE_NEWNET`.
   Usar crate `nix` ou `caps` (não FFI manual).
2. **Falha coletiva** (SoK) —、协调: se 3+ subagentes falham na mesma tool, circuit breaker
   abre e o pai recebe `ToolCooldown` em vez de retry infinito.
3. **Cgroup por subagente** — limite de memória (`memory.max`) e CPU (`cpu.max`)
   por subagente. Via `systemd-run --scope` ou escrita direta em `/sys/fs/cgroup/`.

### Onde implementa
- Novo: `ayrola-kernel/src/sandbox/mod.rs`, `namespace.rs`, `cgroup.rs`
- Integração: `Agent::spawn_subagent()` cria namespace antes do `tokio::spawn`

---

## Fases do roadmap (revisadas com papers)

| Fase | Duração | Entregável | Papers que informam |
|---|---|---|---|
| **0 — Fundação** | ✅ feito | Kernel Tokio + Laya stub + event store NDJSON | — |
| **0.5 — Research Synthesis** | 1-2 sem | Este documento + ADRs por paper | Todos os 55 |
| **1 — RLM Engine** | 3-5 mo | `RecursiveAgent` trait, 3 subagentes paralelo <100ms | Prime Agent, Spec-PTC, RAO, SoL-Pi |
| **2 — Event-Sourced Memory** | 2-3 mo | Snapshot+delta, `replay_to()`, entity index | ChronoMem, EnSIMem, MemPrism, EWM |
| **3 — Auto-Melhoria N2** | 4 mo | Critic+Pruner+Proposer, refinement loop | RRSI, RSIAgent, Prime Agent `/refine` |
| **4 — Sandbox-per-Agent** | 2 mo | Namespaces + cgroups + circuit breaker | SoK Multi-Agent Security, Isolation |
| **5 — Auto-Melhoria N3** | 5 mo | Harness self-modification + Dream-RSI | NeoHorse-1, Dream-RSI, Last AI Built |

---

## Módulos Rust a criar (mapa completo)

```
ayrola-kernel/src/
├── lib.rs
├── main.rs
├── event_store.rs          ← ESTENDIDO (causal_id, parent_event_id, snapshot)
├── decision.rs             ← ESTENDIDO (DecisionLayer trait real, Laya ONNX)
├── agent.rs                ← ESTENDIDO (workdir isolado, namespace, cgroup)
│
├── rlm/                    ← NOVO (Fase 1)
│   ├── mod.rs              RlmEngine, RecursiveAgent trait
│   ├── decompose.rs        Task decomposition
│   └── spawn.rs            Subagent fan-out
│
├── memory/                 ← NOVO (Fase 2)
│   ├── mod.rs
│   ├── snapshot.rs         Snapshot + delta compaction
│   ├── query.rs            query_by_entity, replay_to
│   └── compaction.rs       Context compaction por subagente
│
├── refine/                 ← NOVO (Fase 3)
│   ├── mod.rs
│   ├── proposer.rs         Budget temporalmente annealed
│   ├── critic.rs           Laya avalia reusabilidade
│   ├── pruner.rs           Remove mudanças inefetivas
│   └── environment.rs      Dream-RSI: mutar suite de benchmark
│
├── sandbox/                ← NOVO (Fase 4)
│   ├── mod.rs
│   ├── namespace.rs        Linux namespaces por subagente
│   ├── cgroup.rs           Limites de memória/CPU
│   └── breaker.rs          Circuit breaker collective
│
└── tools/
    ├── mod.rs              ToolProtocol
    ├── speculative.rs      SpeculativeExecutor (Spec-PTC)
    └── reader.rs           Delegated reading subagent
```

---

## Conceito foundational: Language Model Shape

**Paper:** `2609.language-model-shape` (Alex Zhang, 2026)

> "Design language models around harnesses, not the other way around."

- Laya é uma instância desse "shape" — output constrainido, prefill-only, rápido
- Abre espaço para modelos especializados: tool-calling, memory indexing, routing
- RLCD (Reinforcement Learning for Calibrated Decisions) — objective function para modelos de decisão
- Conecta com RAH: harness recursion + model shape = sistema coeso

**Decisão:** O Ayrola adota a filosofia de "model shape per function":
- Laya = decision shape
- Futuro: tool-selector shape, memory-embedding shape, router shape

---

## ADRs a escrever (a partir dos papers)

| ADR | Decisão | Paper fonte |
|---|---|---|
| ADR-001 | Tokio como runtime (não asupersync) | pi_agent_rust análise |
| ADR-002 | Event store append-only com causal chain | ChronoMem, RAH |
| ADR-003 | Recursive unit = harness completo, não model call | RAH (2606.13643) |
| ADR-004 | Ensemble 3 tiers (cache -> pre-filter ONNX -> LLM) | Rejected Laya solo |
| ADR-005 | Refinement com critic+pruner (não free-form) | RRSI |
| ADR-006 | Sandbox via namespaces, não Docker | SoK Multi-Agent |
| ADR-007 | Delegated reading como tipo de subagente | SoL-Pi |
| ADR-008 | Speculative tool execution com discard | Spec-PTC |

---

## Scripts de pesquisa (reprodução)

```bash
# 1. Buscar papers por tema
orx discover embedding "query" --limit 20 --prioritize recency

# 2. Ler paper completo
orx paper <arxiv-id> --full

# 3. Extrair texto de PDF
alphaxiv paper text <arxiv-id>

# 4. Papers similares (para扩Cf的研究)
alphaxiv paper similar <arxiv-id>

# 5. Atualizar este documento
python3 scripts/update-research.py
```

---

**Próximo passo:** escolher Caminho A (do zero) ou B (fork `pi_agent_rust`) e começar Fase 1.
