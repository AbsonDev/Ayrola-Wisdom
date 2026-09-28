# Runtimes Emergentes — Rust / Go / TypeScript

Projetos que questionam a hegemonia do Python como runtime de agentes.

---

## 1. pi_agent_rust

**Repo:** https://github.com/Dicklesworthstone/pi_agent_rust  
**Stars:** ~2k+  
**Linguagem:** Rust (zero unsafe code)  
**Baseado em:** Pi Agent (coding harness minimalista)

### O que é
Port de *Pi Agent* escrito em Rust, com foco em performance e segurança.

### Inovações
- **Zero unsafe code** — máxima segurança em runtime de agente
- **Rust resolve GIL** — concorrência real, sem Global Interpreter Lock
- **Startup rápido** — binário nativo vs Python interpreter
- **Memória previsível** — sem GC pauses

### Contexto
- GitHub Copilot migrou **800k+ linhas** de runtime para Rust em 2026
- Discussão HN: código polêmico, mas a direção está correta
- Early stage — não recomendado para produção ainda

### Quando observar
Quando maturar (~6-12 meses), pode ser a base para coding agents de alta escala

---

## 2. AnswerDotAI/conkernel

**Repo:** https://github.com/AnswerDotAI/conkernel  
**Data:** 2026  
**Linguagem:** Python  
**Tipo:** Biblioteca (plugável)

### O que é
Dá ao agente um **Jupyter kernel persistente** — envia código, aguarda resultado, mantém estado.

### Irmão
- `clikernel` — Python/Luau session persistente, 500 linhas de Python

### Por que importa
- **Valida o kernel persistente** como padrão — biblioteca independente
- Custo de adoção baixo — plugável em qualquer agente
- Se o Prime Agent é o "iPhone do kernel persistente", o conkernel é o "Android": acessível, reutilizável

---

## 3. Mastra

**Repo:** https://github.com/mastra-ai/mastra  
**Stars:** Crescimento rápido (2025-2026)  
**Linguagem:** TypeScript  
**Foco:** Serverless-friendly, Next.js

### O que é
Framework de agentes TypeScript-first, serverless-friendly.

### Inovações
- **TypeScript-first** — type safety nativo
- **Serverless-friendly** — deploy em Vercel/Edge
- **Next.js integrado** — agentes como API routes
- **Crescimento rápido** — comunidade ativa

### Quando usar
- Projetos Next.js que precisam de agentes
- Tipagem forte é prioridade
- Serverless deployment

---

## 4. AutoAgents (Rust runtime)

**Repo:** Pesquisa em andamento  
**Linguagem:** Rust  
**Foco:** Edge/cloud, produção

### O que é
Runtime nativo Rust para agentes de produção — segurança, modularidade, performance.

### Tendência
- Rust está ganhando tração como runtime de agentes
- OpenAI Agents SDK, Mastra, e agora Rust runtimes
- Python fica na camada de *definição de harness*, não runtime

---

## 5. GitHub Copilot Runtime (migração para Rust)

**Artigo:** https://github.blog/ai-and-ml/generative-ai/migrating-the-github-copilot-runtime-to-rust-using-copilot/  
**Data:** Q2 2026  
**Escala:** 800k+ linhas de Rust

### O que é
GitHub migrou o runtime do Copilot de TypeScript/Node.js para Rust usando seus próprios agentes.

### Por que importa
- **Validação em escala** — não é projeto pequeno
- **Auto-migração** — agentes migrando agentes
- Sinaliza que Rust é o futuro de runtimes de produção

