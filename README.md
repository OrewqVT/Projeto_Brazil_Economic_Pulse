# Brazil Economic Pulse

Projeto de engenharia de dados que coleta séries econômicas do Banco Central do Brasil (BCB), armazena o histórico bruto, transforma os dados em indicadores e orquestra tudo com Airflow.

## Perguntas que o projeto deve responder

- Como a Selic se comportou nos últimos 12 meses?
- Qual foi a inflação de serviços acumulada no ano?
- Como a cotação de compra do dólar se relaciona com a taxa de juros?

## Arquitetura

- **Fonte:** API pública do SGS (Sistema Gerenciador de Séries Temporais) do BCB. Não exige chave de acesso.
- **Bronze:** resposta original da API, salva sem alterações.
- **Silver:** dados tipados e deduplicados, em DuckDB e Parquet.
- **Gold:** indicadores e tabela mensal consolidada, com modelos e testes em dbt-duckdb.
- **Orquestração:** Airflow, com agendamento diário, retries e backfill.
- **Entrega:** dashboard em Streamlit, ambiente com Docker Compose e CI com pytest no GitHub Actions.

## Séries escolhidas

| Série | Código | Periodicidade | Unidade | Fonte |
| --- | --- | --- | --- | --- |
| Taxa de juros - Selic | 11 | Diária | Percentual ao dia (% a.d.) | [Portal BCB](https://dadosabertos.bcb.gov.br/dataset/11-taxa-de-juros---selic) |
| IPCA - Serviços | 10844 | Mensal | Variação percentual mensal | [Portal BCB](https://dadosabertos.bcb.gov.br/dataset/10844-indice-de-precos-ao-consumidor-amplo-ipca---servicos) |
| Taxa de câmbio - Livre - Dólar americano (compra) | 10813 | Diária | Taxa, unidade monetária corrente por dólar americano | [Portal BCB](https://dadosabertos.bcb.gov.br/dataset/10813-taxa-de-cambio---livre---dolar-americano-compra) |

Observação: o código 10813 é a cotação de compra. A cotação de venda tem outro código.

## Limites da API do BCB

- Consultas por período podem cobrir no máximo 10 anos. Para períodos maiores, divida em blocos.
- O endereço que retorna os últimos N valores aceita no máximo 20.
- Os dados seguem a licença ODbL, então a fonte deve ser citada.

## Roteiro de sprints

1. **Ingestão Bronze:** buscar as séries com retry e salvar o JSON bruto.
2. **Silver com DuckDB:** tipar, deduplicar e garantir que rodar duas vezes gera o mesmo resultado.
3. **Gold e modelagem:** indicadores e testes dbt.
4. **Orquestração com Airflow:** DAG agendada, retries e backfill.
5. **Qualidade e observabilidade:** checagens de frescor, volume e valores, com falha da DAG quando algo estiver errado.
6. **Produto e portfólio:** Streamlit, Docker Compose, testes e CI.

## Status

Em andamento. Sprint 1 em desenvolvimento.

## Licença

A definir.
