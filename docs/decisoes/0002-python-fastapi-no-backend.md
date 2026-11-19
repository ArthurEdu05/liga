# 0002 — Python + FastAPI no backend, no lugar de Spring Boot

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
O plano inicial era Spring Boot (stack principal do autor) mais um serviço de IA em FastAPI. Com as restrições de custo zero e serverless (ADR 0001 e 0003), não há opção gratuita, sem cartão e sem expiração para rodar Java em serverless.

## Decisão
Backend inteiro em **Python + FastAPI**, com o agente de IA no mesmo serviço. Ferramentas: uv, Pydantic, SQLAlchemy, Alembic, pytest, pydantic-settings.

## Alternativas consideradas
- **Spring Boot + FastAPI:** exigiria uma máquina virtual sempre ligada; descartado.
- **TypeScript (Node/Next.js):** viável tecnicamente (LangGraph.js, Vercel AI SDK) e unificaria a linguagem com o Angular. Descartado porque o ecossistema de IA e de avaliação (datasets, métricas, Hugging Face) é mais maduro em Python, e as vagas-alvo de AI Engineer pedem Python.

## Consequências
- Uma linguagem só no backend, sem comunicação entre serviços.
- Os conceitos do Spring têm equivalentes diretos (FastAPI, `Depends()`, Pydantic, SQLAlchemy, Alembic, pytest).
- O pacote da função precisa caber no limite da Vercel para Python, o que influencia a ADR 0006.
