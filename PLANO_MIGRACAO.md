# PLANO DE MIGRAÇÃO — Fork pi_agent_rust → Ayrola Kernel

**Base:** `Dicklesworthstone/pi_agent_rust` (MIT, 875 arquivos .rs)
**Destino:** `AbsonDev/ayrola-kernel` (branch `main`, commit `9f0799a` — já commitado local)
**Data:** 2026-09-28

---

## 1. Estado atual

O fork foi materializado em `~/ayrola-kernel` e commitado localmente. O remote foi
configurado, mas **o repositório ainda precisa ser criado no GitHub** (ver seção 8).

- Commit local: `9f0799a` — 3.361 arquivos, 1.939.000 linhas
- Kernel da Fase 0 (5 módulos, 9/9 testes) preservado em `~/ayrola-kernel-phase0-backup`
- Runtime atual: `asupersync` v0.5.0 (precisa virar Tokio)

---

## 2. Classificação dos módulos (decisão final)

### 2.1 HERDAR COMO BASE (48 módulos, ~4.5 MB)

Estes já implementam pilares do Ayrola. Reusamos o código, ajustando apenas o runtime.

| Módulo | Tamanho | Pilar | Ajuste necessário |
|---|---|---|---|
| `src/subagents/` (5 files) | 91 KB | 2 — Subagentes | asupersync→Tokio; adicionar workdir isolado por subagente |
| `src/memory/` (4 files) | 94 KB | 1 — Memória | asupersync→Tokio; adicionar event store event-sourced |
| `src/worktree_iso/` (2 files) | 27 KB | 4 — Sandbox | **ESTENDER**: adicionar Linux namespaces (CLONE_NEWUSER\|NEWNS\|NEWPID\|NEWNET) |
| `src/session_control/` (5 files) | 59 KB | 1 — Sessões | asupersync→Tokio |
| `src/resource_governor.rs` | 170 KB | 4 — Sandbox | asupersync→Tokio; adicionar cgroup limits |
| `src/approval.rs` | 27 KB | 4 — Sandbox | asupersync→Tokio |
| `src/security_scan/` (3 files) | — | 4 — Sandbox | manter |
| `src/mcp/` (6 files) | 539 KB | Tools | asupersync→Tokio |
| `src/extensions/` (12 files) | 587 KB | Tools | asupersync→Tokio |
| `src/providers/` (16 files) | 1.3 MB | LLM | **REDUZIR**: manter openai, anthropic, gemini; remover 13 outros |
| `src/lsp/` (12 files) | — | Tools | manter |
| `src/browser/` (8 files) | — | Tools | manter |
| `src/computer/` (3 files) | — | Tools | manter |
| `src/media_tools/` (5 files) | — | Tools | manter |
| `src/http/` (6 files) | — | Core | manter |
| `src/connectors/` (2 files) | — | Core | manter |
| `src/secrets/` (1 file) | — | Core | manter |
| `src/text_completion/` (1 file) | — | LLM | manter |
| `src/bin/` (2 files) | — | Core | manter |

### 2.2 REESCREVER DO ZERO (18 módulos — núcleo do Ayrola)

Estes são o coração do Ayrola. A implementação do pi_agent não serve.

| Módulo pi_agent | Tamanho | Por que reescrever | Novo módulo Ayrola |
|---|---|---|---|
| `src/agent.rs` | 871 KB | Loop monolítico, acoplado ao pi | `src/agent.rs` (novo, ~400 linhas) + `src/rlm/` |
| `src/session.rs` | 692 KB | Formato do pi, sem event sourcing | `src/memory/snapshot.rs` + `src/memory/query.rs` |
| `src/tools.rs` | 813 KB | Tool set do pi (36 tools genéricos) | `src/tools/mod.rs` + `speculative.rs` + `reader.rs` |
| `src/compaction.rs` | 194 KB | Compaction heurística | `src/memory/compaction.rs` (por subagente) |
| `src/compaction_worker.rs` | 462 KB | Worker acoplado ao runtime | (eliminado — vira parte de `memory/`) |
| `src/compaction_snap.rs` | 25 KB | Snapshots do pi | (eliminado — vira `memory/snapshot.rs`) |
| `src/checkpoint.rs` | 346 KB | Checkpoint do pi | (eliminado — event store cobre) |
| `src/scheduler.rs` | 154 KB | Scheduler do pi | `src/rlm/spawn.rs` (Tokio tasks) |
| `src/swarm_replay.rs` | 138 KB | Replay do swarm do pi | `src/memory/query.rs::replay_to()` |
| `src/swarm_activity_ledger.rs` | 121 KB | Ledger do pi | (eliminado — event store cobre) |
| `src/agent_hub.rs` | 565 KB | Hub monolítico | `src/rlm/mod.rs` |
| `src/session_store_v2.rs` | 234 KB | Store do pi | (eliminado — NDJSON event store) |
| `src/session_sqlite.rs` | 96 KB | SQLite do pi | (eliminado — event store) |
| `src/session_index.rs` | 137 KB | Índice do pi | `src/memory/query.rs` (entity index) |
| `src/semantic_workspace_graph.rs` | 233 KB | Grafo do pi | (adiado — Fase 5+) |
| `src/context_files.rs` | 461 KB | Context files do pi | (eliminado — event store) |
| `src/models.rs` | 402 KB | Model registry do pi | (reduzir: 10 modelos, não 400) |
| `src/provider_metadata.rs` | 135 KB | Metadata do pi | (reduzir) |
| `src/failover.rs` | 106 KB | Failover do pi | (manter simplificado) |

