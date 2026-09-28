# VEREDITO — Revisão Crítica do Plano (2026-09-28)

Este documento responde a uma auditoria externa que apontou problemas reais.
Cada claim foi **verificado contra o código** antes de aceitar ou recusar.

---

## 1. Claims verificados como CORRETOS

| Claim da auditoria | Verificação | Status |
|---|---|---|
| Phase 0 = 5.361 linhas de código, 161 testes (não 9) | `wc -l` nos 5 arquivos .rs | ✅ CONFIRMADO |
| `decision.rs:97` diz "Phase 0: stub" | Linha lida | ✅ CONFIRMADO |
| Laya nunca foi integrado | `loaded` flag, heurística `contains("spawn")` | ✅ CONFIRMADO |
| Fork = 406 .rs em src/ (não 875), 663.548 linhas em src/ (não 1.94M), 693.456 total | `find src -name "*.rs" \| wc -l` | ✅ CONFIRMADO |
| `ort` no Cargo.toml Phase 0 = `2.0.0-rc.13` (o fork não tem `ort` nenhum) | grep no Cargo.toml | ✅ CONFIRMADO |
| `asupersync` = 4 ocorrências, `tokio` = 0 (0% migração) | grep no Cargo.toml | ✅ CONFIRMADO |
| Migração para Tokio = 0% executada | `asupersync` ainda no Cargo.toml | ✅ CONFIRMADO |
| `DECISOES.md` tinha tabela quebrada e `#3` duplicado | Leitura direta | ✅ CONFIRMADO |
| `PROPOSTA.md:38` tinha caractere chinês `不问` | Leitura direta | ✅ CONFIRMADO |
| README dizia "9/9 tests" e "Fase 0 EM ANDAMENTO" | Leitura direta | ✅ CONFIRMADO |

---

## 2. Claims que exigem nuance

### 2.1 "Laya é near chance sem fine-tune"

A fonte citada (blog alphamatch.ai) afirma que checkpoints base ficam perto de acaso
em decision sets tipados antes de especialização. **Aceito como risco real**, mas com
ressalvas:

- Não tenho acesso ao modelo para medir. Aceito como **incerteza**, não como fato medido.
- A distribuição oficial de Laya é Python (`pip install laya`). Não achei crate Rust oficial.
- `receptron/laya` no GitHub é Node/TypeScript, não Rust.

**Implicação:** não posso afirmar "Laya é 4x mais rápido que Jev" nem "vence 26-1".
Essas comparações vieram de fonte secundária que a própria fonte desaconselha.

### 2.2 "Rust puro contradiz Laya"

Aceito. `DECISOES.md` diz "zero Python, zero FFI". Laya é pacote Python. Exportar
ONNX à mão de um modelo não-autoregressivo com router é exatamente o caso que quebra
exportadores.

**Decisão tomada:** Laya sai da decisão fechada. Vira **candidato do tier 2** do ensemble.

### 2.3 "A métrica de spawn é vazia"

Aceito parcialmente. `tokio::spawn` é microsegundo. Comparar com start de processo
Python (500ms-2s) não é uma comparação justa — são coisas diferentes.

O MANIFESTO diz "10 subtarefas × 10ms = 100ms total". Em paralelo real são ~10ms.
**A métrica precisa ser medida, não estimada.**

---

## 3. Decisões revertidas

### 3.1 Laya: decisão fechada → candidato

**Motivo:** evidência insuficiente (9 dias), distribuição oficial Python, sem crate
Rust, e o blog de comparação desaconselha exatamente o uso que fizemos.

**Substituição:** ensemble de 3 tiers (seção 4).

### 3.2 Fork como base → backend via MCP

**Motivo:** 1.12M linhas que não escrevi, 60% tocados, 10 semanas de refactor
mecânico antes de qualquer valor Ayrola. Pior: o plano deletava `swarm_replay.rs`,
`swarm_activity_ledger.rs`, `checkpoint.rs` — que são exatamente o Pilar 1.

**Substituição:** kernel Ayrola próprio (~3-5k linhas). pi_agent_rust vira backend
de tools via MCP. Ganha largura de tools sem herdar dívida.

### 3.3 Nível 3 com validação de tipo → verificação de comportamento

