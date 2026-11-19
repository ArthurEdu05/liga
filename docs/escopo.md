# Liga — Escopo do projeto

> A liga é o elástico que segura o dinheiro organizado. E ligar é conectar: web, Telegram e áudio, tudo ligado no mesmo assistente.

## 1. Visão

O Liga é um assistente financeiro pessoal com IA. O usuário registra e consulta suas entradas e saídas **conversando**, pelo chat da web ou pelo Telegram, por texto ou áudio, e acompanha tudo num **painel gerencial** unificado. Registrar um gasto tem que levar segundos, de qualquer lugar, sem ser cansativo no dia a dia.

Exemplos:
- "gastei 100 reais na Nike" → o Liga identifica o valor e o estabelecimento, entende que é uma loja de roupas e registra na categoria certa.
- "quanto gastei com comida esse mês?" → o Liga consulta os lançamentos e responde com o total real.

## 2. Objetivo do projeto

Ser o projeto principal de portfólio para vagas de **AI Engineer, Full Stack com IA e Backend com LLMs**, em empresas ágeis e com cultura AI-first. O projeto precisa provar:
1. construção de produto de ponta a ponta (front, back, banco, deploy);
2. integração séria de IA (agentes, RAG, avaliação, observabilidade);
3. decisões de engenharia conscientes (custo, segurança, arquitetura), registradas em `docs/decisoes/`.

## 3. Funcionalidades

**Núcleo**
- Registro de entradas e saídas (manual e por conversa)
- Contas, categorias e painel gerencial com gráficos
- Multi-usuário: qualquer pessoa cria conta e usa

**Assistente de IA**
- Registro por linguagem natural, com extração de valor, data, estabelecimento, forma de pagamento e categoria
- Consultas sobre os próprios gastos
- Confirmação com opção de desfazer; correções do usuário viram aprendizado
- Registro por áudio

**Canais**
- Chat dentro da plataforma web
- Bot no Telegram, vinculado à conta do usuário por código de uso único

**Entradas automáticas**
- Nota fiscal pelo QR Code da NFC-e (foto como plano B)
- Importação de extrato bancário (OFX/CSV) sem duplicar lançamentos

**Brasilidades**
- Vários cartões com fatura, compras parceladas, várias contas
- Gastos recorrentes e assinaturas

**Inteligência proativa**
- Orçamento por categoria e alertas

## 4. Stack

| Camada | Tecnologia |
|---|---|
| Front | Angular (PWA, celular e PC) |
| Backend | Python + FastAPI |
| Hospedagem | Vercel (front e backend serverless) |
| Agente de IA | LangGraph |
| LLM | Groq |
| Áudio | Whisper via Groq |
| Embeddings | Cloudflare Workers AI (bge-m3) |
| Banco de dados | PostgreSQL no Neon + pgvector |
| Cache e cotas | Redis |
| Autenticação | Clerk |
| Mensageria | Telegram Bot API |
| Portfólio de IA | Hugging Face (dataset dos evals e demo) |
| Ferramentas Python | uv, Pydantic, SQLAlchemy, Alembic, pytest |

## 5. Conceitos que o projeto demonstra

| Conceito | Onde aparece | O que prova |
|---|---|---|
| Agente com tool calling | O LLM decide qual ferramenta chamar (registrar, consultar, corrigir) | Constrói agentes, não só chatbots |
| Agent harness / context engineering | Loop do agente, memória, limite de passos, guardrails, tratamento de erros | Entende o que fica em volta do modelo |
| Saída estruturada | O LLM preenche um schema Pydantic | Usa IA de forma confiável e tipada |
| Embeddings + RAG | Classificação por similaridade e busca semântica nos lançamentos | Domina recuperação vetorial |
| Classificação em camadas | Histórico → embeddings → LLM só quando precisa | Otimiza custo e latência |
| Guardrails numéricos | Valores saem do SQL, nunca do LLM | Sabe onde a IA não deve ser usada |
| Memória e aprendizado | Correções do usuário viram regra | Sistemas que melhoram com o uso |
| Multimodal | Áudio e foto de nota fiscal | Integra mais de um tipo de entrada |
| Eval harness | Dataset de frases com resultado esperado, rodando no CI | Mede qualidade em vez de achismo |
| Observabilidade de LLM | Traces com tokens, latência e erros | Pensa em IA em produção |
| Arquitetura multicanal | Um agente, vários adaptadores de canal | Design de sistema desacoplado |
| Multi-tenant + segurança | `user_id` do JWT, isolamento, webhook validado | Segurança, inclusive contra prompt injection |
| Rate limit e cotas | Redis controlando uso de IA por usuário | Protege recursos compartilhados |
| Degradação elegante | Sem IA, o app segue funcionando no modo manual | Sistemas resilientes |
| Custo zero por design | Tudo em planos gratuitos, sem cartão | Decisões conscientes de custo e infraestrutura |

