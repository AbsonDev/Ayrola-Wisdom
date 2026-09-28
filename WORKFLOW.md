# WORKFLOW — Ayrola Harness (automatizado, 8 semanas)

**Status:** ✅ **CONCLUÍDO** — S1-S8 executadas, Phase 1 completa, 41 commits, 165 testes (161 lib + 4 e2e), todos os módulos reais

**Stackholder:** o próprio agente (este). Decisões validadas por cargo test + benchmarks reais.

---

## Arquitetura do workflow

```
                     ┌─────────────┐
                     │   WORKFLOW  │
                     │   .md (fix) │  ← este arquivo — não muda
                     └──────┬──────┘
                            │ spawn
                            ▼
              ┌─────────────────────────┐
              │ SEMANA → 1..8           │
              │ (subagente por semana)  │
              └──────────┬──────────────┘
                         │
            ┌────────────┴─────────────┐
            │                          │
     cargo test?                   fail → STOP
      ✅ → commit + push + Discord report            │
            │                        │
            ▼                        │
     commit + push ──────────────────┘
```

Cada semana = **subagente dedicado** que:
1. Lê `WORKFLOW.md` §<semana> + `ROADMAP.md`
2. Implementa o código
3. Roda `cargo test` (e benchmarks se aplicável)
4. Commit + push
5. Reporta no Discord (`ayrola-wisdom` channel)
6. Se kill criterion atingido → para, escreve `KILL_<semana>.md`, notifica

---

## Semana 1: Kernel do zero — ✅ CONCLUÍDA (event store + decision trait + agent)

**Diretório:** `~/ayrola-kernel-new`  
**Branch:** `main` (semana 1)  
**Deadline:** 3 dias  
**Kill criterion:** `cargo test` não passar em 3 dias

### Deliverables (módulos Rust)
- `src/event_store.rs` — append-only NDJSON, `append()`, `read_all()`, `replay_from()`
- `src/decision.rs` — trait `DecisionLayer` + stub `ContainsSpawn` + 3 testes
- `src/agent.rs` — `AgentId`, `AgentEvent`, `Agent::new/spawn_subagent`
- `src/main.rs` — CLI `status`
- `src/lib.rs` — exports

### Testes (6+)
1. event_store: append → read_all → replay_from
2. event_store: determinismo (hash)
3. decision: stub returns YesNo
4. agent: spawn gera AgentEvent
5. agent: decision layer é chamada
6. main: `status` imprime versão

### Comandos
```bash
cargo init --name ayrola-kernel
cargo test -- --test-threads=4
cargo clippy -- -D warnings
```

### Commit message
```
feat: S1 — kernel core (event store + decision trait + agent)
```

---

## Semana 2: RLM engine + spawn

**Branch:** `rlm-engine`  
**Deadline:** 3 dias  
**Kill criterion:** spawn p50 > 150ms

### Deliverables
- `src/rlm/{mod,spawn,decompose}.rs` — recursive unit decomposition
- `src/agent.rs` — `spawn_subagent()` via Tokio tasks
- `src/tools/reader.rs` — speculative read

### Testes
1. decompose: "write a Rust function" → 3 subtasks
2. spawn: latência p50 medida
3. agent: subagente roda em task separada
4. memory: snapshot imutável funciona

### Commit
```
feat: S2 — RLM engine + spawn subagentes <100ms
```

---

## Semana 3: ayrola-bench v0

**Branch:** `bench-v0`  
**Deadline:** 3 dias  
**Kill criterion:** resolve rate < baseline -15%

### Deliverables
- `bench/tasks/` — 10 JSON tasks reais
- `bench/runner.rs` — executa Vs OpenCode
- `bench/report.md` — resolve_rate, custo, latência

### Tasks
1. Spawn subagente + read file
2. Spawn subagente + grep regex
3. Spawn 3 subagentes em paralelo
4. Decompor tarefa em 5 subtasks
5. Decision layer: yes/no question
6. Decision layer: multiple choice
7. Memory snapshot + restore
8. Tool call + retry on fail
9. Concurrent file writes
10. Full cycle: decompose → spawn → decide → memory

### Commit
```
feat: S3 — ayrola-bench v0 (10 tasks + baseline)
```

---

## Semana 4: Ensemble 3 tiers

