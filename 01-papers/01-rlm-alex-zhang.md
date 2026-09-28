# Recursive Language Models (RLM)

**Autor:** Alex Zhang  
**Data:** Dezembro 2025  
**arXiv:** [2512.24601](https://arxiv.org/abs/2512.24601)  
**Citações:** 117+  
**Código:** [alexzhang13/rlm](https://github.com/alexzhang13/rlm)

---

## Resumo

O paper propõe **Recursive Language Models (RLMs)** — uma estratégia de inferência onde LLMs processam prompts arbitrariamente longos através de **recursão sobre chamadas de modelo**. O modelo decompõe problemas, invoca a si mesmo recursivamente, e agrega resultados parciais para chegar à resposta final.

---

## Conceito Central

```
LLM recebe prompt longo
    ↓
Modelo decompõe em subproblemas
    ↓
Invocação recursiva do mesmo modelo (com contexto fresco)
    ↓
Agregação de resultados parciais
    ↓
Resposta final
```

---

## Resultados

- Comparado diretamente contra **Claude Code** e **OpenCode**
- Duas variantes testadas (com e sem recursão)
- A recursão sobre chamadas de modelo é uma estratégia eficaz para long-context reasoning

---

## Por que importa para o Ayrola Harness

1. **Valida o RLM como paradigma** — não é experimento isolado
2. **Biblioteca de referência** — `alexzhang13/rlm` é plug-and-play
3. **Base para Prime Agent** — o Prime Agent é uma implementação direta
4. **Benchmark** — Oolong-Synthetic é a referência

---

## Links

- Paper: https://arxiv.org/abs/2512.24601
- Blog: https://alexzhang13.github.io/blog/2025/rlm/
- Código: https://github.com/alexzhang13/rlm
- Blog Prime Intellect: https://www.primeintellect.ai/blog/rlm