### 2.3 REMOVER (não essenciais — ~6 MB)

| Módulo | Razão |
|---|---|
| `src/interactive_ftui.rs` (649 KB) | TUI Bubble Tea — fora do escopo |
| `src/interactive.rs` + `src/interactive/` | TUI |
| `src/autocomplete.rs` (99 KB) | TUI autocomplete |
| `src/keybindings.rs` (104 KB) | TUI keybindings |
| `src/extensions_js.rs` (1.35 MB) | Runtime JS — fora do escopo Rust-puro |
| `src/extensions.rs` (746 KB) | Dispatcher JS legado |
| `src/extension_dispatcher.rs` (521 KB) | Dispatcher JS legado |
| `src/extension_preflight.rs` (187 KB) | Pré-flight de extensions JS |
| `src/extension_scoring.rs` (125 KB) | Scoring de extensions JS |
| `src/auth.rs` (492 KB) | Auth custom — não necessário |
| `src/crypto_shim.rs` (647 KB) | Crypto shim |
| `src/package_manager.rs` (305 KB) | Gerenciador de pacotes |
| `src/rpc.rs` (851 KB) | RPC legado — Ayrola usa MCP |
| `src/sdk.rs` (215 KB) | SDK do pi |
| `src/pi_wasm.rs` (127 KB) | WASM host |
| `src/doctor.rs` (504 KB) | Diagnóstico do pi |
| `src/conformance.rs` (168 KB) | Conformance do pi |
| `src/conformance_shapes.rs` (78 KB) | Shapes do pi |
| `src/acp.rs` (112 KB) | Agent Client Protocol |
| `src/btw.rs` (23 KB) | Role model smol |
| `src/buffer_shim.rs` (20 KB) | Buffer shim |
| `src/crash.rs` (28 KB) | Crash reporting |
| `src/vcr.rs` (97 KB) | VCR de testes |
| `src/ask.rs` (64 KB) | Ask UI |
| `src/ast_tools.rs` (44 KB) | AST tools |
| `src/bpe.rs` (29 KB) | BPE tokenizer |
| `src/completions.rs` (7 KB) | Shell completions |
| `src/artifact_output.rs` (18 KB) | Artifact output |
| `src/commit_split.rs` (23 KB) | Commit split |
| `src/jobs.rs` (197 KB) | Job queue — substituído por Tokio |
| `legacy_pi_mono_code/` | Código TypeScript legado |
| `fuzz/` | Fuzzing do pi |
| `.beads/`, `.dsr/`, `.rch/` | Meta-issues do pi |

### 2.4 MÓDULOS NOVOS DO AYROLA (17 arquivos)

```
src/
├── lib.rs                    NOVO — exports públicos
├── main.rs                   REESCREVER — CLI Ayrola (status/decide/replay/chat/doctor/bench)
├── event_store.rs            NOVO — Pilar 1: NDJSON append-only + causal chain + snapshot
├── decision.rs               NOVO — Pilar 3: trait DecisionLayer + Laya ONNX + stub
├── agent.rs                  NOVO — Pilar 2: AgentId, AgentEvent, spawn, think, decide
├── rlm/
│   ├── mod.rs                NOVO — RlmEngine + trait RecursiveAgent
│   ├── decompose.rs          NOVO — task decomposition
│   └── spawn.rs              NOVO — fan-out via Tokio task::spawn
├── memory/
│   ├── mod.rs                NOVO
│   ├── snapshot.rs           NOVO — snapshot + delta compaction
│   ├── query.rs              NOVO — query_by_entity, replay_to
│   └── compaction.rs         NOVO — compaction por subagente
├── refine/
│   ├── mod.rs                NOVO — RefineLoop
│   ├── proposer.rs           NOVO — budget temporalmente annealed (RRSI)
│   ├── critic.rs             NOVO — Laya avalia reusabilidade
│   ├── pruner.rs             NOVO — remove mudanças inefetivas
│   └── environment.rs        NOVO — Dream-RSI: mutar benchmark suite
├── sandbox/
│   ├── mod.rs                NOVO — Sandbox trait
│   ├── namespace.rs          NOVO — Linux namespaces por subagente
│   ├── cgroup.rs             NOVO — limites memória/CPU
│   └── breaker.rs            NOVO — circuit breaker collective
└── tools/
    ├── mod.rs                NOVO — Tool trait + registry
    ├── speculative.rs        NOVO — SpeculativeExecutor (Spec-PTC)
    └── reader.rs             NOVO — DelegatedReader (SoL-Pi)
```

---

## 3. Substituição de runtime: asupersync → Tokio

### Dependências

