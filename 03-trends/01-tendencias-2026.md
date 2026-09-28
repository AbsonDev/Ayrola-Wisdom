# 8 Tendências Emergentes (2025-2027)

Direções validadas por papers, repos e discussões comunitárias. Não é especulação — são movimentos com evidência.

---

<a id="harness-inteligente"></a>
## 1. O Harness como Camada de Inteligência

**O que mudou:** Até 2025, o harness era "tooling ao redor do modelo". A partir de 2026, a comunidade aceita que **o harness é tão importante quanto o modelo**.

**Evidências:**
- Paper Self-Harness: agente melhora o próprio harness
- Lilian Weng (OpenAI): "harness engineering, não model engineering"
- Hacker News: *"The harness is all you need (mostly)"* — viral em 2026
- Prime Agent: harness como produto de primeira classe

**Implicação para Ayrola:** O harness não é enfeite. É a principal superfície de otimização.

---

<a id="rlm"></a>
## 2. RLMs como Paradigma Padrão

**O estado:** RLM saiu de paper (Dez 2025) para produção (2026) em menos de 9 meses.

**Como funciona:**
```
Tarefa complexa
    ↓
LLM decide decompor
    ↓
Invoca a si mesmo recursivamente (subagentes)
    ↓
Agrega resultados
    ↓
Continua ou refina
```

**O que está por vir:**
- LLM decide *quando* spawnar subagentes (não o humano)
- Profundidade de recursão configurável
- Modelo como orquestrador de si mesmo

**Prova:** RAH paper: +9.6 pontos percentuais sobre Codex puro (71.75% → 81.36%)

**Quem adota:** Prime Agent (nativo), OpenCode (Zen lane), Aeon (multi-backend)

---

<a id="kernel-repl"></a>
## 3. Kernel REPL Persistente como Padrão

**O estado:** Saiu de novidade (Prime Agent) para biblioteca independente em meses.

**O que é:**
- Agente mantém estado entre turnos
- Variáveis, imports, resultados ficam disponíveis
- Tarefas longas não recomeçam do zero

**Prova:** `conkernel` e `clikernel` (AnswerDotAI, 2026) abstraíram o conceito como biblioteca plugável.

**Estado atual:**
| Harness | Kernel persistente |
|---|---|
| Prime Agent | ✅ IPython nativo |
| OpenCode | ✅ Shell session |
| Claude Code | ❌ Sandbox temporário |
| Cursor | ❌ Sem estado entre prompts |

**Implicação para Ayrola:** É o diferencial mais barato de implementar (vs RLM, que precisa de integração especial).

---

<a id="decisao-barata"></a>
## 4. Camada de Decisão Barata (Jev / System One)

**O achado:** Decisões simples (yes/no, roteamento, scoring) custam 100-200x mais em LLM do que deveriam.

**A solução:** Modelos de decisão especializados (Jev, System One) — ~400ms, ~$0/call.

**Casos de uso:**
- Roteamento de requisições
- Captura de intenção
- Triagem de erros
- Commit messages
- Code review básico
- Gating de comandos perigosos

**Quem usa:** Prime Agent (Jev nativo), OpenCode (Zen lane zero-cost)

**Referência:** TypeSafe AI — $40M seed (DCVC), adotado por Vercel e Cloudflare (Forbes, Set 2026)

**Implicação para Ayrola:** Se seu agente roda 100 decisões por sessão, 95 delas podem ser $0.

---

<a id="sandbox"></a>
## 5. Sandbox Real como Default

**O estado:** Todo coding agent de 2026 roda em sandbox. A questão agora é *qual* sandbox.

**Opções:**
| Sandbox | Tipo | Isolamento | Custo |
|---|---|---|---|
| Anthropic srt | OS-level | Seatbelt/bubblewrap | Free |
| Docker | Container | Namespaces | Baixo |
| E2B | MicroVM | Hypervisor | Médio |
| Northflank | MicroVM | Cloud gerenciado | Alto |
| Qovery | MicroVM | Cloud gerenciado | Alto |

**Tendência:** Sandbox-as-a-Service está virando commodity. O diferencial volta para o harness.

**Implicação para Ayrola:** Não invente sandbox. Use um existente e foque no harness.

---

<a id="runtime"></a>
## 6. Runtime Diversificação (Rust/Go vs Python)

**Sinalizações:**
- GitHub Copilot migrou 800k+ linhas para Rust (Q2 2026)
- Pi Agent portado para Rust
- Mastra cresce como TypeScript-first
- Go debate como runtime de agentes

**Tese:** Python domina hoje (caminho mais fácil). Em 2027, agentes de produção rodam em Rust/Go/TS por performance — Python fica na camada de *definição de harness*, não runtime.

**Estratégia para Ayrola:**
- Curto prazo: Python (prototipagem rápida, ecossistema maduro)
- Longo prazo: Kernel em Rust, harness definition em Python/TS

---

<a id="auto-melhoria"></a>
## 7. Auto-Melhoria Estrutural

**Níveis de auto-melhoria:**

| Nível | O que é | Exemplo | Status |
|---|---|---|---|
| 1 — Prompt optimization | Melhora o prompt | DSPy, prompt optimizer | Produção |
| 2 — Skill/harness refinement | Melhora skills, memórias, rotas | Prime Agent `/refine` | Produção |
| 3 — Harness self-modification | Agente modifica código do harness | Self-Harness paper | Pesquisa |
| 4 — Continual learning | Aprende sem catástrofe | AgentCL, lifelong papers | Pesquisa |

**Onde estamos:** Nível 2 é produção. Nível 3 é pesquisa (paper validado, mas precisa validação em produção).

**Implicação para Ayrola:** Comece no nível 2 (é o que funciona e é comprovado). Nível 3 é diferencial de pesquisa.

---

<a id="multiagent"></a>
## 8. Multi-Agent como Default

**O estado:** Tarefas simples = agente único. Tarefas complexas = múltiplos agentes por padrão.

**Mudanças:**
- Spawning de subagentes virou primitiva nativa (não workaround)
- Profundidade recursiva configurável
- Orquestração automática (modelo decide quem spawnar)
- A2A messaging como padrão

**Adoção rápida:** Claude Code, Prime Agent, OpenHands, Aeon

**Implicação para Ayrola:** Se não suportar subagentes nativos, vai ficar para trás em 6-12 meses.

---

## Resumo Visual

```
2025 ──────────────────────────────────────────── 2027

Dominante:        Modelo-centric     ──────────►  Harness-centric
Paradigma:        Agent + tools      ──────────►  RLM + Continual Harness
Runtime:          Python             ──────────►  Rust/Go/TS (kernel)
Decisões:         LLM para tudo      ──────────►  Jev/System One para 90%
Estrutura:        Monolithic         ──────────►  Multi-agent default
Auto-melhoria:    Prompt tweak       ──────────►  Harness restructure
Sandbox:          Premium feature    ──────────►  Commodity
```

---

## 5 Tendências que valem a pena apostar

Se tivesse que apostar em 5, seriam estas:

1. **RLM** — vai ser o padrão de 2026
2. **Kernel REPL persistente** — diferencial barato, alto impacto
3. **Decisão barata (Jev)** — ROI obvious, todo agente precisa
4. **Multi-agent** — inevitável, sem isso fica para trás
5. **Auto-melhoria nível 2** (`/refine`) — diferencia de forma mensurável

**Evitar apostar em:**
- Sandbox próprio (commodity)
- Runtime próprio cedo (muito investimento)
- Nível 3 de auto-melhoria como default (pesquisa, não produção)