**Motivo:** `cargo check` prova tipos, não semântica. E `refine/environment.rs`
especificava "mutar benchmark suite" — auto-modificar o próprio benchmark é
**reward hacking por construção**.

**Substituição:** shadow executor. Toda mudança candidata roda contra um golden set
fixo. Só é promovida se delta > 0 em todos os eixos. Benchmark é imutável.

---

## 4. Novo eixo 1: Ensemble de decisão em 3 tiers

A intuição original estava certa: **decisão é mais cara em LLM do que devia**.
O erro foi escolher o modelo errado. O ganho real não é latência — é **fazer o LLM
fazer 10x menos trabalho**.

```
Tier 0: Cache semântico
  └─ hit (similaridade > 0.95) → 0ms, 0 custo
  └─ ADR-005: decision cache com hash de (contexto normalizado, tier)

Tier 1: Classificador pequeno (pre-filter)
  └─ 4-8 params, ONNX, ~5ms
  └─ só escolhe entre N candidatos pré-definidos
  └─ NÃO decide sozinho — só descarta alternativas

Tier 2: LLM completo
  └─ só quando tier 0 e 1 não resolvem
  └─ ou quando confiança < threshold
  └─ aqui sim: Laya é opção, ou qualquer outro
```

**Por que isso é melhor que Laya solo:**
- Não te prende a um fornecedor
- Testável em 1 semana (medir custo/decisão vs baseline)
- O tier 0 (cache) é o que realmente mata custo em workloads repetitivos
- Laya vira **checkpoint opcional do tier 2**, não o sistema inteiro

**Métrica de sucesso:** custo por decisão reduzido com delta de acerto < 2%.

---

## 5. Novo eixo 2: `ayrola-bench` como produto

**O gap real:** não existe harness de eval público para agent harnesses. Todo mundo
afirma que seu agente é melhor sem medir.

**O que é:** tasks reais, replayable, com scoreboard de custo / latência / qualidade
e regressão automática.

**Por que isso é o asset mais importante:**
- Resolve o problema "não posso provar nenhum dos 5 diferenciais sem comparar"
- Cria o padrão contra o qual todos vão se medir
- Gera flywheel: o bench descobre o que melhorar
- É defensável: primeiro a fazer, primeiro a padrão

**Métrica:** 20 tasks reais + baseline honesto de OpenCode e Claude Code.

---

## 6. Novo eixo 3: Certificação de decisão

Redesenha o Pilar 1 de "memória bonita" para **auditabilidade**.

Cada decisão carrega um certificado:
```json
{
  "decision_id": "uuid",
  "inputs": {"question": "...", "context_hash": "sha256:..."},
  "decision": {"choice": "option_b", "confidence": 0.82},
  "evidence": ["event:abc123", "event:def456"],
  "cost": {"tokens": 1200, "usd": 0.003, "latency_ms": 340},
  "tier": 1,
  "replayable": true
}
```

Replay é **determinístico e verificável por hash**. É isso que exige o event store
imutável — e é exatamente o que quase ninguém tem.

**Por que é difícil de copiar:** exige event log confiável desde o dia 1. retrofit
é caro.

---

## 7. Roadmap revisado: 4 semanas, não 10

| Sem. | Entrega | Critério de saída |
|---|---|---|
| 1 | Kernel novo do zero: event store + decision trait | append <1ms, replay determinístico verificado por hash |
| 2 | `ayrola-bench` v0: 20 tasks reais + baseline OpenCode | número honesto de resolve rate, custo, latência |
| 3 | Ensemble de decisão (cache → pre-filter → LLM) | custo/decisão reduzido com delta de acerto < 2% |
| 4 | README honesto + 1 ADR real | status real, sem docs corrompidos |

Semanas 5+ a partir de dado real, não de estimativa inventada.

---

## 8. Estado atual honesto

| Item | Realidade |
|---|---|
| Código Ayrola escrito | 5.361 linhas, 165 testes (161 lib + 4 e2e), **stub** |
| Laya integrada | Não. Heurística `contains("spawn")` |
| Migração Tokio | 0%. `asupersync` ainda no Cargo.toml |
| Fork no GitHub | Sim, `077cccb`, intocado |
| Benchmark | Não existe |
| Decisões revertidas | 3 (esta sessão) |

**Nada aqui é um produto. É pesquisa com um repositório público.**
