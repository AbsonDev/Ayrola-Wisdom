# Feature Comparison — Estado da Arte em 2026

Comparação direta entre os principais agent harnesses disponíveis.

---

## Matriz de Features

| Feature | Prime Agent | OpenCode | DeepSeek Harness | Claude Code | Cursor |
|---|---|---|---|---|---|
| **RLM nativo** | ✅ Sim | ✅ Sim | ❓ Unknown | ❌ Não | ❌ Não |
| **Kernel REPL persistente** | ✅ IPython | ✅ Shell | ❌ | ❌ Sandbox | ❌ Sem estado |
| **Auto-refinamento harness** | ✅ /refine | ✅ | ❌ | ❌ | ❌ |
| **Subagentes recursivos** | ✅ Sem limite | ✅ Zen lane | ❓ | ✅ Limitado (depth cap) | ❌ |
| **Multi-modelo fallback** | ✅ 3+ providers | ✅ | ❌ | ✅ | ✅ |
| **Sandbox real** | ⚠️ Docker | ✅ | ❌ | ✅ srt | ✅ |
| **Decisão barata ($0)** | ✅ Laya | ✅ Zen lane | ❌ | ❌ | ❌ |
| **Plugin-first** | ❌ Built-in | ❌ Built-in | ✅ Radical | ❌ Built-in | ❌ Built-in |
| **Multi-canal** | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Auto-healing skills** | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Open-source** | ✅ | ✅ MIT | ✅ MIT | ❌ | ❌ |
| **Serverless-friendly** | ❌ | ❌ | ❌ | ❌ | ✅ |

**Legenda:** ✅ Yes | ❌ No | ⚠️ Parcial | ❓ Unknown

---

## Pontos Fortes por Harness

### Prime Agent
- Melhor auto-refinamento (`/refine`)
- Subagentes recursivos sem limite
- Kernel IPython completo

### OpenCode
- Mais simples de instalar (MIT, TS)
- Zen lane zero-cost
- Hot reload de skills
- Usa Laya como decision layer (open-source)

### DeepSeek Harness
- Plugin-first (mais extensível)
- 95k+ stars em 2 dias (adoção massiva)
- 5 projetos derivados (comunidade ativa)
- MIT license

### Claude Code
- Sandbox mais maduro (srt)
- 39% adoção entre devs
- Ecossistema MCP maduro
- Não open-source (limita customização)

### Cursor
- Melhor UX
- IDE-first
- Cloud agents
- Não open-source

---

## Pontos Fracos por Harness

| Harness | Fraqueza Principal |
|---|---|
| Prime Agent | Runtime Python (performance), ainda beta |
| OpenCode | Menos features que Prime Agent |
| DeepSeek Harness | Muito novo (estabilidade?) |
| Claude Code | Não open-source, sem auto-refinamento |
| Cursor | Não open-source, sem CLI |

---

## Onde o Ayrola pode se posicionar

| Diferencial | Estado no Mercado | Oportunidade para Ayrola |
|---|---|---|
| RLM + Harness auto-refinável | Prime Agent domina | Foco em Rust/TS runtime |
| Plugin-first | DeepSeek Harness lidera | Melhor DX + documentação |
| Decisão barata ($0) | OpenCode Zen lane | Integração mais simples |
| Multi-canal | OpenClaw domina | Foco em coding + multi-canal |
| Auto-melhoria nível 2 | Prime Agent | Extensão para nível 3 (pesquisa) |
