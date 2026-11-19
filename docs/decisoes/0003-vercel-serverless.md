# 0003 — Hospedagem serverless na Vercel

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
O sistema precisa ficar disponível indefinidamente, inclusive após meses sem uso, sem custo e sem cartão.

## Decisão
Hospedar o front (Angular) e o backend (FastAPI como Vercel Functions) na **Vercel**, plano Hobby. O serverless "dorme" sem uso e "acorda" a cada requisição, sem máquina para expirar.

## Alternativas consideradas
- **Oracle Cloud Always Free (VM):** exige cartão, tem regra de conta inativa e falta de capacidade frequente; descartado.
- **Render (free):** o serviço hiberna e tem cold start longo, ruim para o webhook do Telegram; descartado.
- **Hugging Face Spaces:** hiberna após inatividade; descartado como backend principal.

## Consequências
- Limites por função (tamanho do pacote Python, duração e memória) precisam ser respeitados: nada de modelos pesados dentro da função.
- Tarefas agendadas (recorrências, alertas) via Vercel Cron Jobs.
- Sem processos longos em segundo plano; tudo orientado a requisição.
