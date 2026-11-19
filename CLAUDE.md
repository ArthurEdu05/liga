# CLAUDE.md — Liga

Contexto para qualquer sessão de desenvolvimento com IA neste repositório. Leia antes de mudar código.

## O que é o Liga

Assistente financeiro pessoal com IA. O usuário registra e consulta entradas e saídas **conversando** (chat na web ou Telegram, por texto ou áudio) e acompanha tudo num **painel gerencial**. Registrar um gasto deve levar segundos.

É um projeto de portfólio para vagas de AI Engineer / Full Stack com IA. Qualidade de engenharia (avaliação, observabilidade, segurança, decisões justificadas) importa tanto quanto as funcionalidades.

Documentos de referência:
- `docs/escopo.md`: escopo completo, conceitos, arquitetura e roadmap
- `docs/decisoes/`: ADRs, uma por decisão técnica. Decisão nova ou mudança de decisão = nova ADR.

## Stack

| Camada | Tecnologia |
|---|---|
| Front | Angular (PWA), em `web/` |
| Backend | Python + FastAPI, em `api/`, rodando como Vercel Functions |
| Agente | LangGraph |
| LLM | Groq (chamado atrás de uma interface própria, nunca direto espalhado pelo código) |
| Áudio | Whisper via Groq |
| Embeddings | Cloudflare Workers AI (`@cf/baai/bge-m3`) |
| Banco | PostgreSQL no Neon + pgvector |
| Cache / cotas | Redis |
| Auth | Clerk (JWT validado no backend) |
| Mensageria | Telegram Bot API (webhook) |
| Hospedagem | Vercel (front e backend) |
| Python tooling | uv, Pydantic, SQLAlchemy, Alembic, pytest, pydantic-settings |
| Evals | `evals/` (datasets + runner, resultados publicados no Hugging Face) |

## Regras inegociáveis

1. **Custo zero.** Só planos gratuitos, nenhum cartão cadastrado em nenhum serviço. Não introduzir dependência de serviço pago. Se um limite gratuito estourar, o sistema degrada (erro tratado), nunca gera cobrança.
2. **Sempre disponível.** A arquitetura é serverless e precisa funcionar depois de meses parada. Nada que dependa de servidor sempre ligado ou que expire por inatividade.
3. **Multi-tenant desde o dia 1.** Toda tabela de dados do usuário tem `user_id`. O `user_id` vem **sempre** do token autenticado (ou do vínculo do Telegram), nunca do corpo da requisição nem do LLM. Toda query filtra por `user_id`.
4. **Números vêm do banco, nunca do LLM.** O LLM interpreta e redige; somas, totais e filtros são feitos em SQL por ferramentas tipadas.
5. **Dinheiro em `NUMERIC`/`Decimal`**, nunca `float`.
6. **Segredos só em variáveis de ambiente.** Nunca commitar `.env` ou chaves. Manter `.env.example` atualizado.
7. **Privacidade.** Não enviar nome, e-mail ou outros dados pessoais ao LLM; só o texto necessário para a tarefa.
8. **Degradação elegante.** Sem IA disponível (cota, erro), registro manual e painel continuam funcionando.

## Arquitetura (resumo)

```
Angular (PWA) ─┐
               ├─► FastAPI (Vercel Functions)
Telegram ──────┘     ├─ Auth (JWT Clerk) + cotas (Redis)
                     ├─ API de lançamentos e painel
                     ├─ Adaptadores de canal (web, telegram)
                     └─ Agente LangGraph
                          ├─ Ferramentas → PostgreSQL (Neon)
                          ├─ RAG → pgvector + embeddings (Cloudflare)
                          ├─ LLM → Groq
                          └─ Áudio → Whisper (Groq)
```

- Um único agente atende todos os canais; cada canal é um adaptador.
- Classificação de estabelecimento em camadas: histórico do usuário → similaridade por embeddings → LLM só se necessário.
- Toda execução do agente gera trace (tokens, latência, ferramentas chamadas, erros).

## Modelo de dados (diretrizes)

O banco já nasce preparado para o escopo completo, mesmo que a interface venha depois:
- contas, cartões, faturas, compras parceladas (uma compra → N parcelas, cada uma em uma fatura), recorrências (regra que gera lançamentos), categorias, lançamentos.
- campo `origem` em cada lançamento: `manual | chat | audio | telegram | nota_fiscal | extrato`.
- migrações sempre via Alembic.

## Estrutura do repositório

```
liga/
├── web/      Angular (PWA)
├── api/      FastAPI (rotas → serviços → repositórios), agente em api/.../agent
├── evals/    datasets e runner do eval harness
└── docs/     escopo e ADRs
```

## Convenções

- Idioma: código (nomes de variáveis, funções, classes) em portugês; documentação, mensagens ao usuário e commits em português.
- Python: tipagem completa, Pydantic para entradas/saídas, camadas rotas → serviços → repositórios, injeção via `Depends()`.
- Testes com pytest para regras de negócio; toda mudança de prompt/agente deve rodar os evals.
- Commits pequenos e descritivos (Conventional Commits: `feat:`, `fix:`, `docs:`, `chore:`...).
- O dono do repositório revisa e commita manualmente: não fazer commit nem push sem pedido explícito.

## Roadmap

| Fase | Entrega | Status |
|---|---|---|
| 0 | Fundação: repositório, ambiente, banco, esqueleto FastAPI e Angular, primeiro deploy | em andamento |
| 1 | Núcleo financeiro: registro manual, contas, categorias, painel básico | — |
| 2 | Assistente de IA no chat + evals e observabilidade | — |
| 3 | Áudio | — |
| 4 | Bot no Telegram | — |
| 5 | Nota fiscal (QR NFC-e) e importação de extrato (OFX/CSV) | — |
| 6 | Cartões, faturas, parcelas, recorrências | — |
| 7 | Orçamentos e alertas | — |
| 8 | Login, controle de acesso e deploy público | — |

Nenhum deploy público com dados reais antes da fase 8.

