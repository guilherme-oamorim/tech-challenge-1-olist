# Tech Challenge — Fase 1 | Pós-Tech Data Analytics (FIAP)

Análise do desempenho do e-commerce brasileiro a partir do **Brazilian E-Commerce Public Dataset by
Olist** (Kaggle) — ~100 mil pedidos entre 2016 e 2018 — com foco em recomendações acionáveis para
investidores e acionistas.

## Pergunta norteadora

> O que levou o setor a entrar em um platô de crescimento a partir de 2018, e quais ações podem
> retomar a trajetória de crescimento observada em 2017?

## Estrutura do repositório

```
Tech Challenge 1/
├── data/                            # CSVs brutos do Kaggle + tabela de tradução de categoria
├── data/clean/                   # Saída do notebook de tratamento (.parquet + dicionário)
├── Tratamento_merge.ipynb            # 01 — tratamento, merge e governança (fonte única de verdade)
├── TechChallenge-FIAP.ipynb          # 02 — análise: os gráficos da apresentação + conclusão
```

### `Tratamento_merge.ipynb` — tratamento e governança

Único notebook do repositório que lê os CSVs brutos. Nesta ordem:

1. Carga dos 8 datasets originais + inventário de qualidade (nulos, duplicatas)
2. Tradução e nome amigável da categoria de produto
3. Conversão das colunas de data
4. Filtro do período válido — **jan/2017 a ago/2018** (exclui 2016 e set–out/2018 por volume
   residual/coleta incompleta, com a evidência que justifica o corte)
5. Propagação do filtro para as tabelas de evento (itens, pagamentos, reviews, clientes) —
   `products` e `sellers` não são filtrados por data, por serem cadastro
6. Agregação de itens, pagamentos e reviews no **grão de pedido**
7. Validação cruzada (itens × pagamentos) — quantifica e explica toda divergência de valor
8. Persistência em `.parquet` + dicionário das colunas derivadas

Cada decisão de tratamento está documentada no próprio notebook (tabela de decisões de governança,
logo no início).

### `TechChallenge-FIAP.ipynb` — análise

Lê exclusivamente os `.parquet` gerados pelo notebook 01 — nunca reprocessa CSV bruto. Contém só os
gráficos usados na apresentação executiva, cada um com a tabela de números correspondente exibida
antes do gráfico (para conferência e para montar slides):

| # | Gráfico | O que mostra |
|---|---------|--------------|
| 1 | Evolução mensal — Faturamento e Pedidos | O platô a partir de jan/2018, após o pico da Black Friday |
| 2 | Top 10 categorias de produto | Concentração de faturamento e volume em poucas categorias |
| 3 | Faturamento por categoria — Top 5 | 4 das 5 maiores categorias caem em 2018; Saúde & Beleza não |
| 4 | Variação de preço médio — Top 5 | Preço testado e **descartado** como causa da queda |
| 5 | Frete — sinal comum entre categorias | Choque de custo de frete em **jun–jul/2018**, ligado à greve dos caminhoneiros (mai/2018) |
| 6 | Vendedores — distribuição mensal | Métrica cumulativa (6) validada com métricas não cumulativas (6.1) — base de vendedores está saudável, não em deterioração |
| 7 | Atraso de entrega e satisfação | Pico sistêmico de atraso/insatisfação em **fev–mar/2018**, antes e independente da greve |

## Conclusão do estudo

O platô **não nasce de um problema interno da Olist** — preço, base de vendedores e volume de
pedidos foram testados e descartados como causa. O que impediu a retomada do ritmo de crescimento de
2017 foi uma sequência de **choques logísticos externos** ao longo de 2018:

1. **Fev–mar/2018** — atraso de entrega e satisfação do cliente pioram de forma aguda e sistêmica nas
   5 categorias que respondem por 40% do faturamento. Causa raiz **não identificada com confiança**
   (diesel e Carnaval foram testados como hipótese e descartados) — fica como recomendação de
   investigação futura.
2. **Jun–jul/2018** — choque de custo de frete com causa confirmada: a greve dos caminhoneiros
   (21–30/mai/2018). O custo só aparece na fatura dos pedidos despachados após o fim do bloqueio, por
   isso o pico é em junho/julho, não em maio.

**Recomendações:** diversificar transportadoras/rotas; reforçar SLA e comunicação proativa de prazo
ao cliente; investir em pesquisa de mercado do lado do cliente; investigar internamente a causa raiz
do episódio de fev–mar/2018.

## Como reproduzir

Requer Python 3.11+ e as bibliotecas: `pandas`, `numpy`, `plotly`, `pyarrow` (leitura/escrita de
`.parquet`) e `kaleido` (exportação de figuras, opcional).

```bash
pip install pandas numpy plotly pyarrow kaleido
```

Rodar **nesta ordem**, a partir da raiz do repositório:

1. `Tratamento_merge.ipynb` — gera `dados_tratados/*.parquet`
2. `TechChallenge-FIAP.ipynb` — lê os parquets e produz os gráficos

> Os CSVs brutos do Kaggle já estão em `Dados/`. `Dados/product_category_name_translation.csv` é a
> tabela oficial de tradução de categoria do dataset Olist (não incluída no download padrão do
> Kaggle usado neste projeto).

## Fonte dos dados

[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (Kaggle).
