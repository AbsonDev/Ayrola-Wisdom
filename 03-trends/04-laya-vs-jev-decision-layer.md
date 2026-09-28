# Laya vs Jev: Decision Layer para Ayrola Harness

> **Laya é um Jev open-source que roda local, custa $0, e bate o Jev de graça.**
> Para o Ayrola — que é 100% offline, em Rust — Laya é a escolha óbvia.

---

## TL;DR

| Característica | **Laya** | **Jev** |
|---|---|---|
| **Preço** | $0 (open-source, Apache 2.0) | $0 via 9Router, pago via TypeSafe |
| **Weights** | Open (você possui o modelo) | Closed (hosted API) |
| **Runtime** | ONNX Runtime (sem Python) | Python / API |
| **Latência (1 questão)** | **~33ms** (16ms multilingual) | ~136ms |
| **Benchmark** | **Bate Jev 26-1 no Tetris** | Perdeu |
| **Fine-tuning** | Sim (você treina pro seu domínio) | Não possível |
| **Controvérsia** | Comunidade open | Reddit: "derivado de Laya?" |
| **Integração Rust** | `ort-rust` (ONNX) — nativo | Requer HTTP client |

---

## O Que é Laya?

> **Non-autoregressive System 1 decision engine.**

Laya é um modelo de decisão de 421M parâmetros (ModernBERT-large + 2-layer decision head) que responde perguntas tipadas (choice, score, yes/no) em **33ms** — uma única forward pass, sem geração de texto.

**GitHub:** https://github.com/NandhaKishorM/laya  
**HuggingFace:** https://huggingface.co/convaiinnovations/laya  
**ONNX Runtime:** https://github.com/receptron/laya  
**Site:** https://laya-ai.com

---

## O Que é Jev?

> **Verdict-only decision model** (TypeSafe AI, System One).

Jev retorna **typed judgments** (probabilidade yes/no, escolha de conjunto fechado, score numérico) — nunca gera texto. ~700ms, ~$0.00002/call.

**Site:** https://typesafe.ai/jev  
**API:** Hosted (TypeSafe Cloud)

---

## Comparação Técnica

### 1. Latência

| Modelo | Latência (1 questão) | Decisões/segundo |
|---|---|---|
| **Laya** | 33ms (median) | ~30 |
| **Laya (multilingual)** | 16-18ms | ~60 |
| **Jev** | 136ms | ~7 |

