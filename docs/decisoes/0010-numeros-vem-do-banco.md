# 0010 — Números vêm do banco, nunca do LLM

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
LLMs erram contas e podem inventar valores. Num app financeiro, um total errado destrói a confiança no produto.

## Decisão
O LLM só **interpreta** a pergunta e **redige** a resposta. Somas, totais, filtros e comparações são feitos em SQL por **ferramentas tipadas** (ex.: `consultar_gastos(categoria, periodo)`), e a resposta usa exatamente os números retornados.

## Alternativas consideradas
- **Mandar os lançamentos ao LLM para ele somar:** impreciso, caro em tokens e expõe mais dados.
- **Text-to-SQL livre:** mais flexível, mas difícil de garantir segurança e isolamento por usuário.

## Consequências
- O conjunto de ferramentas de consulta precisa cobrir as perguntas comuns; perguntas fora delas recebem uma resposta honesta de que o assistente ainda não sabe responder.
- Os evals verificam se o número da resposta bate com o banco.
