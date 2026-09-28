# Escolha da Linguagem do Runtime

Decisao critica: Python, Rust, TypeScript ou Go? Analise com dados de 2025-2026.

---

## Decisao Rapida (TL;DR)

| Se voce quer... | Use | Por que |
|---|---|---|
| **Rapido para validar / MVP** | Python | Ecossistema AI maduro, LangChain/LlamaIndex, MCP SDK oficial |
| **Performance extrema / producao** | Rust | Zero-cost, sem GC, 97x mais rapido que Python, binario single-file |
| **Serverless / Next.js / frontend team** | TypeScript | Type safety, Node.js, deploy trivial, ecossistema web |
| **Alta concorrencia simples** | Go | Goroutines simples, GC baixo, balance entre performance e simplicidade |

---

## Benchmarks Diretos (2025-2026)

### CPU-bound

| Language | Tempo (n-body) | vs Rust |
|---|---|---|
| Rust | 3.2s | 1x (baseline) |
| Python | 312.4s | **97x mais lento** |

### Concorrencia (microservice HTTP, RPS)

| Language | RPS relativo | Memoria | GC pause (p99) |
|---|---|---|---|
| Rust | 100% (baseline) | ~5-15MB | 0ms (sem GC) |
| Go | ~90% | ~20-40MB | ~0.1-1ms |
| TypeScript (Node.js) | ~60-70% | ~50-100MB | ~1-10ms |
| Python | ~25-33% | ~100-300MB | ~1-50ms (nao-deterministico) |

### Startup (cold start)

| Language | Startup |
|---|---|
| Rust | ~1ms (binario) |
| Go | ~10ms |
| Node.js | ~100-200ms |
| Python | ~500-2000ms (interpreter + imports) |

**Impacto para agent harness:** Se seu agente faz spawn de subagentes, Python sofre **500ms-2s de overhead por spawn**.

---

## AI Agent Ecosystem (2026)

### Python -- o dominio atual

| Framework | Maturidade | Nota |
|---|---|---|
| LangChain | Producao | 37k+ stars |
| LlamaIndex | Producao | 18k+ stars |
| OpenAI Agents SDK | Producao | 26.9k+ stars |
| MCP SDK | Oficial | Python sim |
| PyTorch | Dominante | Python-only |
| HuggingFace | Dominante | Python-first |

**Pros:** Tudo esta aqui. Qualquer paper de RL/LLM tem codigo em Python.
**Contras:** GIL (mesmo com free-threading, 51% compatibilidade de pacotes), startup lento.

**Python 3.14 free-threading (sem GIL):**
- Production-ready (2026)
- 10x CPU speedup em workloads multi-threaded
- 51% package compatibility (ainda nao 100%)
- Django supportado

### TypeScript -- para web teams

| Framework | Maturidade | Nota |
|---|---|---|
| OpenAI Agents SDK | Producao | Python + TS |
| Mastra | Crescendo | TypeScript-first |
| MCP SDK | Oficial | TypeScript sim |
| Vercel AI SDK | Producao | v4 (2025) |

**Pros:** Serverless, type safety, Next.js integration.
**Contras:** Ecossistema AI menos maduro que Python, Node.js GC.

### Rust -- performance, mas ecossistema jovem

| Framework | Maturidade | Nota |
|---|---|---|
| Rig | Beta | Early stage |
| AutoAgents | Beta | < 1k stars |
| OpenFANG | Alpha | Early stage |
| Prism MCP SDK | v0.1.0 | Muito recente |

**Pros:** Zero-cost abstraction, sem GC, seguranca de memoria, performance 10-100x.
**Contras:** Curva de aprendizado ingreme, ecossistema AI ainda em formacao, borrow checker.

### Go -- concorrencia simples

| Framework | Maturidade | Nota |
|---|---|---|
| Go-LLM | Producao | GitHub: cohosted/go-llm |
| LangChain Go | Producao | Port oficial |
| MCP SDK | Oficial | Go sim |

**Pros:** Goroutines simples, GC eficiente, deploy trivial.
**Contras:** Menos popular para AI, ecossistema mais limitado.

---

## Casos Reais em Producao (2025-2026)

| Harness | Linguagem | Por que? |
|---|---|---|
| GitHub Copilot CLI runtime | **Rust** | Migrou 800k linhas de TS para Rust (Q2 2026). Performance, memory safety. |
| Claude Code | TypeScript | CLI simples, rapid iteration, MCP nativo |
| OpenCode | TypeScript | Open-source, facil para comunidade contribuir |
| Cursor | TypeScript | IDE integration, Electron-based |
| Prime Agent | Python | Kernel IPython persistente, ecossistema AI |
| Aeon | TypeScript | GitHub Actions, serverless-friendly |
| pi_agent_rust | Rust | Experimental, zero-unsafe, performance |
| AutoAgents | Rust | Experimental, production-grade, type safety |

---

## Recomendacao Especifica para Ayrola Harness

### Estrategia "Two-Tier" (recomendada)

```
CAMADA 1 -- Harness Definition (orquestracao, planejamento)
  -> Python
  -> Porque: LangChain, LlamaIndex, MCP SDK, PyTorch -- tudo aqui
  -> Prototipagem rapida, validacao de conceito

CAMADA 2 -- Kernel / Runtime (execucao, I/O, concorrencia)
  -> Rust (longo prazo) ou Go (curto prazo)
  -> Porque: performance, sem GC, startup rapido
  -> Evita GIL, GC pauses, overhead de processos
```

### Justificativa

1. **Python para definicao:** Ninguem constroi agentes sem LangChain, LlamaIndex, HuggingFace. Python e o idioma universal da pesquisa AI. Comece aqui.

2. **Rust/Go para runtime:** Quando voce fizer spawn de 10 subagentes simultaneamente, Python paga 5s-20s de overhead de startup. Rust paga ~10ms.

3. **TypeScript como alternativa:** Se sua equipe for 100% web/frontend, TypeScript e razoavel. O Prime Agent e OpenCode sao TS, e funcionam bem.

4. **Nao use Rust para tudo:** Ainda nao tem o ecossistema AI. Voce vai reescrever LangChain daqui pra frente.

### Timeline sugerido

| Fase | Linguagem | Justificativa |
|---|---|---|
| MVP (1-2 meses) | **Python** | Fastest path to validar RLM/harness concepts |
| Producao (6-12 meses) | **Python + Rust kernel** | Substituir hot paths e spawns por Rust |
| Scale (12+ meses) | **Rust primary** | Quando o ecossistema madurar |

---

## Decisao Final

**Use Python para comecar.** Mas arquitete o kernel separado (conkernel/clikernel pattern) para facilitar a migracao para Rust/Go depois.

**Nao faca Rust-first** a menos que voce tenha expertise avancada e time disposto a reescrever ecossistema.

**Nao faca TypeScript-first** a menos que sua equipe seja 100% web.

---

## Fontes

- Benchmark CPU: https://tech-insider.org/python-vs-rust-2026
- Benchmark concurrency: https://blog.stackademic.com/go-vs-rust-vs-python
- GitHub Copilot Rust migration: https://github.blog/ai-and-ml/generative-ai/migrating-the-github-copilot-runtime-to-rust
- Rust AI ecosystem: https://zylos.ai/research/2026-04-01-rust-native-ai-agent-frameworks-ecosystem-2026/
- Python free-threading: https://docs.python.org/3/howto/free-threading-python.html
- AutoAgents Rust benchmark: https://dev.to/saivishwak/benchmarking-ai-agent-frameworks-in-2026
