# 0008 — Clerk para autenticação e Redis para cotas e cache

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
O Liga é público (qualquer pessoa cria conta) e usa cotas gratuitas compartilhadas de IA. Os dois serviços já foram usados sem custo no projeto anterior do autor (Amazonas Berry).

## Decisão
- **Clerk** para cadastro, login e sessões. O backend valida o JWT do Clerk (via JWKS) e extrai o `user_id`.
- **Redis** para rate limit e cotas de IA por usuário, cache e estado temporário de conversas.

## Alternativas consideradas
- Auth própria: mais trabalho e mais risco de segurança, sem ganho para o portfólio.
- Cotas no Postgres: possível, mas Redis é mais adequado para contadores com expiração.

## Consequências
- **Pendência:** confirmar se o Clerk exige domínio próprio na instância de produção. Se exigir, decidir entre pagar um domínio (único custo do projeto) ou adotar outra auth gratuita; isso gera uma nova ADR.
- Um "disjuntor" global desliga a IA quando a cota do provedor estiver perto do fim, mantendo o modo manual.