## 6. Arquitetura

```
 Angular (PWA) ──┐
                 ├──► FastAPI (Vercel Functions)
 Telegram ───────┘        │
                          ├── Auth (JWT do Clerk) + cotas (Redis)
                          ├── API de lançamentos e painel
                          ├── Adaptadores de canal (web, Telegram)
                          └── Agente LangGraph
                                 ├── Ferramentas → PostgreSQL (Neon)
                                 ├── RAG → pgvector + embeddings (Cloudflare)
                                 ├── LLM → Groq
                                 └── Áudio → Whisper (Groq)

 Em paralelo: eval harness no CI · traces de custo e latência
```

### Fluxo de registro ("gastei 100 na Nike")
1. O canal (web ou Telegram) envia a mensagem ao backend, que identifica o usuário.
2. O agente extrai os dados num schema estruturado (valor, data, estabelecimento, pagamento).
3. A categoria é resolvida em camadas: histórico do usuário → similaridade por embeddings → LLM.
4. A ferramenta de registro grava no banco com `origem` do canal.
5. O agente confirma ("R$ 100 em Roupas, Nike, hoje ✓ desfazer"). Correções viram memória.

### Fluxo de consulta ("quanto gastei com comida esse mês?")
1. O agente interpreta a pergunta e chama uma ferramenta tipada, por exemplo `consultar_gastos(categoria, periodo)`.
2. A ferramenta executa SQL filtrando pelo `user_id`.
3. O agente redige a resposta com os números retornados, sem calcular nada por conta própria.

## 7. Restrições

- **Custo zero:** só planos gratuitos, sem cartão cadastrado. No pior caso, "limite atingido", nunca uma fatura.
- **Sempre disponível:** serverless; funciona igual depois de meses parado.
- **Público e seguro:** cadastro aberto, dados isolados por usuário, cotas de IA por usuário, LGPD básica (política de privacidade, exportar e excluir conta, nenhum dado pessoal enviado ao LLM).
- **Pronto para o futuro:** banco modelado para cartões, parcelas e recorrências; camadas de LLM e de canais desacopladas.

## 8. Roadmap

| Fase | Entrega |
|---|---|
| 0 | Fundação: repositório, ambiente, banco, esqueleto do FastAPI e do Angular, primeiro deploy |
| 1 | Núcleo financeiro: registro manual, contas, categorias, painel básico |
| 2 | Assistente de IA no chat, com evals e observabilidade desde o primeiro prompt |
| 3 | Áudio |
| 4 | Bot no Telegram |
| 5 | Nota fiscal e importação de extrato |
| 6 | Cartões, faturas, parcelas e recorrências |
| 7 | Orçamentos e alertas |
| 8 | Login, controle de acesso e deploy público |

**Regra:** nenhum deploy público com dados reais antes da fase 8.

## 9. Identidade visual

Definida e documentada em [`docs/marca/`](marca/README.md):
- Logo: "liga" em minúsculas (Sora Bold), com o "l" terminando em curva e o "i" formado por um maço de notas com cifrões e uma liga amarela.
- Ícone do app: só o maço com a liga, em quadrado verde-escuro arredondado.
- Paleta: Verde Liga `#0E3B2C`, Verde nota `#2E8B57`, Amarelo liga `#F2B705`, Creme `#FAF7EF`.
- Fonte: Sora.

