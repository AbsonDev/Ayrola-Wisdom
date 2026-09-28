# Recursive Agent Harnesses (RAH)

**Autor:** Elias Lumer  
**Data:** Junho 2026  
**arXiv:** [2606.13643](https://arxiv.org/abs/2606.13643)  
**Citações:** 6+

---

## Achado Principal

Um harness recursivo melhora o baseline Codex de **71.75% → 81.36%** na benchmark Oolong-Synthetic (usando Claude Sonnet 4.5 como backbone).

---

## Inovação Técnica

- Subagentes herdam a capacidade de spawn do pai (recursão com profundidade configurável)
- Recursion over model calls como estratégia primária — não um workaround
- Prova que a recursividade no harness é mais importante que o modelo base para tarefas complexas

---

## Por que importa

1. **Valida academicamente o RLM** — Prime Agent e RLM-Code não são experimentos isolados
2. **Mostra ganhos concretos** — +9.6 pontos percentuais sobre Codex puro
3. **Define o estado da arte** em coding agents para 2026

---

## Links

- Paper: https://arxiv.org/abs/2606.13643
- HTML: https://arxiv.org/html/2606.13643v1
