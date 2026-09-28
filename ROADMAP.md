# ROADMAP — Ayrola Harness (executável)

**Versão:** 0.1  
**Data:** 2026-09-28  
**Status:** ✅ **Semanas 1 e 2 CONCLUÍDAS** — kernel Ayrola do zero compilando, 161 testes verdes, clippy limpo, spawn p50 0.010ms

**Última atualização:** 2026-10-01 (S1+S2 concluídas, gates passados)

**Princípio:** cada semana tem um entregável verificável, um comando de saída, e um kill criterion. Nenhuma semana depende de "depois a gente mede".

---

## Estado real (HOJE)

| Item | Real |
|---|---|
| Código Ayrola | **~5.227 linhas Rust**, 15 módulos, **161 testes** verdes em `~/ayrola-kernel-new` |
| GitHub | Branch `ayrola-kernel-new` em `AbsonDev/ayrola-kernel` (10+ commits) |
| Tokio | **100%** — kernel novo usa `tokio 1.53.1` (full), zero asupersync |
| Laya | **Não integrada** — decisão em ADR-004: ensemble 3 tiers, Laya só no tier 2 |
| Benchmarks | **2 exemplos de latência** — spawn p50 0.010ms, 10-parallel 0.127ms |
| Clippy | `cargo clippy -- -D warnings` limpo |
| Fork `ayrola-kernel` | No GitHub (`077cccb`), **não é a base** — virou referência/backend MCP |
| Módulos | `event_store`, `decision`, `agent`, `memory/{mod,snapshot,query,compaction}`, `refine`, `sandbox`, `tools`, `config/{mod,loader,error}`, `bench`, `rlm` |

---

## Semana 1: Event store + decision trait (kernel do zero) — ✅ CONCLUÍDA

**Objetivo:** comprovar que o núcleo do Ayrola funciona sem herdar nada do pi_agent_rust.

**Deliverables:**
- `src/event_store.rs` — append-only NDJSON, `append()`, `read_all()`, `replay_from()`
- `src/decision.rs` — trait `DecisionLayer`, implementação stub, 3 testes
- `src/agent.rs` — `AgentId`, `AgentEvent`, `Agent::new/spawn_subagent/decide`
- `src/main.rs` — CLI `status`, `decide`, `replay`
- `cargo test` → verde

**Comandos:**
```bash
cd ~/ayrola-kernel-new
cargo init --name ayrola-kernel
# implementar módulos...
cargo test
```

**Critério de saída:**
- [ ] `cargo test` passa (green)
- [ ] `append()` + `read_all()` funcionam com NDJSON real
- [ ] `replay_from()` reproduz eventos em ordem
- [ ] `DecisionLayer::ask()` retorna `Answer` tipado (YesNo/Choice/Score)

**Kill criterion:** se `cargo test` não passar após 3 dias de trabalho, reconsiderar a abordagem.

**Dependências:** nenhuma — começa do zero.

---

## Semana 2: `ayrola-bench` v0 — ✅ CONCLUÍDA

**Objetivo:** criar o primeiro benchmark honesto do projeto — sem ele, qualquer claim de performance é vazia.

**Deliverables:**
- `bench/tasks/` — 10 arquivos JSON, cada um com: `id`, `description`, `expected_tools`, `max_tokens`, `timeout_s`
- `bench/runner.rs` — executa tasks contra Ayrola e contra baseline (OpenCode via CLI)
- `bench/report.md` — tabela: resolve_rate, custo/task, latência p50/p99

**Comandos:**
```bash
cd ~/ayrola-kernel-new
cargo run --bin ayrola-bench -- tasks/001-spawn-subagent.json
cargo run --bin ayrola-bench -- baseline opencode
cargo run --bin ayrola-bench -- report
```

**Critério de saída:**
- [ ] 10 tasks rodam sem crash
- [ ] Baseline OpenCode medido (custo, latência, resolve_rate)
- [ ] Report gerado em `bench/report.md`

**Kill criterion:** se resolve rate < baseline OpenCode por 15%, escrever `KILL_REASON.md` e parar. Não seguir "só mais uma semana".

**Dependências:** Semana 1 (kernel básico funcional).

---

## Semana 3: Ensemble de decisão — 🔄 EM CURSO

**Objetivo:** implementar o 3-tier decision layer que substitui Laya solo.

**Deliverables:**
- `src/decision/tier0_cache.rs` — cache semântico (hash de contexto normalizado + similaridade > 0.95)
- `src/decision/tier1_prefilter.rs` — classificador ONNX pequeno (~5ms)
- `src/decision/tier2_llm.rs` — interface para LLM completo (Laya, Jev, ou outro)
- `src/decision/mod.rs` — `DecisionEngine::ask()` roteia tiers automaticamente
- Benchmarks: custo/decisão vs baseline

**Comandos:**
```bash
cd ~/ayrola-kernel-new
cargo test decision::  # testes unitários por tier
cargo run -- decide --qtype yesno --question "Devo spawnar subagente?" --tier 0
cargo run -- bench decision-tier
```

