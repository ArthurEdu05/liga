# 0007 — Telegram como canal de mensageria

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
O usuário quer registrar gastos pelo app de mensagens que já usa, além do chat da web.

## Decisão
**Telegram Bot API** via webhook. A conta do Telegram é vinculada à conta do Liga por um código de uso único gerado no painel (`/start <código>`). O webhook valida o secret token em toda chamada.

## Alternativas consideradas
- **WhatsApp Cloud API:** desde outubro de 2026 a Meta cobra mensagens de serviço após uma franquia mensal, além de exigir conta Meta Business, número dedicado e meio de pagamento; fere a ADR 0001.
- **Bibliotecas não oficiais do WhatsApp:** violam os termos de uso e arriscam banimento do número.

## Consequências
- O backend tem uma camada de adaptadores de canal; web e Telegram são implementações da mesma interface. O WhatsApp pode virar um novo adaptador no futuro sem mudar o agente.
- Mensagens sem vínculo de conta não são processadas.
