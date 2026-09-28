# Harness Engineering for Self-Improvement

**Autor:** Lilian Weng (OpenAI)  
**Data:** Julho 2026  
**Link:** https://lilianweng.github.io/posts/2026-07-04-harness/

---

## Tese Central

Auto-melhoria de agentes **não começa pelo modelo reescrevendo seus pesos** — começa pelo *harness engineering*. O harness é a camada mais maleável e com maior ROI para otimização.

---

## Conceitos-Chave

- **Harness-in-the-loop learning:** o próprio harness é otimizado junto com o modelo
- O harness pode: spawn de subagentes paralelos, monitorar jobs, executar I/O
- Visão: "harnesses evoluem em direção a auto-pesquisa, e modelos melhores permitem harnesses melhores"

---

## O que é um Harness (definição)

> *"O scaffolding ao redor do agente que entrega: contexto, tools, memória, estado, sandbox, orquestração."*

Componentes de um harness:
1. **Context delivery** — system prompt, memória de curto/longo prazo
2. **Tool interface** — como o modelo chama ferramentas (MCP, function calling)
3. **Planning state** — estado do plano atual (o que foi feito, o que falta)
4. **Memory** — memória persistente entre sessões
5. **Sandbox** — ambiente de execução seguro
6. **Orchestration** — como subagentes são coordenados

---

## Por que importa

1. **Referência conceitual** — define o vocabulário do movimento
2. **Influência direta** — papers de 2025-2026 citam este post
3. **Repositório derivado:** `leezythu/Awesome-Harness-Self-Improvement`

---

## Links

- Post: https://lilianweng.github.io/posts/2026-07-04-harness/
- Reading list: https://github.com/leezythu/Awesome-Harness-Self-Improvement
- Análise: https://www.developersdigest.tech/blog/harness-engineering-self-improvement
