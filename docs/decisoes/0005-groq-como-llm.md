# 0005 — Groq como provedor de LLM e de transcrição

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
O agente precisa de um LLM com tool calling e o registro por voz precisa de transcrição. Tudo gratuito e sem uso dos dados dos usuários para treino, já que são dados financeiros de terceiros.

## Decisão
**Groq** como provedor do LLM e do **Whisper** (transcrição). A chamada ao LLM fica atrás de uma interface própria no código; o modelo e o provedor são configuração.

## Alternativas consideradas
- **Gemini (free tier):** o conteúdo enviado no plano gratuito é usado para melhorar os produtos do Google; inadequado para dados financeiros de usuários.
- **Mistral (plano Experiment):** mesmo problema de uso para treino.
- **Cerebras, OpenRouter, GitHub Models:** gratuitos e compatíveis com a API da OpenAI; ficam como opções futuras atrás da mesma interface.

## Consequências
- Os limites de requisições por minuto e por dia exigem cotas por usuário e classificação em camadas para economizar chamadas.
- O modelo específico do Groq é escolhido e reavaliado pelos evals (extração correta, ferramenta correta, latência).
- Trocar de provedor no futuro não exige reescrever o agente.
