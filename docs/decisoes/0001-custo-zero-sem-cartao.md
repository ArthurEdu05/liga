# 0001 — Custo zero, sem cartão em nenhum serviço

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
O Liga é um projeto pessoal e de portfólio, aberto ao público. Ele precisa continuar no ar por tempo indeterminado, inclusive em períodos sem manutenção, sem gerar nenhuma despesa.

## Decisão
Usar apenas serviços com plano gratuito permanente e **não cadastrar cartão em nenhum deles**. Assim, ao atingir um limite gratuito, o serviço recusa a requisição (ex.: erro 429) em vez de cobrar. O custo zero passa a ser garantido pela arquitetura, e não por disciplina.

## Alternativas consideradas
- Planos pagos baratos: descartado, fere o requisito.
- Free tiers que exigem cartão (ex.: Oracle Cloud Always Free): descartado, abre caminho para cobrança e tem regras de inatividade.

## Consequências
- O sistema precisa tratar limites como situação normal: cotas por usuário, tratamento de 429 e degradação elegante (modo manual segue funcionando).
- Escolhas de stack ficam restritas ao que tem free tier permanente e sem cartão.
- Único custo opcional futuro: domínio próprio.
