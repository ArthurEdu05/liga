# 0004 — PostgreSQL no Neon com pgvector

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
Os dados financeiros são relacionais (compra → parcelas → fatura → cartão), exigem agregações (totais por categoria e período) e transações. O RAG precisa de busca vetorial.

## Decisão
**PostgreSQL no Neon**, plano gratuito, com a extensão **pgvector** para os embeddings no mesmo banco.

## Alternativas consideradas
- **Supabase:** pausa projetos gratuitos após 7 dias de inatividade; fere o requisito de disponibilidade.
- **Redis como banco principal:** inadequado para relações, agregações e transações; fica restrito a cotas e cache (ADR 0008).
- **Banco vetorial separado:** desnecessário com o pgvector.

## Consequências
- A cota gratuita de computação é **mensal e renovada todo mês**. O banco desliga sozinho após alguns minutos sem uso e não consome cota parado; o uso pessoal estimado fica muito abaixo do limite.
- Se a cota estourar, o banco fica suspenso até o mês virar, sem cobrança (ADR 0001). O tamanho de computação fica fixo no mínimo.
- A primeira consulta após o banco "acordar" é um pouco mais lenta.
- Limite de armazenamento do plano gratuito precisa ser monitorado.
