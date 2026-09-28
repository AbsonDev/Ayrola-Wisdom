# Template — Proposta do Ayrola Harness

> Preencha cada seção. Não precisa responder tudo agora — é um guia para ir pensando.
> Quando tiver as respostas, este arquivo vira a sua proposta de verdade.

---

## 1. IDENTIDADE

**Nome:** Ayrola Harness
**Uma frase:** *(complete)* — ex: "O harness que nunca esquece e sempre melhora."

**Para quem:** *(complete)*
- Opção A: devs solo que querem coding agent de longo prazo
- Opção B: equipes que precisam de agentes auditáveis
- Opção C: qualquer pessoa que usa IA para trabalho real (não só demo)

**Problema que resolve:** *(complete)*
> Existing: OpenCode é simples mas limitado. Prime Agent é powerful mas complexo.
> DeepSeek Harness é extensível mas imaturo. Claude Code é bom mas fechado.

---

## 2. PARÂMETROS DO AGENTE
*(baseado em `03-trends/02-decisoes-tecnicas.md`)*

| Decisão | Escolha | Justificativa |
|---|---|---|
| Linguagem runtime | ☐ Python ☐ Rust ☐ TypeScript ☐ Go | |
| Kernel persistente | ☐ IPython ☐ Shell ☐ conkernel ☐ Próprio | |
| RLM | ☐ Nativo ☐ Manual ☐ Híbrido | |
| Auto-melhoria | ☐ Nível 1 ☐ Nível 2 ☐ Nível 3 | |
| Decisão barata | ☐ Jev ☐ System One local ☐ Sem | |
| Sandbox | ☐ Docker ☐ E2B ☐ srt ☐ Sem | |
| Multi-modelo | ☐ Sim ☐ Não | |
| Plugin system | ☐ MCP ☐ Plugin-first ☐ Built-in | |
| Multi-canal | ☐ Sim ☐ Não | |

---

## 3. DIFERENCIAL (a resposta de "por que Ayrola e não os outros?")

Preencha com **no máximo 3** diferenciais claros:

1. **Diferencial 1:** *(ex: kernel persistente que sobrevive a crash)*
2. **Diferencial 2:** *(ex: auto-melhoria com validação em hold-out)*
3. **Diferencial 3:** *(ex: zero custo de decisão com layer local)*

**O que NÃO vai fazer:** *(o que você deliberadamente deixa de fora)*
> Ex: não vai ser IDE. não vai ter sandbox próprio. não vai suportar 20 modelos.

---

## 4. FEATURES MVP (mínimo viável)

Liste 3-5 features para a primeira versão funcional:

- [ ] Feature 1: *(ex: kernel persistente)*
- [ ] Feature 2: *(ex: subagentes recursivos com depth limit)*
- [ ] Feature 3: *(ex: MCP nativo)*
- [ ] Feature 4: *(ex: /refine para auto-melhoria)*
- [ ] Feature 5: *(ex: multi-modelo com fallback)*

**Fora do MVP (roadmap):**
- ☐ Item futuro 1
- ☐ Item futuro 2

---

## 5. O QUE JÁ EXISTE QUE VOCÊ PODE REUSAR

| Componente | De onde | Licença |
|---|---|---|
| Kernel REPL | conkernel / clikernel | MIT |
| RLM | alexzhang13/rlm | MIT |
| Decision layer | Jev / System One | API key |
| Sandbox | Anthropic srt | MIT |
| MCP | Model Context Protocol | Open spec |

**Regra:** não reescreva o que já existe e é bom. Integre.

---

## 6. BENCHMARK DE SUCESSO

Como medir que o Ayrola é bom? Escolha 2-3:

- [ ] **Resolving benchmark** (Oolong-Synthetic) — seguir o modelo RLM/RAH
- [ ] **Long-horizon** — tarefa de 2h+ sem intervenção
- [ ] **Custo por sessão** — quanto custa rodar 1000 tasks
- [ ] **Auto-melhoria mensurável** — ganho após 10 sessões de /refine
- [ ] **Developer experience** — tempo do primeiro commit

**Número-alvo:** *(ex: resolver 85% da Oolong-Synthetic por <$0.50/task)*

---

## 7. ROADMAP

### Fase 0 — Fundação (1-2 semanas)
- [ ] Estrutura do repo e arquitetura base
- [ ] Kernel persistente funcionando
- [ ] Loop agent básico (tool call + resposta)

### Fase 1 — MVP (4-6 semanas)
- [ ] Features do MVP funcionando
- [ ] MCP integrado
- [ ] Multi-modelo com fallback
- [ ] Primeiro benchmark rodando

### Fase 2 — Diferenciação (4-8 semanas)
- [ ] /refine (auto-melhoria nível 2)
- [ ] Subagentes recursivos
- [ ] Decision layer ($0)

### Fase 3 — Produção (contínuo)
- [ ] Sandbox hardening
- [ ] Documentação e exemplos
- [ ] Comunidade

---

## 8. NOTES / DÚVIDAS

Anote dúvidas que você precisa resolver antes de começar:

- [ ] Dúvida 1
- [ ] Dúvida 2
- [ ] Dúvida 3

---

## Referências

- Tendências: `03-trends/01-tendencias-2026.md`
- Decisões técnicas: `03-trends/02-decisoes-tecnicas.md`
- Comparativo: `04-comparative/FEATURE_COMPARISON.md`
- Gap analysis: `04-comparative/GAP_ANALYSIS.md`
- Papers: `01-papers/`
- Repos para reusar: `02-repositories/`