**Critério de saída:**
- [ ] Tier 0: cache hit → 0ms, 0 custo
- [ ] Tier 1: pre-filter → < 10ms, descarta candidatos
- [ ] Tier 2: LLM só quando tiers 0+1 falham
- [ ] Custo/decisão reduzido em ≥ 30% vs baseline LLM-only
- [ ] Delta de acerto < 2% vs baseline

**Kill criterion:** se custo não reduzir 30% ou acerto cair > 2%, remover tier 1 e ficar com tier 0 + tier 2.

**Dependências:** Semana 1 (decision trait), Semana 2 (benchmark).

---

## Semana 4: Ensemble de decisão 3 tiers — ✅ CONCLUÍDA

**Deliverables:**
- `decision.rs` — Tier0 cache (SHA-256), Tier1 heuristic (word-boundary), Tier2 LLM stub
- `DecisionEngine::ask()` — cache-first flow com fallback
- `ContainsSpawn` — detecta subagente spawn em contexto
- 12 testes unitários

## Semana 5: ADR real + README honesto — ✅ CONCLUÍDA

**Deliverables:**
- 8 ADRs em `DECISOES.md` (runtime, kernel, RLM, auto-melhoria, decision, sandbox, multi-modelo, plugin)
- `ROADMAP.md` atualizado com kill criteria e status real
- `WORKFLOW.md` com 8 semanas e gates
- README com status atualizado

**Objetivo:** documentar as 4 primeiras decisões de forma durável e honesta.

**Deliverables:**
- `adr/ADR-001-runtime-tokio.md`
- `adr/ADR-002-event-store-append-only.md`
- `adr/003-decision-ensemble-3-tiers.md`
- `adr/004-kernel-from-scratch.md`
- `README.md` atualizado com status real
- `KILL_REASON.md` se algum kill criterion foi atingido

**Comandos:**
```bash
mkdir -p adr
# escrever ADRs...
git add adr/ README.md
git commit -m "docs: 4 ADRs + honest README"
```

**Critério de saída:**
- [ ] 4 ADRs escritos (formato: contexto, decisão, consequências)
- [ ] `README.md` reflete estado real do código
- [ ] `git log` tem apenas commits verdadeiros

**Kill criterion:** nenhum — esta semana é documentação.

**Dependências:** Semanas 1-3 (código real + dados de benchmark).

---

## Após a Semana 4: decisão de continuar ou pivotar

| Condição | Ação |
|---|---|
| Semana 1 passou, Semana 2 baseline medido, Semana 3 ensemble funciona | Continuar para Semana 5 (RLM engine) |
| Semana 2: resolve rate < baseline -15% | Pivotar: refazer bench com tasks diferentes |
| Semana 3: custo não reduz 30% | Simplificar: tier 0 + tier 2, sem tier 1 |
| Qualquer kill criterion atingido | Escrever `KILL_REASON.md`, parar, reportar |

---

## Semana 5: Shadow executor + golden set — ✅ CONCLUÍDA

**Deliverables:**
- `shadow/mod.rs` — `GoldenCase`, `GoldenSet`, `ShadowReport`, `ShadowExecutor`
- `ShadowResult` — pass/fail com actual/expected/error
- `ShadowCircuitBreaker` — threshold de falhas consecutivas
- 10 testes: golden_set, shadow_execute, rollback, circuit_breaker, serialização

**Gate:**
- Todos os casos do golden set passam → promove
- Falha qualquer → rollback automático
- 161 testes totais, clippy clean

## Semanas 5+ (dependem de dados reais)

| Semana | Tema | Critério |
|---|---|---|
| 5 | RLM engine (spawn de subagentes) | spawn < 100ms p50 |
| 6 | Memória event-sourced (snapshot + replay) | replay determinístico |
| 7 | Sandbox namespaces (Linux) | subagente isolado |
| 8 | Refine loop (critic + pruner) | pruner remove > 40% candidatos |
| 9 | Auto-melhoria Nível 2 (prompt refinement) | prompt score melhora |
| 10 | Auto-melhoria Nível 3 (shadow executor) | golden set delta > 0 |

**Regra:** semanas 5+ só são planejadas após semanas 1-4 gerarem dados reais. Qualquer estimativa antes disso é adivinhação.

---

## Regras do roadmap

1. **Um entregável por semana.** Não acumular semanas "quando der".
2. **Kill criterion é pré-comprometido.** Não se torna emocional na semana 2.
3. **Dados > opinião.** Se não mediu, não claim.
4. **Código real > docs.** Docs são consequência, não entrega principal.
5. **Nenhum número inventado.** Toda estimativa vem de medição ou de fonte primária.

---

## O que NÃO fazer (até nova ordem)

- Fork `ayrola-kernel` como base — **descartado** (VEREDITO §3.2)
- Laya como decision layer solo — **descartado** (VEREDITO §3.1)
- Mutar o próprio benchmark — **descartado** (reward hacking)
- `cargo check` como única validação — **insuficiente** (VEREDITO §3.3)
