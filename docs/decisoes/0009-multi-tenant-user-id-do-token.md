# 0009 — Multi-tenant com `user_id` vindo do token

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
Vários usuários usam o mesmo sistema e os dados são financeiros. O agente de IA chama ferramentas que acessam o banco, e um prompt injection poderia tentar acessar dados de outra pessoa.

## Decisão
- Toda tabela de dados de usuário tem `user_id` desde a primeira migração, mesmo antes do login existir.
- O `user_id` vem **sempre** do token autenticado (ou do vínculo do Telegram), nunca do corpo da requisição nem de argumentos gerados pelo LLM.
- As ferramentas do agente recebem o `user_id` do contexto da execução; o LLM não tem como escolhê-lo.

## Consequências
- Nenhuma query de dados de usuário sem filtro por `user_id`; isso deve ser coberto por testes.
- Habilitar o login (fase 8) não exige migração de dados.