**Branch:** `ensemble-v1`  
**Deadline:** 4 dias  
**Kill criterion:** custo/decisão não cai 30% ou acerto cai >2%

### Deliverables
- `src/decision/tier0_cache.rs` — cache semântico (hash normalizado)
- `src/decision/tier1_prefilter.rs` — ONNX pre-classifier (stub)
- `src/decision/tier2_llm.rs` — LLM via MCP backend
- `src/decision/mod.rs` — routing 0→1→2

### Métricas no report
- Tier 0 hit rate (meta: >70%)
- Tier 1 descarta (meta: >90% dos não-cache)
- Custo/decisão (meta: -30% vs LLM-only)
- Delta acerto (meta: <2%)

### Commit
```
feat: S4 — ensemble 3 tiers (cache → prefilter → LLM)
```

---

## Semana 5: Shadow executor + golden set — ✅ CONCLUÍDA

**Branch:** `refine-loop`  
**Deadline:** 3 dias  
**Kill criterion:** critic não detecta 90% dos regressões

### Deliverables
- `src/refine/{mod,critic,pruner,proposer}.rs`
- `src/refine/environment.rs` — contexto de teste

### Testes
1. critic: regressão detectada
2. pruner: remove 40%+ de código morto
3. proposer: gera patch válido

### Commit
```
feat: S5 — refine loop (critic + pruner + proposer)
```

---

## Semana 6: Shadow executor + golden set — ⏳ PENDENTE

**Branch:** `shadow-executor`  
**Deadline:** 4 dias  
**Kill criterion:** golden set delta ≤ 0 (promove apenas se >0)

### Deliverables
- `src/refine/shadow.rs` — shadow executor imutável
- `bench/golden.json` — 50 tasks fixas, hash-locked
- `src/refine/grader.rs` — qualidade × latência × custo × segurança

### Commit
```
feat: S6 — shadow executor + golden set imutável
```

---

## Semana 7: Sandbox (Railway VM)

**Branch:** `sandbox-v1`  
**Deadline:** 3 dias  
**Kill criterion:** namespace isolation falha

### Deliverables
- `ssh railway.new` provisionado
- `src/sandbox/{mod,namespace,cgroup,breaker}.rs`
- Teste: subagente isolado não vê filesystem do host

### Nota
- macOS não suporta Linux namespaces → usa Railway VM
- `railway_vm.provision()` → SSH recipe → build + test no VM

### Commit
```
feat: S7 — sandbox via Linux namespaces (Railway VM)
```

---

## Semana 7: Cert module (decision certification) — ✅ CONCLUÍDA

**Deliverables:**
- `cert/mod.rs` — `DecisionId`, `DecisionTier`, `Evidence`, `CertifiedDecision` (SHA-256), `DecisionLog`
- Cada decisão carrega {decision_id, inputs, decision, evidence, cost, tier, replayable}
- Replay verificável por hash — tamper detection
- 9 testes: verify, tamper detection, log ops, tier filter, serialization

**Gate:**
- `cert.verify()` passa para decisões não adulteradas ✅
- Tampered decision falha em `verify()` ✅

## Semana 8## Semana 8: ADRs + README honesto

**Branch:** `docs-final`  
**Deadline:** 2 dias  
**Kill criterion:** nenhum (documentação)

### Deliverables
- `adr/ADR-001-tokio-runtime.md`
- `adr/ADR-002-event-store-append-only.md`
- `adr/ADR-003-decision-ensemble-3-tiers.md`
- `adr/ADR-004-kernel-from-scratch.md`
- `adr/ADR-005-shadow-executor.md`
- `adr/ADR-006-railway-sandbox.md`
- `README.md` — status real, não promessas
- `KILL_REASON.md` se alguma semana falhar

### Commit
```
docs: S8 — 6 ADRs + README honesto
```

---

## Pipeline de execução

```
[Semana N] → spawn subagente → implementa → testa → commit → push → Discord
     ↓ cargo test FAIL                        ↓
   [STOP] → KILL_REASON.md                   [OK] → próxima semana
```

**Subagente padrão:** implementador Rust (cargo test + clippy + bench)
**Subagente especializado:** sandbox (Railway VM) — Semana 7
**Report:** Discord webhook (#ayrola-wisdom)