| Antes | Depois |
|---|---|
| `asupersync = "0.5.0"` | `tokio = { version = "1.48", features = ["rt-multi-thread", "macros", "sync", "time", "process"] }` |
| `asupersync::sync::Mutex` | `tokio::sync::Mutex` |
| `asupersync::runtime::RuntimeBuilder` | `#[tokio::main]` |
| `asupersync::runtime::reactor::create_reactor` | (interno do Tokio) |
| TLS via `tls-webpki-roots` / `tls-native-roots` | `rustls` via `reqwest` |
| `futures::executor::block_on` | `tokio::runtime::Runtime::block_on` |

### Riscos

| Risco | Mitigação |
|---|---|
| `asupersync` tem API que Tokio não tem (reactor custom) | Adaptar camada por camada, com `cargo check` a cada passo |
| `asupersync::sync::Mutex` pode ser non-Send | Trocar por `tokio::sync::Mutex` (Send por default) |
| TLS custom (auth.rs, http/) | Deletar `auth.rs`; `http/` usa `reqwest` com rustls |

---

## 4. Sequência de migração (10 semanas)

| Semana | Tarefa | Critério de saída |
|---|---|---|
| **1** | Criar repo GitHub + push do fork | `git ls-remote` retorna `9f0799a` |
| **2** | Remover 2.3 (não essenciais) | `cargo check` passa com warnings apenas |
| **3** | asupersync → Tokio em `subagents/`, `memory/`, `worktree_iso/`, `session_control/` | `cargo check --lib` verde |
| **4** | asupersync → Tokio em `mcp/`, `extensions/`, `providers/` | `cargo check --lib` verde |
| **5** | Reduzir `providers/` a 3 (openai, anthropic, gemini) | `cargo build` verde |
| **6** | Escrever `event_store.rs` + `decision.rs` (do backup da Fase 0) | testes 9/9 passando |
| **7** | Escrever `agent.rs` novo + `rlm/` (fan-out) | 3 subagentes paralelo < 100ms |
| **8** | Escrever `memory/` (snapshot, query, compaction) | `replay_to()` funciona |
| **9** | Escrever `sandbox/` (namespace, cgroup, breaker) | subagente isolado confirmado |
| **10** | Escrever `refine/` (proposer, critic, pruner, environment) | refinement loop roda |

---

## 5. Riscos do fork

| Risco | Severidade | Mitigação |
|---|---|---|
| **Dívida técnica herdada** — bugs do pi que nunca vamos achar | Alta | Módulos reescritos (2.2) elimina ~60% do código herdado |
| **Divergência do upstream** — pi continua evoluindo | Média | Manter branch `upstream` + rebase mensal (ou nunca — fork definitivo) |
| **asupersync → Tokio é invasivo** | Média | Fazer módulo a módulo, com testes |
| **Licença** — MIT, ok | Baixa | Manter `LICENSE` original + atribuição no README |
| **macOS vs Linux namespaces** | Média | Namespaces só funcionam em Linux. Desenvolvimento em Linux VM (Railway) |

---

## 6. Critérios de qualidade (o que define "feito")

| Critério | Métrica | Alvo |
|---|---|---|
| Build limpo | `cargo clippy -- -D warnings` | 0 warnings |
| Testes | `cargo test` | > 90% coverage nos módulos Ayrola |
| Spawn latency | benchmark de `rlm/spawn.rs` | < 100ms p50, < 200ms p99 |
| Decision latency | benchmark de `decision.rs` | < 50ms (Laya ONNX) |
| Isolamento | teste de namespace | subagente não vê filesystem do pai |
| Refinement | benchmark de `refine/` | pruner remove > 40% dos candidatos inefetivos |
| Tamanho do binário | `cargo build --release` | < 15 MB |

---

## 7. Comandos do dia-a-dia

```bash
# Rodar
cargo run -- status
cargo run -- decide --qtype yesno --question "..."
cargo run -- replay --last 10

# Testar
cargo test
cargo clippy -- -D warnings
cargo bench

# Verificar qualidade
cargo check --all-targets
```

---

## 8. AÇÃO IMEDIATA NECESSÁRIA

O repositório GitHub `AbsonDev/ayrola-kernel` **ainda não existe**. O fork está commitado
localmente em `~/ayrola-kernel` com o remote já configurado.

**Para criar o repo e fazer o push:**

```bash
cd ~/ayrola-kernel
gh repo create AbsonDev/ayrola-kernel --public --source=. --push   --description "Ayrola Harness — Rust kernel for recursive agent harnesses (fork of pi_agent_rust)"
```

Depois disso, `git ls-remote origin` deve retornar o commit `9f0799a`.

---

## 9. Referências

| Documento | Conteúdo |
|---|---|
| `RESEARCH-PATTERNS.md` | 13 papers → 4 pilares |
| `PLANO_IMPLEMENTACAO.md` | Decisões de código por paper |
| `ESTADO_DA_ARTE.md` | 55 papers descobertos via `orx` |
| `DECISOES.md` | 8 decisões fechadas |
| `MANIFESTO.md` | Visão e filosofia |
| `PROPOSTA.md` | Roadmap original |
