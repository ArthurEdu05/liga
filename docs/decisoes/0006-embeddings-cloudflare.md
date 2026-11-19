# 0006 — Embeddings via Cloudflare Workers AI

- **Status:** Aceita
- **Data:** 2026-11-19

## Contexto
A classificação de estabelecimentos e a busca semântica precisam de embeddings. Rodar o modelo dentro da função (com PyTorch) estoura o limite de tamanho de pacote da Vercel (ADR 0003).

## Decisão
Gerar embeddings pela API do **Cloudflare Workers AI**, modelo **`@cf/baai/bge-m3`** (multilíngue, bom em português, também disponível no Hugging Face). Vetores armazenados no pgvector (ADR 0004).

## Alternativas consideradas
- **Modelo local com PyTorch:** não cabe na função.
- **Modelo pequeno em ONNX dentro da função:** possível, mas aumenta o cold start e o tamanho do pacote.
- **APIs que usam dados para treino:** descartadas.

## Consequências
- A cota diária gratuita é muito maior que o uso previsto (nomes de estabelecimentos têm poucos tokens).
- No plano gratuito, ao exceder a cota a requisição falha, sem cobrança; a classificação cai para a camada seguinte.
- Trocar o modelo de embeddings exige recalcular os vetores armazenados.
