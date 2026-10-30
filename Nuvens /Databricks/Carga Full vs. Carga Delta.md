# 🔄 Carga Full vs. Carga Delta: Fundamentos, Lógica e Aplicação em Engenharia de Dados

A escolha da estratégia de ingestão e processamento de dados entre **Carga Full** e **Carga Delta** define a arquitetura de desempenho, o custo de infraestrutura e a latência das informações em um Data Lake, Data Warehouse ou Data Lakehouse.

---

## 1. 🌕 Carga Full (Full Load / Carga Total)

### 📌 O que é e Lógica de Funcionamento
A **Carga Full** lê e transfere a totalidade do conjunto de dados da origem para o destino em **toda execução do pipeline**, independentemente de os dados terem sido alterados ou não.

* **Estratégia de Destino:** O pipeline executa um `TRUNCATE + INSERT` (limpa a tabela de destino e insere tudo novamente) ou aciona um `OVERWRITE` de partição/diretório no armazenamento de arquivos (como Parquet ou Delta Lake).
* **Ausência de Estado (*Stateless*):** O orquestrador (ex: Azure Data Factory) não precisa memorizar quando o pipeline rodou pela última vez ou qual foi o último registro lido.


[ Banco Origem ] ──── Read All ────► [ Pipeline ETL ] ──── Overwrite ────► [ Tabela Destino ]
(1.000.000 linhas)                   (1.000.000 linhas)                   (1.000.000 linhas)

### 🔹 Casos de Uso Recomendados
* **Tabelas de Dimensão Pequenas:** Tabelas de catálogo, status de domínio, países, categorias de produto (baixo volume de linhas).
* **Origens Sem Colunas Temporais:** Sistemas legados que não possuem colunas de auditoria (`created_at`, `updated_at`) ou identificadores sequenciais.
* **Reprocessamento Histórico / Disaster Recovery:** Necessidade de reconstruir a camada de dados a partir do zero.

---

## 2. ⚡ Carga Delta (Incremental Load / Carga Incremental)

### 📌 O que é e Lógica de Funcionamento
A **Carga Delta** extrai, carrega e processa **apenas os registros novos, alterados ou excluídos** que surgiram desde a última execução bem-sucedida do pipeline.

* **Estratégia de Destino:** Em vez de sobrescrever a tabela inteira, o destino recebe apenas os novos registros via `APPEND` (apenas inserção) ou via `MERGE INTO` / `UPSERT` (atualização dos registros existentes + inserção dos novos).
* **Necessidade de Estado (*Stateful*):** Exige um mecanismo para rastrear o ponto de parada da carga anterior (*watermark*).

[ Banco Origem ] ── Read Delta ──► [ Pipeline ETL ] ── Merge/Append ──► [ Tabela Destino ]
(Apenas novos/alterados)             (ex: 5.000 linhas)               (Atualiza 5k de 1 milhão)


### 🔹 Principais Mecanismos para Identificar o Delta

1. **Watermark / Colunas de Controle:**
   * O pipeline consulta uma tabela de controle que guarda o valor máximo da última execução (`last_watermark_value`).
   * Executa a busca na origem filtrando pelo valor posterior:
     ```sql
     SELECT * FROM origem.vendas 
     WHERE updated_at > '2026-10-30 08:00:00';
     ```
2. **CDC (Change Data Capture):**
   * O banco de dados de origem lê diretamente o log de transações do SGBD (ex: Binlog no MySQL, WAL no PostgreSQL, CDC no SQL Server) e publica os eventos de `INSERT`, `UPDATE` e `DELETE` em tempo real.
3. **Log de Modificação por Partição:**
   * Leitura de pastas baseadas no caminho da data de chegada no Data Lake (ex: `/ano=2026/mes=10/dia=30/`).

### 🔹 Casos de Uso Recomendados
* **Tabelas Fato de Grande Volume:** Vendas, transações financeiras, logs de acessos, eventos de rastreamento logístico.
* **Pipelines Near Real-Time / Streaming:** Ingestão contínua com latência de minutos ou segundos.

---

## 📊 3. Tabela Comparativa (Formatada para GitHub)

| Categoria | 🌕 Carga Full | ⚡ Carga Delta |
| :--- | :--- | :--- |
| **Volume de Leitura** | Lê 100% dos dados da origem a cada execução. | Lê apenas as modificações desde a última carga. |
| **Complexidade de Código** | Baixa (Não requer gestão de estado ou chaves). | Média a Alta (Exige Watermarks, CDC ou cláusula `MERGE`). |
| **Uso de Recursos (Rede/CPU)** | Alto (Transfere grandes volumes desnecessariamente). | Otimizado (Transfere apenas a diferença útil). |
| **Integridade dos Dados** | Auto-corretiva (Apaga e recria o estado atualizado). | Requer tratamento para registros duplicados ou fora de ordem. |
| **Operação de Gravação** | `OVERWRITE` / `TRUNCATE + INSERT` | `APPEND` / `MERGE INTO` (Upsert) |
| **Gestão de Exclusões (Deletes)** | Trata automaticamente (se sumiu da origem, some do destino). | Exige colunas de *Soft Delete* (`is_deleted=true`) ou CDC. |
| **Ideal Para** | Tabelas pequenas, dimensões e tabelas de domínio. | Tabelas fato, histórico transacional e Big Data. |

---

## 🛠️ Exemplo Prático em SQL (Gravação da Carga Delta)

Em arquiteturas Lakehouse (Databricks / Delta Lake), a carga delta é consolidada utilizando a instrução `MERGE INTO` para evitar duplicidade na camada Silver:

````sql
MERGE INTO silver.vendas AS destino
USING bronze.stg_vendas_delta AS origem
ON destino.id_venda = origem.id_venda
WHEN MATCHED AND origem.updated_at > destino.updated_at THEN
  UPDATE SET 
    destino.status = origem.status,
    destino.valor = origem.valor,
    destino.updated_at = origem.updated_at
WHEN NOT MATCHED THEN
  INSERT (id_venda, status, valor, created_at, updated_at)
  VALUES (origem.id_venda, origem.status, origem.valor, origem.created_at, origem.updated_at);

