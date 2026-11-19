<p align="center"><img src="docs/marca/logo-negativo.svg" alt="Liga" width="360"></p>

<p align="center"><b>Assistente financeiro com IA para o dia a dia.</b><br>
Registre e consulte seus gastos conversando, pela web ou pelo Telegram.</p>

---

> 🚧 Em construção.

## O que é

O Liga é um assistente financeiro pessoal. Em vez de preencher planilhas, você conversa:

- *"gastei 100 reais na Nike"* → o Liga entende o valor, identifica que é uma loja de roupas e registra na categoria certa.
- *"quanto gastei com comida esse mês?"* → o Liga consulta seus lançamentos e responde com o total real.

Tudo isso também aparece num painel gerencial com a visão unificada das suas entradas e saídas.

> **Por que "liga"?** A liga é o elástico que segura o dinheiro organizado. E ligar é conectar: web, Telegram e áudio, tudo ligado no mesmo assistente.

## Destaques de engenharia

- **Agente de IA** com tool calling, memória e guardrails (LangGraph)
- **RAG** com embeddings e pgvector para classificar estabelecimentos
- **Classificação em camadas**: histórico → embeddings → LLM só quando precisa
- **Os números vêm do banco, nunca do LLM**
- **Eval harness** medindo a qualidade do agente a cada mudança
- **Observabilidade**: traces com tokens, latência e erros
- **Multicanal**: um único agente para web e Telegram
- **Custo zero por design**: 100% em planos gratuitos, sem cartão cadastrado

## Stack

Angular (PWA) · Python + FastAPI · LangGraph · Groq · Cloudflare Workers AI · PostgreSQL (Neon) + pgvector · Redis · Clerk · Telegram · Vercel

## Documentação

- [Escopo do projeto](docs/escopo.md)
- [Decisões de arquitetura (ADRs)](docs/decisoes/README.md)
- [Identidade visual](docs/marca/README.md)

## Autor

Arthur Eduardo · [Portfólio](https://portfolio-arthur-eduardo.vercel.app) · [GitHub](https://github.com/ArthurEdu05)
