# RESEARCH-PATTERNS.md

> **Foundational concept:** `2609.language-model-shape` (Alex Zhang) — "Design language models around harnesses, not the other way around." Laya is an instance of this shape. Read this FIRST.

---



Mapeamento cruzado: papers × pilares Ayrola × implementação no kernel.

Gerado: 2026-09-28 | Fonte: orx discover embedding + alphaXiv | 55 papers únicos

## Pilares do Ayrola

- **Pilar 1** — Memória event-sourced + time-travel
- **Pilar 2** — Subagentes <100ms
- **Pilar 3** — Auto-melhoria nível 3
- **Pilar 4** — Sandbox-per-agent via Linux namespaces

## Papers mapeados para pilares

| arXiv | Título | Votos | Pilares |
|---|---|---|---|
| `2608.23552` | Prime Agent: A Self-Improving RLM Harness | 357⭐ | Pilar 2, Pilar 3 |
| `2609.11873` | The Last AI Built by Humans: Toward Genuine Recurs | 305⭐ | Pilar 3 |
| `2609.14858` | Dream-RSI: Recursive Self-Improvement through Evol | 244⭐ | Pilar 3 |
| `2609.24972` | RRSI: Regularized Recursive Self-Improvement of Ag | 218⭐ | Pilar 2, Pilar 3 |
| `2608.spec-ptc` | Speculative Programmatic Tool Calling | 190⭐ | Pilar 2 |
| `2609.20519` | SoL-Pi: Recursively Scaling Auto-Research Loops fo | 181⭐ | Pilar 2, Pilar 3 |
| `2609.15364` | RSIAgent: Autonomous Exploration for Recursive Sel | 130⭐ | Pilar 3 |
| `2608.16157` | FreeToken: Efficient Edge-Native MoE Serving with  | 105⭐ | Pilar 4 |
| `2608.24876` | Recursive Experiential-Working Memory Evolution fo | 85⭐ | Pilar 1 |
| `2605.06639` | Recursive Agent Optimization | 81⭐ | Pilar 2 |
| `2609.08183` | NeoHorse-1: Towards Recursive Self-Improvement via | 71⭐ | Pilar 3 |
| `2609.24274` | vla.simd: Efficient CPU Inference for Language-Con | 18⭐ | Pilar 4 |
| `2607.27773` | ChronoMem: Version Control and Semantic Rollback f | 11⭐ | Pilar 1 |
| `2609.00595` | SoK: When Safe Agents Fail Together: The Security  | 10⭐ | Pilar 4 |
| `2607.12406` | Isolation as a First-Class Principle for LLM-Agent | 8⭐ | Pilar 4 |
| `2609.27279` | EnSIMem: Entity-Structured Indexing for Long-Term  | 7⭐ | Pilar 1 |
| `2608.06745` | MemPrism: Task-Conditioned Relational Memory Views | 6⭐ | Pilar 1 |
| `2605.24486` | AgentFugue: Agent Scaling for Long-Horizon Tasks t | 6⭐ | Pilar 2 |

## Detalhamento por pilar

### Pilar 1

#### Recursive Experiential-Working Memory Evolution for Long-Horizon Agent Harnesses (85⭐)
- arXiv: `2608.24876`
- Publicação: 2026-08-25

Recursive self-improvement (RSI) remains hard in long-horizon tasks, where growing histories obscure the task state and misalign skill invocation. We introduce Recuris, a recursive Experiential-Working Memory architecture for long-horizon agent harnesses, in which Working Memory tracks task progress and guides skill selection from Experiential Memory, grounding skill use in current needs rather th...

#### ChronoMem: Version Control and Semantic Rollback for Large Language Model Agent Memory (11⭐)
- arXiv: `2607.27773`
- Publicação: 2026-07-30

LLM agents increasingly rely on long-term memory to support multi-session interaction and personalization. However, existing agent memory systems are designed around forward-only evolution, continuously accumulating, consolidating, and overwriting knowledge, with no principled mechanism to inspect, version, or revert prior states. This makes agents brittle under corrections, concept drift, and mem...

#### EnSIMem: Entity-Structured Indexing for Long-Term Agent Memory (7⭐)
- arXiv: `2609.27279`
- Publicação: 2026-09-23

