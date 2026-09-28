# PROPOSTA — O Que Construir

Escopo, features, roadmap e métricas de sucesso do Ayrola Harness.

---

## 1. Escopo do MVP

### Incluído (v1 — 6 meses)
- **Kernel Rust puro** (Tokio + event store) — sem Python, sem GIL
- **Laya ONNX embarcado desde o dia 1** (via `ort` crate) — $0, 33ms, offline
- RLM engine (depth-bounded, 3 níveis)
- Memória event-sourced com time-travel
- Auto-melhoria nível 2 (skill refinement)
- Terminal-first interface
- MCP plugin system

### Fora do MVP (v2+)
- Auto-melhoria nível 3 (self-modification)
- Sandbox-per-agent (namespaces)
- Dashboard web
- Multi-canal (Discord, Slack, WhatsApp)
- Fine-tuning Laya customizado

---

## 2. Fases do Roadmap

### Fase 0: Fundação Rust (1-2 meses)
- `cargo init --name ayrola-kernel`
- Tokio event loop básico
- Event store append-only (memmap)
- **Laya ONNX decision layer integrada desde o dia 1** (via `ort` crate)
- CLI mínima

**Entregável:** Kernel Rust que aceita input, consulta Laya (33ms, $0, offline), persiste eventos.

**Princípio da Fase 0:** Nada de Python. Nada de HTTP para decisões. O kernel é Rust puro com Laya embarcado — a decisão NÃO via SaaS, pergunta Laya local.

### Fase 1: RLM Engine (3-5 meses)
- Decomposição recursiva de tarefas
- Spawn de subagentes (<100ms)
- Agregação de resultados
- Decision layer: ensemble cache → pre-filter → LLM (Laya candidato tier 2)

**Entregável:** Agente que decompõe tarefas complexas em paralelo.

### Fase 2: Memória Event-Sourced (6-7 meses)
- Replay de sessões completas
- Fork de timelines
- Time-travel debugger
- Snapshots periódicos
- Busca semântica sobre histórico

**Entregável:** Debug de qualquer decisão que o agente tomou.

### Fase 3: Auto-Melhoria Nível 2 (8-11 meses)
- Análise de trajetórias
- Proposta de refinamento de skills
- Validação (Laya + cargo check)
- Hot-reload de prompts/skills

**Entregável:** Agente melhora seus prompts e skills automaticamente.

### Fase 4: Sandbox-per-Agent (12-13 meses)
- Linux namespaces via `nix` crate
- Cgroups para resource limits
- Seccomp para syscall filtering
- Landlock para filesystem

**Entregável:** Isolamento real por subagente, 1-10ms overhead.

### Fase 5: Auto-Melhoria Nível 3 (14-18 meses)
- Patch no código Rust do próprio harness
- Validação: ensemble decision layer (segurança) + cargo check (tipo) + golden set (comportamento)
- Hot-reload via `libloading`
- Loop completo: detecta → propõe → valida → aplica

**Entregável:** Agente reescreve o próprio código.

---

## 3. Features Detalhadas

### 3.1 RLM Engine
- Decomposição: LLM decide quebrar tarefa em subtarefas
- Spawn: Tokio `task::spawn` com 10-50ms de latência (não processo — 10-20x mais rápido que Prime Agent)
- Depth-bounded: default 3, configurável
- Agregação: resultados estruturados recombinados

### 3.2 Memória Event-Sourced
- Log append-only de todas as ações
- Snapshots periódicos (checkpoint)
- Replay: reexecuta sessão inteira
- Fork: cria branch de timeline
- Time-travel: volta a qualquer ponto

### 3.3 Decision Layer (Ensemble 3 tiers)
- Tier 0: Cache semântico (0ms, 0 custo)
- Tier 1: Classificador ONNX pequeno (~5ms, pre-filter)
- Tier 2: LLM completo (Laya/Jev/outro) só quando necessário
- Perguntas tipadas: choice, score, yes/no por decisão
- Fine-tuning customizado (v2)
- Usos: routing, safety gates, spawn decisions

### 3.4 Auto-Melhoria
- **Nível 2:** Analisa padrões de erro → propõe refinamento
- **Nível 3:** Detecta falha recorrente → patch no código
- Validação em 2 camadas: Laya (lógica) + cargo check (tipo)

### 3.5 Sandbox-per-Agent
- Namespaces: 1-10ms vs 50-100ms Docker
- Isolamento: filesystem, network, process
- Resource limits via cgroups
- Zero overhead para subagentes

### 3.6 Plugin System (MCP)
- Padrão MCP para tools
- Hot reload de plugins
- Marketplace próprio (v2)

---

## 4. Métricas de Sucesso

### Performance
- [ ] Sub-100ms subagent spawn (meta: 10-50ms)
- [ ] <1ms event store append
- [ ] <50ms decision layer call
- [ ] <10ms sandbox creation

### Capacidade
- [ ] RLM com 3 níveis de recursão
- [ ] 10+ subagentes em paralelo
- [ ] Replay de sessão de 1000 turnos
- [ ] Time-travel em <1s

### Auto-Melhoria
- [ ] Nível 2 reduz erros em 20% (mês 6)
- [ ] Nível 3 aplica 1 patch/semana com sucesso (mês 12)

### Qualidade
- [ ] 95% de cobertura de testes
- [ ] 0 vulnerabilidades de segurança (audit cargo)
- [ ] 0 leaks de memória (valgrind/miri)

---

## 5. O Que NÃO Fazer

| ❌ Evitar | Motivo |
|---|---|
| IDE própria | Terminal-first é o padrão. IDE é outro produto. |
| Sandbox próprio (Docker-like completo) | Use namespaces, não reimplemente containers. |
| 20+ modelos | Complexidade sem retorno. 3-5 modelos suficientes. |
| Python-first / LangChain / FFI | Performance limitante + GIL mata paralelismo. Rust puro resolve. |
| Adoção massiva precoce | Construa sólido primeiro. Cresça depois. |

---

## 6. Dependências Técnicas

### Rust
- `tokio` — async runtime
- `ort` — ONNX Runtime (Laya)
- `nix` — Linux namespaces (sandbox)
- `libloading` — hot-reload (self-modification)
- `serde` — serialização
- `memmap2` — event store em memória
- `clap` — CLI

### Infraestrutura
- 9Router local (fallback LLM)
- ONNX Runtime (CPU ou GPU)
- Qdrant local (memória semântica, v2)

---

## 7. Métricas de Adoption

### Ano 1 (v1 estável)
- 100 stars
- 10 contributors
- 1-2 production use cases

### Ano 2
- 1000 stars
- 50 contributors
- 10+ production use cases
- Nível 3 validado em produção

---

*Proposta v1.0 — 2026-09-27. Ajustar após feedback da comunidade.*
