# Bibliografia Curada — Links Diretos

Coleção de links organizados por categoria para referência rápida.

---

## Papers (arXiv)

| Título | Autor | Ano | Link | Código |
|---|---|---|---|---|
| Recursive Language Models | Alex Zhang | 2025 | [2512.24601](https://arxiv.org/abs/2512.24601) | [alexzhang13/rlm](https://github.com/alexzhang13/rlm) |
| Recursive Agent Harnesses | Elias Lumer | 2026 | [2606.13643](https://arxiv.org/abs/2606.13643) | — |
| Continual Harness | Seth Karten | 2026 | [2605.09998](https://arxiv.org/abs/2605.09998) | — |
| Self-Harness | H. Zhang | 2026 | [2606.09498](https://arxiv.org/abs/2606.09498) | [qzzqzzb/Self-Harness](https://github.com/qzzqzzb/Self-Harness) |
| RHI (Recursive Harness Self-Improvement) | — | 2026 | [2607.15524](https://www.alphaxiv.org/abs/2607.15524) | — |
| AgentCL (continual learning eval) | — | 2026 | [2606.02461](https://arxiv.org/html/2606.02461v1) | — |

---

## Posts Técnicos Fundamentais

| Título | Autor | Link |
|---|---|---|
| Harness Engineering for Self-Improvement | Lilian Weng (OpenAI) | [blog](https://lilianweng.github.io/posts/2026-07-04-harness/) |
| Recursive Language Models: the paradigm of 2026 | Prime Intellect | [blog](https://www.primeintellect.ai/blog/rlm) |
| Prime Agent: A self-improving RLM agent | Prime Intellect | [blog](https://www.primeintellect.ai/blog/prime-agent) |
| The harness is all you need (mostly) | GitHub Blog | [post](https://github.blog/ai-and-ml/github-copilot/the-harness-is-all-you-need-mostly/) |

---

## Repositórios para Clonar / Estudar

### Pioneiros
```bash
# Prime Agent (o seu harness atual)
git clone https://github.com/PrimeIntellect-ai/prime-agent

# DeepSeek Harness (plugin-first, 95k stars)
git clone https://github.com/deepseek-ai/deepseek-harness

# OpenClaw (multi-canal, #1 GitHub 2026)
git clone https://github.com/openclaw/openclaw

# OpenCode (terminal-first, MIT)
git clone https://github.com/anomalyco/opencode
```

### Runtime / Kernel
```bash
# Kernel persistente (biblioteca)
git clone https://github.com/AnswerDotAI/conkernel
git clone https://github.com/AnswerDotAI/clikernel

# Rust runtime
git clone https://github.com/Dicklesworthstone/pi_agent_rust
```

### Auto-melhoria
```bash
# Self-Harness implementation
git clone https://github.com/qzzqzzb/Self-Harness

# Reading list curada
git clone https://github.com/leezythu/Awesome-Harness-Self-Improvement

# 167 harnesses ranqueados + templates
git clone https://github.com/ryanalberts/best-of-Agent-Harnesses
```

### Orquestração
```bash
# Aeon (GitHub Actions autônomo)
git clone https://github.com/aeonfun/aeon
```

---

## Comunidade / Discussões

| Tópico | Link |
|---|---|
| Prime Agent discussion (r/LocalLLaMA) | [Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1vgnmny/) |
| Is PrimeAgent Legit? | [Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1vliidn/) |
| Self-evolving agent harnesses | [Reddit](https://www.reddit.com/r/AI_Agents/comments/1vtsfvp/) |
| What's the best agent harness right now? | [Reddit](https://www.reddit.com/r/AgentsOfAI/comments/1webbgl/) |
| Continual learning map (mid-2026) | [Reddit](https://www.reddit.com/r/artificial/comments/1u40uys/) |
| HN: Pi – minimal terminal coding harness | [HN](https://news.ycombinator.com/item?id=47143754) |
| HN: DeepSeek Harness developer preview | [HN](https://news.ycombinator.com/item?id=49285244) |
| HN: What is a harness? | [HN](https://news.ycombinator.com/item?id=49409092) |

---

## Ferramentas / Serviços Referenciados

| Ferramenta | O que é | Link |
|---|---|---|
| Jev / System One | Decision layer $0 | [typesafe.ai](https://typesafe.ai) |
| 9Router / OmniRoute | Model routing | [localhost:20128](http://localhost:20128) |
| MCP | Model Context Protocol | [spec](https://spec.modelcontextprotocol.io/) |
| Anthropic Sandbox Runtime | OS-level sandbox | [docs](https://github.com/anthropics/anthropic-sdk-python/tree/main/src/anthropic/sandbox) |
| E2B | MicroVM sandbox | [e2b.dev](https://e2b.dev) |
| Northflank | Cloud microVM | [northflank.com](https://northflank.com) |

---

## Benchmarks

| Benchmark | O que mede | Link |
|---|---|---|
| Oolong-Synthetic | Coding agent evaluation | Usado no RAH paper |
| SWE-bench | Real-world GitHub issues | [github.com/swe-bench](https://github.com/swe-bench) |
| AgentCL | Continual learning eval | [paper](https://arxiv.org/html/2606.02461v1) |