An agent that interacts with users over long periods must recall facts, preferences, events, and changes from a continuously growing interaction history. Existing memory systems often compress interactions into generic summaries or retrieve anonymous text chunks, making it difficult for an agent to identify the correct entity, property, and supporting evidence. We present EnSIMem, an entity-struct...

#### MemPrism: Task-Conditioned Relational Memory Views for Long-Horizon Agents (6⭐)
- arXiv: `2608.06745`
- Publicação: 2026-08-07

Long-horizon agents rely on memory to reuse experiences, yet existing memory systems often assume that evidence can be directly consumed through a fixed representation. This leads to representation mismatch, where relevant information is available but not organized for the current decision. To this end, we propose MemPrism, a task-conditioned relational memory framework that separates persistent e...

### Pilar 2

#### Prime Agent: A Self-Improving RLM Harness (357⭐)
- arXiv: `2608.23552`
- Publicação: 2026-08-24

Language models are sequential processors, but long-horizon agency requires external information and computation beyond model weights and active context. Prime Agent is an open-source harness for long-horizon evaluation and coding-agent workflows. A persistent IPython REPL follows the Recursive Language Model abstraction for programmatic context processing and test-time compute, while Continual Ha...

#### RRSI: Regularized Recursive Self-Improvement of Agent Harnesses (218⭐)
- arXiv: `2609.24972`
- Publicação: 2026-09-21

An LLM agent's capability is largely magnified by its harness, namely the prompts, control flow, tooling, memory, and context management surrounding the frozen backbone model. Recent methods increasingly automate this process by iteratively proposing and selecting component-wise edits of an agent harness, practically establishing a form of recursive self-improvement (RSI) at the agent-system level...

#### Speculative Programmatic Tool Calling (190⭐)
- arXiv: `2608.spec-ptc`
- Publicação: 2026-08-24

Speculative programmatic tool calling pre-launches tool calls parsed out of partially generated REPL code, overlapping high-latency sub-agent and sub-LLM calls with the harness's own token generation....

#### SoL-Pi: Recursively Scaling Auto-Research Loops for Efficient Agent Harness (181⭐)
- arXiv: `2609.20519`
- Publicação: 2026-09-17

As coding agents move from supervised code completion to unattended, around-the-clock exploration, their work expands from isolated predictions into long trajectories of reasoning, tool use, and feedback. Token efficiency therefore becomes important for scaling recursive self-improvement. We take an RSI-inspired approach at the harness layer, scaling auto-research loops across increasingly numerou...

#### Recursive Agent Optimization (81⭐)
- arXiv: `2605.06639`
- Publicação: 2026-05-07

We introduce Recursive Agent Optimization (RAO), a reinforcement learning approach for training recursive agents: agents that can spawn and delegate sub-tasks to new instantiations of themselves recursively. Recursive agents implement an inference-time scaling algorithm that naturally allows agents to scale to longer contexts and generalize to more difficult problems via divide-and-conquer. RAO pr...

#### AgentFugue: Agent Scaling for Long-Horizon Tasks through Collective Reasoning (6⭐)
- arXiv: `2605.24486`
- Publicação: 2026-05-23

Recent progress on long-horizon agentic tasks has been driven largely by scaling up individual agents through stronger models, better tools, and more effective scaffolding. In contrast, much less is understood about scaling out: whether multiple peer agents, all targeting the same task, can become an additional source of capability without relying on explicit role specialization or workflow orches...

### Pilar 3

#### Prime Agent: A Self-Improving RLM Harness (357⭐)
- arXiv: `2608.23552`
- Publicação: 2026-08-24

Language models are sequential processors, but long-horizon agency requires external information and computation beyond model weights and active context. Prime Agent is an open-source harness for long-horizon evaluation and coding-agent workflows. A persistent IPython REPL follows the Recursive Language Model abstraction for programmatic context processing and test-time compute, while Continual Ha...

#### The Last AI Built by Humans: Toward Genuine Recursive Self-Improvement (305⭐)
- arXiv: `2609.11873`
- Publicação: 2026-09-10

Recursive self-improvement (RSI) enables AI systems to turn experience and feedback into persistent changes that improve both their capabilities and the process of future improvement. We first use the Headroom-Closed Index (HCI) to reveal the problems of existing LLMs, then introduce the RSI concept and its development roadmap: from improvement-execution autonomy, improvement-strategy autonomy, ex...

#### Dream-RSI: Recursive Self-Improvement through Evolving Worlds (244⭐)
- arXiv: `2609.14858`
- Publicação: 2026-09-14