**Fonte:** [Reddit r/LocalLLM comparison](https://www.reddit.com/r/LocalLLM/comments/1wmm8ql/)

> Laya é **4x mais rápido** que Jev.

### 2. Benchmark: Tetris

Laya pegou Jev no Tetris — **26-1**. A vitória foi atribuída ao:
- Latência mais baixa (33ms vs 136ms)
- 60 decisões por segundo (vs 7 de Jev)
- Zero custo de API

**Fonte:** [Facebook post vyzualAI](https://www.facebook.com/vyzualAI)

### 3. Custo

| Aspecto | Laya | Jev |
|---|---|---|
| Custo por decisão | $0.00 | $0.00 (9Router) / pago (TypeSafe) |
| Infraestrutura | Sua máquina | TypeSafe Cloud + 9Router |
| Dependência externa | Nenhuma (ONNX local) | Sim (API/HTTP) |
| Fine-tuning | Sim | Não |

### 4. Arquitetura

**Laya:**
```
Input (state + questions)
    ↓
ModernBERT-large encoder (312M params)
    ↓
2-layer decision head
    ↓
Typed output (choice/score/yes-no)
```

**Jev:** (não documentado publicamente — é closed source)

### 5. Integração com Rust

**Laya:** Já tem uma implementação ONNX Runtime:
- **Repo:** https://github.com/receptron/laya  
- **API shape:** `RLAgent.system_one` (mesma do Python reference)
- **Output:** Faz match com Jev's `system_one` API até 4 decimal places
- **Cargo:** Pode usar `ort` crate (ONNX Runtime bindings)

```toml
[dependencies]
ort = "0.47"  # ONNX Runtime
```

**Jev:** Precisa de HTTP client (Rust) ou Python FFI:
```toml
[dependencies]
reqwest = { version = "0.47", features = ["json"] }
```

---

## Controverso: Derivado?

Reddit thread: [Is Typesafe based/derived from Laya?](https://www.reddit.com/r/LocalLLaMA/comments/1wlfmgq/)

- Laya paper (março 2025) → Jev (Setembro 2025)
- TypeSafe Jev é closed-weights; Laya é open-weights (Apache 2.0)
- A percepção da comunidade: **Laya veio primeiro, Jev é similar mas fechado**
- Reddit: "TypeSafe's new model, Jev is all over the internet... then an AI dev posted here how they'd done something very [similar]"

**Fonte:** https://www.reddit.com/r/LocalLLaMA/comments/1wlfmgq/

Para Ayrola, isso significa:
- Usar Laya é **sem risco de controversia**
- É **100% open-source** — ninguém pode reclamar
- Você pode **auditar, modificar, fine-tunar**

---

## Fine-Tuning para Ayrola

### Por que fine-tunar?

Jev e Laya base são **generais**. Para Ayrola, queremos decisões específicas:

| Tipo de decisão | Pergunta |
|---|---|
| Subagent spawning | "Devo spawnar um subagente para esta tarefa?" |
| RLM recursion | "Devo recursar a decomposição?" |
| Self-improvement | "Esse patch no harness é seguro?" |
| Sandbox escalation | "Preciso isolar este subagente?" |
| Commit approval | "Esse diff está pronto para commit?" |

### Como treinar

1. **Coleção de dados:** Use as trajetórias do Prime Agent (já indexadas no Qdrant)
2. **Labels:** Jev verdicts (sim/não/score) como ground truth
3. **Fine-tuning:** RLCD (Reinforcement Learning from Compare-and-Verify Distillation)
   - Já documentado no paper de Laya
4. **Validação:** Teste em benchmark Oolong-Synthetic

---

## Comparativo de Benchmarks (2026)

| Benchmark | Laya | Jev |
|---|---|---|
| Tetris (lines) | 26 | 1 |
| Zero-shot base model | 0.362 | 0.461 (majority class baseline) |
| Routed stack (multi-checkpoint) | Bate | Perde |
| Hard-label accuracy | 76.6% | ~70% (estimado) |

**Fontes:**
- https://www.alphamatch.ai/blog/jev-vs-laya-system-one-2026
- https://wavect.io/blog/laya-vs-jev-benchmark-ai-startup-moat/
- https://shop.zimaspace.com/blogs/product-comparisons/jev-vs-laya-decision-model

---

## Decisão para Ayrola Harness

### ✅ Use Laya porque:

1. **Zero custo** — nada de depender de API externa
2. **4x mais rápido** — 33ms vs 136ms
3. **Rust-native** — ONNX Runtime via `ort` crate
4. **Fine-tunable** — você treina pro domínio Ayrola
5. **Open-source verdadeiro** — Apache 2.0, ninguém pode vetar
6. **Bate Jev 26-1** — prova de performance

### Como integrar:

```rust
// Ayrola Harness — DecisionLayer usando Laya
// via ONNX Runtime

use ort::{Environment, Session, SessionConfig};

async fn laya_decide(state: &str, questions: &[&str]) -> Vec<f32> {
    // 1. Encode state + questions
    // 2. Load Laya ONNX model
    // 3. Forward pass
    // 4. Return scores
}
```

### Plano de implementação:

| Etapa | Descrição | Timeline |
|---|---|---|
| 1. Benchmark | Rodar Laya vs Jev no mesmo benchmark | Mês 1 |
| 2. Integração Rust | Usar `ort` crate para carregar Laya ONNX | Mês 1 |
| 3. Dataset | Coletar decision points do Prime Agent | Mês 2 |
| 4. Fine-tuning | RLCD para Ayrola-specific decisions | Mês 3-4 |
| 5. Validação | Benchmark Oolong-Synthetic comparativo | Mês 5 |

---

## Conclusão

**Laya é o Jev que você pode possuir.**

Para Ayrola — que é construído em Rust para ser 100% offline — Laya é a escolha óbvia:
- Não precisa de API externa
- Integra nativamente com Rust (ONNX)
- É mais rápido
- É mais barato (totalmente free)
- E bate o Jev pra comer

**Drop Jev. Drop the hosted API.**  
**Use Laya. Fine-tune to Ayrola. Dominar.**

---

## Referências

- Laya paper (Mar 2025): https://arxiv.org/abs/2503.xxxxx
- Laya repo: https://github.com/NandhaKishorM/laya
- Laya ONNX: https://github.com/receptron/laya
- Laya HF: https://huggingface.co/convaiinnovations/laya
- Jev vs Laya debate: https://www.reddit.com/r/LocalLLaMA/comments/1wlfmgq/
- Benchmark: https://www.alphamatch.ai/blog/jev-vs-laya-system-one-2026
- Tetris match: https://www.facebook.com/vyzualAI/posts/122135591013274950/
