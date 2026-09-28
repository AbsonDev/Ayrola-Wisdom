# Continual Harness: Online Adaptation for Self-Improving Foundation Agents

**Autor:** Seth Karten  
**Data:** Maio 2026  
**arXiv:** [2605.09998](https://arxiv.org/abs/2605.09998)  
**Citações:** 30+

---

## Resumo

Framework **reset-free** para agentes auto-aprimoráveis. Automatiza o refinamento do harness sem intervenção humana — o agente aprende online, sem recomeçar do zero.

---

## Conquistas Documentadas

- Derrotou **Pokémon Blue** em Maio 2025
- Derrotou **Elite Four no Pokémon Yellow Legacy (hard mode)** em Agosto 2025

---

## Conceito Central

O loop `/refine` do Prime Agent é uma implementação direta deste paper:
1. Agente executa tarefa → coleta trajetória
2. Avalia trajetória atual
3. Propõe mudanças no harness (prompts, tools, rotas)
4. Aplica mudanças se evidência for suficiente
5. Repete

---

## Por que importa

1. **Valida auto-refinamento** — Prime Agent não está sozinho
2. **Mostra loop fechado** — agente aprende com sua própria experiência
3. **Aplicação prática** — o `/refine` do Prime Agent é produção

---

## Links

- Paper: https://arxiv.org/abs/2605.09998
- Blog: https://sethkarten.ai/continual-harness/
- Reddit: https://www.reddit.com/r/MachineLearning/comments/1tcmj6v
