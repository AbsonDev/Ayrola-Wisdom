# Self-Harness: Harnesses That Improve Themselves

**Autor:** H. Zhang  
**Data:** Junho 2026  
**arXiv:** [2606.09498](https://arxiv.org/abs/2606.09498)  
**Citações:** 45+

---

## Tese Central

Um agente LLM pode melhorar seu **próprio harness operacional** — sem mexer nos pesos do modelo, sem re-treinamento. Apenas otimizando o código ao redor (prompts, tools, rotas, memória).

---

## Mecanismo

1. Avalia harness atual em conjunto de tarefas
2. Propõe mudanças estruturais (não só tuning de prompt)
3. Testa mudanças em validação hold-out
4. Aplica apenas se houver ganho mensurável

---

## Código Disponível

- Repositório: [qzzqzzb/Self-Harness](https://github.com/qzzqzzb/Self-Harness)
- Implementação reproduzível do paper

---

## Por que importa

1. **Valida auto-refinamento como paradigma** — não é feature do Prime Agent, é movimento
2. **Mostra que harness é camada de otimização** — tão importante quanto o modelo
3. **Abre caminho para RL aplicado à infra** — não só ao prompt

---

## Links

- Paper: https://arxiv.org/abs/2606.09498
- Código: https://github.com/qzzqzzb/Self-Harness
- AlphaXiv: https://www.alphaxiv.org/abs/2606.09498