Recursive self-improvement is becoming increasingly vital for autonomous AI agents, where progress hinges on discovering high-value solutions across complex domains. The driver of this process is effective exploration, however, managing and improving exploration strategies remains a major bottleneck. Current systems face a fundamental dilemma: fixed strategies fail to adapt as search spaces scale,...

#### RRSI: Regularized Recursive Self-Improvement of Agent Harnesses (218⭐)
- arXiv: `2609.24972`
- Publicação: 2026-09-21

An LLM agent's capability is largely magnified by its harness, namely the prompts, control flow, tooling, memory, and context management surrounding the frozen backbone model. Recent methods increasingly automate this process by iteratively proposing and selecting component-wise edits of an agent harness, practically establishing a form of recursive self-improvement (RSI) at the agent-system level...

#### SoL-Pi: Recursively Scaling Auto-Research Loops for Efficient Agent Harness (181⭐)
- arXiv: `2609.20519`
- Publicação: 2026-09-17

As coding agents move from supervised code completion to unattended, around-the-clock exploration, their work expands from isolated predictions into long trajectories of reasoning, tool use, and feedback. Token efficiency therefore becomes important for scaling recursive self-improvement. We take an RSI-inspired approach at the harness layer, scaling auto-research loops across increasingly numerou...

#### RSIAgent: Autonomous Exploration for Recursive Self-improvement in New Environments (130⭐)
- arXiv: `2609.15364`
- Publicação: 2026-09-14

Digital agents must often adapt to new environments whose interfaces, tools, and failure modes are not fully captured by pretrained models. We introduce \textbf{RSIAgent}, a training-free multi-agent framework for recursive self-improvement through autonomous memory construction. RSIAgent coordinates curriculum, actor, and verifier agents to continually explore the environment, validate outcomes, ...

#### NeoHorse-1: Towards Recursive Self-Improvement via Agentic Post-Training with Routing Harness (71⭐)
- arXiv: `2609.08183`
- Publicação: 2026-09-08

Recursive self-improvement (RSI) requires a concrete mechanism through which an AI system observes its capabilities and converts that evidence into the next round of learning. We present NeoHorse-1, a family of agent-native models developed to explore this path through agentic post-training. Our system combines a heterogeneous model pool with intelligent routing, recording the predicted capability...

### Pilar 4

#### FreeToken: Efficient Edge-Native MoE Serving with Bandwidth-Adaptive Execution (105⭐)
- arXiv: `2608.16157`
- Publicação: 2026-08-17

Frontier open-weight models are increasingly available, but serving them still largely assumes datacenter infrastructure. We present FreeToken, an edge-native MoE serving system that treats a personal machine not as a small GPU, but as a unified, elastic inference platform. FreeToken co-designs the full serving stack, including model layout and loading, expert residency, CPU--GPU execution, agenti...

#### vla.simd: Efficient CPU Inference for Language-Conditioned Manipulation (18⭐)
- arXiv: `2609.24274`
- Publicação: 2026-09-21

Deploying language-conditioned manipulation without a dedicated GPU requires efficient inference and action chunks that cover the delay between policy queries. We present this http URL, a CPU inference engine that combines shared SIMD micro-kernels, reusable computation, and target-specific optimization. We relate query latency and execution horizon to action availability under lagged and time-ali...

#### SoK: When Safe Agents Fail Together: The Security of Multi Agent LLM Systems (10⭐)
- arXiv: `2609.00595`
- Publicação: 2026-09-01

Safe agents can fail together. Multi-agent LLM systems (MAS) move information, state, decisions, and authority across principal boundaries, creating failures that local checks may miss. Without an execution-level view, a multi-agent setting can easily be mistaken for evidence of a genuinely multi-agent security effect. We thus systematize MAS security through an execution-centered analysis of 197 ...

#### Isolation as a First-Class Principle for LLM-Agent System Safety: Concepts, Taxonomy, Challenges and Future Directions (8⭐)
- arXiv: `2607.12406`
- Publicação: 2026-07-14

The capability of LLM agents to function as the ``brain'' of a system fundamentally expands the scope of analysis beyond a standalone model. Consequently, safety is no longer only about input--output content alignment. It also concerns system behavior and real-world execution outcomes. However, the current literature is fragmented across attack types, applications, and benchmarks. This makes it ha...
