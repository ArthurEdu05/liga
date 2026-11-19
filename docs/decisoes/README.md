# Registro de decisões (ADRs)

Cada arquivo registra uma decisão técnica importante: o contexto, o que foi decidido, as alternativas e as consequências. Uma decisão nunca é apagada; quando muda, uma nova ADR a substitui e a antiga é marcada como "Substituída".

| Nº | Decisão | Status |
|---|---|---|
| [0001](0001-custo-zero-sem-cartao.md) | Custo zero, sem cartão em nenhum serviço | Aceita |
| [0002](0002-python-fastapi-no-backend.md) | Python + FastAPI no backend, no lugar de Spring Boot | Aceita |
| [0003](0003-vercel-serverless.md) | Hospedagem serverless na Vercel | Aceita |
| [0004](0004-neon-postgres-pgvector.md) | PostgreSQL no Neon com pgvector | Aceita |
| [0005](0005-groq-como-llm.md) | Groq como provedor de LLM e de transcrição | Aceita |
| [0006](0006-embeddings-cloudflare.md) | Embeddings via Cloudflare Workers AI | Aceita |
| [0007](0007-telegram-como-mensageria.md) | Telegram como canal de mensageria | Aceita |
| [0008](0008-clerk-e-redis.md) | Clerk para autenticação e Redis para cotas e cache | Aceita |
| [0009](0009-multi-tenant-user-id-do-token.md) | Multi-tenant com `user_id` vindo do token | Aceita |
| [0010](0010-numeros-vem-do-banco.md) | Números vêm do banco, nunca do LLM | Aceita |

## Modelo

```markdown
# NNNN — Título

- **Status:** Proposta | Aceita | Substituída por NNNN
- **Data:** AAAA-MM-DD

## Contexto
## Decisão
## Alternativas consideradas
## Consequências
```
