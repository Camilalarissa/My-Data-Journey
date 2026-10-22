# 🛠️ Cheat Sheet: Principais Comandos no Databricks

Guia de referência rápida com os comandos mais utilizados no dia a dia do **Databricks**, cobrindo Comandos Mágicos, Databricks Utilities (`dbutils`), PySpark e Databricks SQL / Delta Lake.

---

## 📑 Índice
1. [Comandos Mágicos (Magic Commands)](#1-comandos-mágicos-magic-commands)
2. [Databricks Utilities (`dbutils`)](#2-databricks-utilities-dbutils)
3. [PySpark - Leitura, Transformação e Escrita](#3-pyspark---leitura-transformação-e-escrita)
4. [Databricks SQL & Manutenção Delta Lake](#4-databricks-sql--manutenção-delta-lake)

---

## 1. ✨ Comandos Mágicos (Magic Commands)

Utilizados na primeira linha de uma célula do notebook para alterar a linguagem de execução ou executar utilitários de sistema.

```python
%python
# Alterna a célula para execução em Python (padrão em notebooks PySpark)
print("Executando em Python")

-- Alterna a célula para executar consultas SQL diretamente
SELECT * FROM catalogo.schema.tabela_vendas LIMIT 10;

# Executa comandos diretamente no terminal Linux do nó driver do cluster
%sh ls -lh /dbfs/

# Navega pelo sistema de arquivos (DBFS / Volumes)
%fs ls dbfs:/user/hive/warehouse/

%md
# Título em Markdown
Texto explicativo formatado para documentação dentro do notebook.

## 2. 🧰 Databricks Utilities (dbutils)
Ferramenta nativa do Databricks para interagir com o sistema de arquivos, gerenciar segredos, manipular widgets e controlar a execução de notebooks.

📁 Sistema de Arquivos (dbutils.fs)

# Listar arquivos e diretórios
dbutils.fs.ls("dbfs:/mnt/dados_bronze/")

# Criar diretórios
dbutils.fs.mkdirs("dbfs:/tmp/teste_pipeline/")

# Copiar arquivos ou diretórios
dbutils.fs.cp(
    "dbfs:/tmp/arquivo_origem.csv", "dbfs:/mnt/dados_bronze/arquivo_destino.csv"
)

# Mover ou renomear arquivos
dbutils.fs.mv(
    "dbfs:/tmp/dados_temp.parquet", "dbfs:/mnt/dados_silver/dados_finais.parquet"
)

# Remover arquivos (recurse=True apaga pastas não vazias)
dbutils.fs.rm("dbfs:/tmp/teste_pipeline/", recurse=True)

# Visualizar os primeiros bytes de um arquivo
dbutils.fs.head("dbfs:/mnt/dados_bronze/vendas.json", 1000)

🔐 Gerenciamento de Segredos (dbutils.secrets)
# Listar todos os escopos de segredos disponíveis
dbutils.secrets.listScopes()

# Listar as chaves disponíveis dentro de um escopo
dbutils.secrets.list("escopo-key-vault")

# Buscar o valor de um segredo (ex: senha de banco ou token de API)
senha_banco = dbutils.secrets.get(
    scope="escopo-key-vault", key="senha-postgres-prod"
)


🎛️ Parâmetros e Widgets de Interface (dbutils.widgets)

# Criar uma caixa de texto para entrada manual de parâmetro
dbutils.widgets.text("data_processamento", "2026-10-22", "Data de Referência")

# Criar um menu suspenso (dropdown)
dbutils.widgets.dropdown(
    "ambiente", "dev", ["dev", "homolog", "prod"], "Ambiente de Execução"
)

# Ler o valor preenchido no widget
data_ref = dbutils.widgets.get("data_processamento")
env = dbutils.widgets.get("ambiente")

# Remover todos os widgets da tela
dbutils.widgets.removeAll()

🏃 Orquestração de Notebooks (dbutils.notebook)

# Executar outro notebook passando parâmetros e aguardando o retorno (timeout em segundos)
resultado = dbutils.notebook.run(
    path="./ingestao_bronze",
    timeout_seconds=600,
    arguments={"data_referencia": "2026-10-22"},
)

# Encerrar a execução do notebook atual e retornar um valor para o notebook chamador
dbutils.notebook.exit("Processamento concluído com sucesso!")


## 3. ⚡ PySpark - Leitura, Transformação e Escrita
Operações fundamentais de manipulação de DataFrames em Spark no Databricks.

from pyspark.sql.functions import col, current_timestamp, from_utc_timestamp

# Leitura de arquivo CSV com inferência de esquema
df_csv = (
    spark.read.format("csv")
    .option("header", "true")
    .option("inferSchema", "true")
    .load("dbfs:/mnt/landing/vendas.csv")
)

# Leitura de tabela Delta Lake
df_delta = spark.read.table("catalogo.bronze.intelipost_status")

# Ingestão Incremental com Auto Loader (cloudFiles)
df_autoloader = (
    spark.readStream.format("cloudFiles")
    .option("cloudFiles.format", "json")
    .option("cloudFiles.schemaLocation", "dbfs:/mnt/checkpoints/schema_vendas")
    .load("dbfs:/mnt/landing/vendas_json/")
)

🔄 Transformações Comuns

# Criar nova coluna, renomear e filtrar
df_transformado = (
    df_delta.withColumn(
        "data_brt", from_utc_timestamp(col("event_date"), "GMT-3")
    )
    .withColumn("ingested_at", current_timestamp())
    .withColumnRenamed("order_number", "codigo_remessa")
    .filter(col("status") == "ENTREGUE")
    .dropDuplicates(["codigo_remessa"])
)

📤 Escrita de Dados
# Escrita em lote (Batch) em formato Delta Lake (Append)
(
    df_transformado.write.format("delta")
    .mode("append")
    .saveAsTable("catalogo.silver.vendas_limpas")
)

# Escrita em fluxo continuo (Streaming) com Auto Loader
(
    df_autoloader.writeStream.format("delta")
    .outputMode("append")
    .option("checkpointLocation", "dbfs:/mnt/checkpoints/silver_vendas")
    .toTable("catalogo.silver.vendas_streaming")
)

## 4. 🛢️ Databricks SQL & Manutenção Delta Lake
Comandos executados em células %sql ou via SQL Warehouses para manipulação de tabelas e otimização de storage.

📐 Otimização e Compactação de Arquivos (Small Files Problem)

-- Otimiza o tamanho dos arquivos em disco mesclando arquivos pequenos
OPTIMIZE catalogo.silver.vendas_limpas;

-- Otimiza e aplica ordenação multidimensional (Z-Order) para acelerar consultas por colunas específicas
OPTIMIZE catalogo.silver.vendas_limpas
ZORDER BY (codigo_remessa, data_brt);

🧹 Limpeza de Histórico (Vacuum)

-- Remove arquivos de dados antigos que não são mais referenciados pelo log de transações Delta
-- (Respeita o período de retenção padrão de 7 dias)
VACUUM catalogo.silver.vendas_limpas;

-- Desativa a trava de retenção para executar VACUUM de 0 horas (Uso apenas em desenvolvimento!)
SET spark.databricks.delta.vacuum.parallelDelete.enabled = true;
SET spark.databricks.delta.retentionDurationCheck.enabled = false;
VACUUM catalogo.silver.vendas_limpas RETAIN 0 HOURS;

### 🔀 Upsert Incremental (MERGE INTO)

-- Atualiza registros existentes e insere novos registros (Camada Silver)
MERGE INTO catalogo.silver.clientes AS destino
USING catalogo.bronze.stg_clientes AS origem
ON destino.id_cliente = origem.id_cliente
WHEN MATCHED THEN
  UPDATE SET 
    destino.nome = origem.nome,
    destino.email = origem.email,
    destino.updated_at = origem.updated_at
WHEN NOT MATCHED THEN
  INSERT (id_cliente, nome, email, updated_at)
  VALUES (origem.id_cliente, origem.nome, origem.email, origem.updated_at);

### Viagem no Tempo (Time Travel)

-- Consultar o histórico de alterações e transações da tabela
DESCRIBE HISTORY catalogo.silver.vendas_limpas;

-- Consultar a tabela exatamente como ela era em uma versão anterior
SELECT * FROM catalogo.silver.vendas_limpas VERSION AS OF 3;

-- Consultar a tabela como ela estava em um horário específico
SELECT * FROM catalogo.silver.vendas_limpas TIMESTAMP AS OF '2026-10-20T10:00:00.000Z';

-- Restaurar a tabela para uma versão anterior
RESTORE TABLE catalogo.silver.vendas_limpas TO VERSION AS OF 2;

###




