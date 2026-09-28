# Auto-Melhoria — Harnesses que Aperfeiçoam a Si Mesmos

O movimento para dentro: agentes que não só executam tarefas, mas melhoram o próprio ambiente em que rodam.

---

## 1. Self-Harness (qzzqzzb)

**Repo:** https://github.com/qzzqzzb/Self-Harness  
**Paper:** https://arxiv.org/abs/2606.09498

### O que é
Paradigma onde um agente LLM melhora seu próprio harness — sem re-treinar o modelo. O modelo e avaliador ficam fixos; apenas o harness é otimizado.

### Mecanismo
```
1. Avalia harness atual em tasks
2. Propõe mudanças estruturais (prompts, tools, rotas, memória)
3. Testa em validação hold-out
4. Aplica apenas se houver ganho mensurável
```

### Implementação
- Mantém pesos do modelo fixos
- Mantém avaliador fixo
- Itera apenas sobre o harness
- Valida cada mudança antes de aplicar

---

## 2. Awesome-Harness-Self-Improvement (leezythu)

**Repo:** https://github.com/leezythu/Awesome-Harness-Self-Improvement  
**Inspiração:** Blog post de Lilian Weng (Jul 2026)

### O que é
Lista curada de leituras sobre engenharia de harness para auto-melhoria de agentes LLM.

### Conteúdo
- Papers sobre continual learning
- Projetos open-source de self-improvement
- Reading list EN/ZH (bilingue)
- Cobertura de 2025-2026

### Por que usar
- Fonte definitiva para pesquisa em self-improvement
- Atualizado pela comunidade
- Referência bibliográfica confiável

---

## 3. Prime Agent /refine

**Comando:** `/refine` no Prime Agent

### O que é
Loop de refinamento:
1. Lê 80k chars de trajetória
2. LLM background propõe CRUD mínimo no harness
3. Laya aprova se evidência for suficiente
4. Aplica mudança (skills, memórias, rotas)

### Características
- **Evidência-backed** — apenas aplica se validado
- **Smallest possible** — minimal change, não rewrite
- **Background LLM call** — não trava o agente
- **Laya gate** — ~$0.0005/sessão para validação

---

## 4. RHI (Recursive Harness Self-Improvement)

**Paper:** https://www.alphaxiv.org/abs/2607.15524

### O que é
Algoritmo iterativo de auto-melhoria: cada rodada avalia e melhora o harness.

### Conceito
- Harness-in-the-loop learning
- Otimiza harness para performance imediata E para qualidade de traces futuros
- Conecta self-improvement com continual learning

---

## Tabela Comparativa

| Projeto | Tipo | Auto-melhora | Código | Paper |
|---|---|---|---|---|
| Self-Harness | Framework | ✅ | ✅ | ✅ |
| Prime Agent `/refine` | Feature | ✅ | Impl. | Inspiração |
| RHI | Algoritmo | ✅ | — | ✅ |
| Awesome-HSI | Lista | Curada | — | — |
