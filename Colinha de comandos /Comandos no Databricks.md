# 🛠️ Cheat Sheet: Principais Comandos no Databricks

Guia de referência rápida com os comandos mais usados no dia a dia do **Databricks**. Cada seção explica **para que serve**, **quando usar** e traz exemplos prontos para copiar.

> 🧭 **Convenções deste guia**
> - Os exemplos usam o **Unity Catalog**: tabelas no formato `catalogo.schema.tabela` e arquivos em **Volumes** (`/Volumes/catalogo/schema/volume/`).
> - Caminhos antigos como `dbfs:/mnt/...` e `dbfs:/user/hive/warehouse/` são **legados**. Eles ainda aparecem em projetos antigos, mas não são o padrão recomendado.
> - 🎯 marca os tópicos cobrados na certificação **Data Engineer Associate**.

---

## 📑 Índice
1. [Comandos Mágicos (Magic Commands)](#1--comandos-mágicos-magic-commands)
2. [Databricks Utilities (`dbutils`)](#2--databricks-utilities-dbutils)
3. [PySpark: Leitura de Dados](#3--pyspark-leitura-de-dados)
4. [PySpark: Transformações](#4--pyspark-transformações)
5. [PySpark: Escrita de Dados](#5--pyspark-escrita-de-dados)
6. [Ingestão Incremental: Auto Loader e COPY INTO](#6--ingestão-incremental-auto-loader-e-copy-into)
7. [SQL: Tabelas, Views e DML](#7--sql-tabelas-views-e-dml)
8. [Delta Lake: Manutenção e Time Travel](#8--delta-lake-manutenção-e-time-travel)
9. [Unity Catalog: Objetos e Permissões](#9--unity-catalog-objetos-e-permissões)
10. [Ajustes de Performance do Spark](#10--ajustes-de-performance-do-spark)
11. [Databricks CLI e Bundles](#11--databricks-cli-e-bundles)
12. [Tabela-resumo: "Quero fazer X, uso Y"](#12--tabela-resumo-quero-fazer-x-uso-y)

---

## 1. ✨ Comandos Mágicos (Magic Commands)

Ficam **na primeira linha de uma célula** e mudam a linguagem **só daquela célula** ou executam um utilitário. A linguagem padrão do notebook continua valendo para as outras células.

| Comando | O que faz | Observação |
| :--- | :--- | :--- |
| `%python` | Executa a célula em Python | Padrão em notebooks PySpark |
| `%sql` | Executa a célula em SQL | O resultado vira o DataFrame `_sqldf` no Python |
| `%scala` / `%r` | Executa em Scala / R | |
| `%md` | Texto em Markdown | Para documentar o notebook |
| `%sh` | Comandos de shell (Linux) | Roda **só no driver** |
| `%fs` | Atalho para `dbutils.fs` | Navegar em arquivos |
| `%run` | Executa outro notebook **no mesmo contexto** | Funções e variáveis passam a existir aqui |
| `%pip` | Instala bibliotecas Python | Reinicia o Python do notebook |

```sql
%sql
-- Consulta SQL direto na célula
SELECT * FROM catalogo.silver.vendas_limpas LIMIT 10;
```

```bash
%sh
# Lista arquivos de um Volume pelo terminal do driver
ls -lh /Volumes/catalogo/bronze/landing/
```

```python
%fs ls /Volumes/catalogo/bronze/landing/
```

```python
%run ./utils/funcoes_limpeza
# Depois desta célula, as funções definidas em funcoes_limpeza ficam disponíveis aqui
```

```python
%pip install openpyxl
```

```markdown
%md
# Título em Markdown
Texto explicativo formatado para documentar o notebook.
```

> ⚠️ **Variáveis não cruzam linguagens.** Um DataFrame criado em Python não existe numa célula `%sql`. Para compartilhar, crie uma **temp view** (`df.createOrReplaceTempView("nome")`) ou grave uma tabela.

---

## 2. 🧰 Databricks Utilities (`dbutils`)

Ferramenta nativa para interagir com arquivos, segredos, widgets, notebooks e jobs. Para ver a ajuda de qualquer módulo:

```python
dbutils.help()          # lista todos os módulos
dbutils.fs.help()       # ajuda de um módulo específico
```

### 📁 Sistema de arquivos (`dbutils.fs`)

Use para **listar, copiar, mover e apagar arquivos**. Funciona com Volumes e com caminhos de nuvem (`s3://`, `abfss://`, `gs://`).

```python
# Listar arquivos e diretórios
display(dbutils.fs.ls("/Volumes/catalogo/bronze/landing/"))

# Criar diretório
dbutils.fs.mkdirs("/Volumes/catalogo/bronze/landing/teste_pipeline/")

# Copiar arquivo
dbutils.fs.cp(
    "/Volumes/catalogo/bronze/landing/origem.csv",
    "/Volumes/catalogo/bronze/landing/backup/destino.csv",
)

# Mover ou renomear
dbutils.fs.mv(
    "/Volumes/catalogo/bronze/landing/temp.parquet",
    "/Volumes/catalogo/bronze/landing/processados/final.parquet",
)

# Remover (recurse=True apaga pastas com conteúdo, use com cuidado!)
dbutils.fs.rm("/Volumes/catalogo/bronze/landing/teste_pipeline/", recurse=True)

# Ver os primeiros bytes de um arquivo
dbutils.fs.head("/Volumes/catalogo/bronze/landing/vendas.json", 1000)
```

> 🗂️ **Legado:** `dbutils.fs.mount()` e caminhos `/mnt/...` são do modelo antigo. Com Unity Catalog, use **Volumes** para arquivos e **External Locations** para storage externo.

### 🔐 Segredos (`dbutils.secrets`)

Use para **ler senhas e tokens sem escrevê-los no código**. O valor nunca aparece na saída do notebook (é exibido como `[REDACTED]`).

```python
# Listar escopos de segredos disponíveis
dbutils.secrets.listScopes()

# Listar as chaves de um escopo (só os nomes, nunca os valores)
dbutils.secrets.list("escopo-key-vault")

# Buscar o valor de um segredo
senha_banco = dbutils.secrets.get(scope="escopo-key-vault", key="senha-postgres-prod")
```

### 🎛️ Widgets e parâmetros (`dbutils.widgets`)

Criam campos no topo do notebook para **parametrizar a execução**. Quando o notebook roda dentro de um Job, os parâmetros do Job preenchem esses widgets.

```python
# Caixa de texto
dbutils.widgets.text("data_processamento", "2026-10-22", "Data de Referência")

# Lista suspensa (dropdown)
dbutils.widgets.dropdown("ambiente", "dev", ["dev", "homolog", "prod"], "Ambiente")

# Ler os valores
data_ref = dbutils.widgets.get("data_processamento")
env = dbutils.widgets.get("ambiente")

# Remover widgets
dbutils.widgets.remove("ambiente")
dbutils.widgets.removeAll()
```

Em células SQL, o widget é lido como **parâmetro nomeado** (`:nome`):

```sql
%sql
SELECT * FROM catalogo.silver.vendas_limpas
WHERE data_venda = :data_processamento;
```

### 🏃 Orquestração de notebooks (`dbutils.notebook`)

```python
# Executa outro notebook como uma execução SEPARADA, passando parâmetros
resultado = dbutils.notebook.run(
    "./ingestao_bronze",
    timeout_seconds=600,
    arguments={"data_referencia": "2026-10-22"},
)
print(resultado)  # recebe o valor devolvido pelo dbutils.notebook.exit()

# Encerra o notebook atual e devolve um valor (string) para quem o chamou
dbutils.notebook.exit("Processamento concluído com sucesso!")
```

| | `%run` | `dbutils.notebook.run()` |
| :--- | :--- | :--- |
| **Contexto** | O **mesmo** do notebook atual | Uma execução **separada** |
| **Variáveis e funções** | Ficam disponíveis no notebook atual | **Não** ficam disponíveis |
| **Parâmetros** | Não aceita parâmetros dinâmicos | Aceita `arguments` |
| **Retorno** | Nenhum | O valor do `dbutils.notebook.exit()` |
| **Uso típico** | Importar funções utilitárias | Orquestrar notebooks com lógica (if, loops) |

### 🔗 Passar valores entre tasks de um Job (`dbutils.jobs.taskValues`) 🎯

Permite que uma task **envie um valor** para as próximas tasks do mesmo Job.

```python
# Na task "ingestao": grava um valor
dbutils.jobs.taskValues.set(key="linhas_ingeridas", value=15230)

# Em uma task seguinte: lê o valor
linhas = dbutils.jobs.taskValues.get(
    taskKey="ingestao",         # nome da task que gravou
    key="linhas_ingeridas",
    default=0,
    debugValue=0,               # valor usado quando o notebook roda fora de um Job
)
```

---

## 3. ⚡ PySpark: Leitura de Dados

```python
from pyspark.sql import functions as F
```

### Tabelas

```python
# Ler uma tabela do Unity Catalog (forma mais comum)
df = spark.read.table("catalogo.bronze.intelipost_status")

# Executar SQL e receber um DataFrame
df_sql = spark.sql("SELECT * FROM catalogo.bronze.intelipost_status WHERE status = 'ENTREGUE'")
```

### Arquivos

```python
# CSV com cabeçalho e inferência de schema
df_csv = (
    spark.read.format("csv")
    .option("header", "true")
    .option("inferSchema", "true")
    .option("sep", ";")                  # separador (padrão é vírgula)
    .load("/Volumes/catalogo/bronze/landing/vendas.csv")
)

# JSON (multiLine=true quando cada arquivo é um único objeto/array JSON formatado)
df_json = (
    spark.read.format("json")
    .option("multiLine", "true")
    .load("/Volumes/catalogo/bronze/landing/eventos/")
)

# Parquet e Delta por caminho
df_parquet = spark.read.parquet("/Volumes/catalogo/bronze/landing/parquet/")
df_delta_path = spark.read.format("delta").load("s3://meu-bucket/silver/clientes/")
```

### Definindo o schema manualmente

Mais rápido e seguro que `inferSchema` em produção, porque evita ler o arquivo duas vezes e garante os tipos.

```python
schema_vendas = "id_venda INT, cliente STRING, valor DOUBLE, data_venda DATE"

df = (
    spark.read.format("csv")
    .option("header", "true")
    .schema(schema_vendas)
    .load("/Volumes/catalogo/bronze/landing/vendas.csv")
)
```

### Inspecionando o DataFrame

```python
display(df)              # tabela interativa (gráficos e Data Profile)
df.printSchema()         # estrutura: colunas e tipos
df.columns               # lista de nomes das colunas
df.count()               # número de linhas (é uma AÇÃO)
df.summary().show()      # estatísticas: count, mean, stddev, min, max, percentis
```

---

## 4. 🔄 PySpark: Transformações

> 💤 Todas as operações desta seção são **transformações (lazy)**: só montam o plano. Nada é executado até uma **ação** (`display`, `count`, `show`, `collect`, `write`).

### Colunas: selecionar, criar, renomear, remover 🎯

```python
df_transformado = (
    df
    .select("order_number", "status", "event_date", "valor")                 # escolher colunas
    .withColumn("data_brt", F.from_utc_timestamp("event_date", "America/Sao_Paulo"))  # nova coluna
    .withColumn("valor", F.col("valor").cast("double"))                      # converter tipo
    .withColumn("ingested_at", F.current_timestamp())                        # data/hora da carga
    .withColumnRenamed("order_number", "codigo_remessa")                     # renomear
    .drop("event_date")                                                      # remover coluna
)
```

### Linhas: filtrar, deduplicar, ordenar 🎯

```python
df_filtrado = (
    df_transformado
    .filter(F.col("status") == "ENTREGUE")            # ou .where(...)
    .filter(F.col("valor") > 0)
    .dropDuplicates(["codigo_remessa"])               # remove duplicatas pela chave
    .orderBy(F.desc("data_brt"))
)
```

### Tratar nulos 🎯

```python
df_limpo = (
    df
    .fillna({"valor": 0, "status": "DESCONHECIDO"})   # substituir nulos por coluna
    .dropna(subset=["codigo_remessa"])                # remover linhas sem a chave
)
```

### Condicionais (`when` / `otherwise`)

```python
df_faixa = df.withColumn(
    "faixa_valor",
    F.when(F.col("valor") >= 1000, "alto")
     .when(F.col("valor") >= 100, "medio")
     .otherwise("baixo"),
)
```

### Texto, arrays e dados aninhados 🎯

```python
df_texto = (
    df
    .withColumn("cep_prefixo", F.split(F.col("cep"), "-").getItem(0))  # dividir texto
    .withColumn("cidade", F.upper(F.trim(F.col("cidade"))))            # padronizar texto
)

# explode: transforma cada item de um array em uma linha
df_itens = df_pedidos.select("id_pedido", F.explode("itens").alias("item"))

# Acessar campos de uma estrutura aninhada (JSON)
df_itens = df_itens.select("id_pedido", "item.sku", "item.quantidade", "item.preco")
```

### Agregações 🎯

```python
df_resumo = (
    df
    .groupBy("cliente")
    .agg(
        F.count("*").alias("qtd_pedidos"),
        F.sum("valor").alias("total_gasto"),
        F.avg("valor").alias("ticket_medio"),
        F.approx_count_distinct("produto").alias("produtos_distintos_aprox"),
    )
)
```

### Joins e uniões 🎯

```python
# Inner join (padrão): só as linhas com correspondência nos dois lados
df_join = df_vendas.join(df_clientes, on="id_cliente", how="inner")

# Left join: mantém todas as vendas, mesmo sem cliente correspondente
df_left = df_vendas.join(df_clientes, on="id_cliente", how="left")

# Join com múltiplas chaves
df_multi = df_vendas.join(df_estoque, on=["sku", "id_loja"], how="left")

# Broadcast join: envia a tabela PEQUENA para todos os workers (evita shuffle)
df_broadcast = df_vendas.join(F.broadcast(df_lojas), on="id_loja", how="left")

# União de DataFrames com as mesmas colunas
df_total = df_jan.unionByName(df_fev)        # alinha pelas colunas, mantém duplicatas
```

> 💡 No **SQL**, `UNION` remove duplicatas e `UNION ALL` mantém todas as linhas. No PySpark, `union` e `unionByName` se comportam como `UNION ALL`. Para remover duplicatas, use `.distinct()` depois.

### Expor para SQL

```python
df_resumo.createOrReplaceTempView("vw_resumo_clientes")   # vive só durante a sessão
```

---

## 5. 📤 PySpark: Escrita de Dados

| Modo | O que faz |
| :--- | :--- |
| `append` | Adiciona as linhas novas |
| `overwrite` | Substitui todo o conteúdo |
| `error` / `errorifexists` | Falha se a tabela já existir (padrão) |
| `ignore` | Não faz nada se a tabela já existir |

```python
# Gravar como tabela gerenciada no Unity Catalog (Delta é o formato padrão)
(
    df_transformado.write
    .mode("append")
    .saveAsTable("catalogo.silver.vendas_limpas")
)

# Sobrescrever permitindo mudança de schema
(
    df_transformado.write
    .mode("overwrite")
    .option("overwriteSchema", "true")
    .saveAsTable("catalogo.silver.vendas_limpas")
)

# Acrescentar aceitando colunas novas (schema evolution)
(
    df_com_coluna_nova.write
    .mode("append")
    .option("mergeSchema", "true")
    .saveAsTable("catalogo.silver.vendas_limpas")
)
```

---

## 6. 📥 Ingestão Incremental: Auto Loader e COPY INTO

> 🎯 Tópico cobrado na Data Engineer Associate.

Os dois carregam **só os arquivos novos** de uma pasta, sem reprocessar o que já foi lido.

| | Auto Loader | COPY INTO |
| :--- | :--- | :--- |
| **Linguagem** | PySpark (Structured Streaming) | SQL |
| **Escala** | Milhões de arquivos | Milhares de arquivos |
| **Evolução de schema** | Automática e configurável | Com `mergeSchema` |
| **Execução** | Contínua ou em lote (`availableNow`) | Em lote, a cada execução |
| **Quando usar** | Volumes grandes e crescentes, streaming | Cargas simples e periódicas |

### Auto Loader (`cloudFiles`)

```python
df_autoloader = (
    spark.readStream.format("cloudFiles")
    .option("cloudFiles.format", "json")
    .option("cloudFiles.schemaLocation", "/Volumes/catalogo/bronze/checkpoints/schema_vendas")
    .option("cloudFiles.schemaEvolutionMode", "addNewColumns")   # aceita colunas novas
    .load("/Volumes/catalogo/bronze/landing/vendas_json/")
)

(
    df_autoloader.writeStream
    .option("checkpointLocation", "/Volumes/catalogo/bronze/checkpoints/bronze_vendas")
    .trigger(availableNow=True)      # processa tudo o que é novo e para (modo lote)
    .toTable("catalogo.bronze.vendas")
)
```

> 💡 **`checkpointLocation`** guarda o que já foi processado. É ele que garante que cada arquivo seja lido **uma única vez**. Sem `trigger(availableNow=True)`, o stream fica rodando continuamente.

### COPY INTO

```sql
-- A tabela de destino precisa existir (pode ser criada vazia, sem schema)
CREATE TABLE IF NOT EXISTS catalogo.bronze.vendas_csv;

COPY INTO catalogo.bronze.vendas_csv
FROM '/Volumes/catalogo/bronze/landing/vendas_csv/'
FILEFORMAT = CSV
FORMAT_OPTIONS ('header' = 'true', 'inferSchema' = 'true')
COPY_OPTIONS ('mergeSchema' = 'true');
```

### Ler arquivos direto no SQL (`read_files`)

```sql
SELECT *
FROM read_files(
  '/Volumes/catalogo/bronze/landing/vendas_csv/',
  format => 'csv',
  header => true
);
```

---

## 7. 🗃️ SQL: Tabelas, Views e DML

### Criar tabelas

```sql
-- Tabela gerenciada com schema explícito e Liquid Clustering
CREATE TABLE IF NOT EXISTS catalogo.silver.clientes (
  id_cliente  INT,
  nome        STRING,
  email       STRING,
  updated_at  TIMESTAMP
)
CLUSTER BY (id_cliente)
COMMENT 'Clientes limpos e deduplicados';

-- CTAS: cria a tabela a partir de uma consulta
CREATE OR REPLACE TABLE catalogo.gold.faturamento_mensal AS
SELECT date_trunc('month', data_venda) AS mes, SUM(valor) AS faturamento
FROM catalogo.silver.vendas_limpas
GROUP BY ALL;

-- Tabela EXTERNA: os dados ficam no caminho informado (exige External Location)
CREATE TABLE catalogo.bronze.legado_pedidos
LOCATION 's3://meu-bucket/legado/pedidos/';
```

### Views 🎯

| Objeto | O que é | Dados gravados? |
| :--- | :--- | :--- |
| **View** | Consulta salva, recalculada a cada leitura | ❌ Não |
| **Temp view** | View que existe só durante a sessão | ❌ Não |
| **Materialized view** | Resultado pré-calculado e atualizado de forma incremental | ✅ Sim |
| **Streaming table** | Tabela alimentada continuamente por um stream | ✅ Sim |

```sql
CREATE OR REPLACE VIEW catalogo.gold.vw_vendas_entregues AS
SELECT * FROM catalogo.silver.vendas_limpas WHERE status = 'ENTREGUE';

CREATE OR REPLACE TEMP VIEW vw_teste AS
SELECT * FROM catalogo.silver.vendas_limpas LIMIT 100;

CREATE OR REPLACE MATERIALIZED VIEW catalogo.gold.mv_faturamento_regiao AS
SELECT regiao, SUM(valor) AS faturamento
FROM catalogo.silver.vendas_limpas
GROUP BY regiao;
```

### Inserir, atualizar e apagar (DML)

```sql
INSERT INTO catalogo.silver.clientes VALUES (1, 'Ana', 'ana@email.com', current_timestamp());

-- Substitui todo o conteúdo pelo resultado da consulta
INSERT OVERWRITE catalogo.gold.faturamento_mensal
SELECT date_trunc('month', data_venda), SUM(valor)
FROM catalogo.silver.vendas_limpas
GROUP BY ALL;

UPDATE catalogo.silver.clientes SET email = lower(email);

DELETE FROM catalogo.silver.clientes WHERE email IS NULL;
```

### Upsert incremental (`MERGE INTO`) 🎯

Atualiza o que já existe e insere o que é novo, numa única operação **atômica**. É o comando mais usado para manter a camada Silver.

```sql
MERGE INTO catalogo.silver.clientes AS destino
USING catalogo.bronze.stg_clientes AS origem
ON destino.id_cliente = origem.id_cliente
WHEN MATCHED AND origem.updated_at > destino.updated_at THEN
  UPDATE SET
    destino.nome       = origem.nome,
    destino.email      = origem.email,
    destino.updated_at = origem.updated_at
WHEN NOT MATCHED THEN
  INSERT (id_cliente, nome, email, updated_at)
  VALUES (origem.id_cliente, origem.nome, origem.email, origem.updated_at)
WHEN NOT MATCHED BY SOURCE THEN
  DELETE;  -- opcional: apaga do destino o que sumiu da origem
```

### Alterar a estrutura

```sql
ALTER TABLE catalogo.silver.clientes ADD COLUMNS (telefone STRING);
ALTER TABLE catalogo.silver.clientes RENAME TO catalogo.silver.clientes_v2;
COMMENT ON TABLE catalogo.silver.clientes_v2 IS 'Clientes ativos';
DROP TABLE IF EXISTS catalogo.silver.tabela_teste;
```

---

## 8. 🏛️ Delta Lake: Manutenção e Time Travel

### Inspecionar uma tabela

```sql
DESCRIBE TABLE catalogo.silver.vendas_limpas;      -- colunas e tipos
DESCRIBE EXTENDED catalogo.silver.vendas_limpas;   -- + tipo (MANAGED/EXTERNAL), local, dono
DESCRIBE DETAIL catalogo.silver.vendas_limpas;     -- nº de arquivos, tamanho, clustering
DESCRIBE HISTORY catalogo.silver.vendas_limpas;    -- versões, operações e usuários
```

### Compactar e organizar arquivos (`OPTIMIZE`)

Resolve o **problema dos arquivos pequenos (small files)** juntando-os em arquivos maiores.

```sql
-- Compactação simples
OPTIMIZE catalogo.silver.vendas_limpas;

-- Liquid Clustering (recomendado): definir as colunas mais filtradas
ALTER TABLE catalogo.silver.vendas_limpas CLUSTER BY (codigo_remessa, data_brt);
OPTIMIZE catalogo.silver.vendas_limpas;   -- aplica o clustering

-- Z-Order (abordagem anterior; não pode ser usada junto com Liquid Clustering)
OPTIMIZE catalogo.silver.vendas_legado ZORDER BY (codigo_remessa, data_brt);
```

> 💡 Com **predictive optimization** ativada no Unity Catalog, o Databricks executa `OPTIMIZE` e `VACUUM` automaticamente nas tabelas gerenciadas.

### Limpar arquivos antigos (`VACUUM`)

Remove arquivos de dados que **não fazem mais parte da versão atual** e passaram do período de retenção (**padrão: 7 dias**).

```sql
-- Ver o que seria apagado, sem apagar
VACUUM catalogo.silver.vendas_limpas DRY RUN;

-- Executar com a retenção padrão
VACUUM catalogo.silver.vendas_limpas;
```

> ⚠️ **Depois do `VACUUM`, o time travel para versões que usavam os arquivos apagados deixa de funcionar.**

```sql
-- ⛔ SOMENTE EM DESENVOLVIMENTO: VACUUM sem retenção
-- Desliga a trava de segurança que impede retenção menor que 7 dias.
-- Pode corromper leituras em andamento e elimina todo o time travel.
SET spark.databricks.delta.retentionDurationCheck.enabled = false;
VACUUM catalogo.silver.vendas_limpas RETAIN 0 HOURS;
SET spark.databricks.delta.retentionDurationCheck.enabled = true;  -- religar a trava!
```

### Viagem no tempo (Time Travel)

```sql
-- Consultar uma versão anterior
SELECT * FROM catalogo.silver.vendas_limpas VERSION AS OF 3;

-- Consultar como estava em um horário específico
SELECT * FROM catalogo.silver.vendas_limpas TIMESTAMP AS OF '2026-10-20T10:00:00.000Z';

-- Restaurar a tabela para uma versão anterior (desfaz erros)
RESTORE TABLE catalogo.silver.vendas_limpas TO VERSION AS OF 2;
```

```python
# Equivalente em PySpark
df_v3 = spark.read.option("versionAsOf", 3).table("catalogo.silver.vendas_limpas")
```

---

## 9. 🛡️ Unity Catalog: Objetos e Permissões

> 🎯 Tópico cobrado na Data Engineer Associate.

### Hierarquia: `catálogo.schema.objeto`

```sql
CREATE CATALOG IF NOT EXISTS catalogo;
CREATE SCHEMA  IF NOT EXISTS catalogo.bronze;
CREATE VOLUME  IF NOT EXISTS catalogo.bronze.landing;   -- para ARQUIVOS (CSV, JSON, imagens)

USE CATALOG catalogo;     -- define o catálogo padrão da sessão
USE SCHEMA bronze;        -- define o schema padrão da sessão

SHOW CATALOGS;
SHOW SCHEMAS IN catalogo;
SHOW TABLES IN catalogo.bronze;
SHOW VOLUMES IN catalogo.bronze;
```

### Permissões (`GRANT` / `REVOKE`)

Para **ler** uma tabela, o usuário precisa de permissão **nos três níveis**. Dê permissões preferencialmente a **grupos**, e não a pessoas.

```sql
GRANT USE CATALOG ON CATALOG catalogo                     TO `analistas`;
GRANT USE SCHEMA  ON SCHEMA  catalogo.silver              TO `analistas`;
GRANT SELECT      ON TABLE   catalogo.silver.vendas_limpas TO `analistas`;

-- SELECT no SCHEMA vale para todas as tabelas dentro dele (inclusive futuras)
GRANT SELECT ON SCHEMA catalogo.gold TO `diretoria`;

-- Permitir escrita
GRANT MODIFY ON TABLE catalogo.silver.vendas_limpas TO `engenheiros`;

-- Ler e escrever arquivos de um Volume
GRANT READ VOLUME, WRITE VOLUME ON VOLUME catalogo.bronze.landing TO `engenheiros`;

-- Retirar acesso
REVOKE SELECT ON TABLE catalogo.silver.vendas_limpas FROM `analistas`;

-- Consultar quem tem acesso
SHOW GRANTS ON TABLE catalogo.silver.vendas_limpas;
```

| Privilégio | Permite |
| :--- | :--- |
| `USE CATALOG` / `USE SCHEMA` | "Entrar" no catálogo / schema (pré-requisito para tudo) |
| `SELECT` | Ler tabelas e views |
| `MODIFY` | Inserir, atualizar e apagar dados |
| `CREATE TABLE` / `CREATE SCHEMA` | Criar objetos dentro do schema / catálogo |
| `READ VOLUME` / `WRITE VOLUME` | Ler / gravar arquivos em um Volume |
| `ALL PRIVILEGES` | Todos os privilégios do objeto |

### Gerenciada vs. Externa

| | Gerenciada (*managed*) | Externa (*external*) |
| :--- | :--- | :--- |
| **Criação** | `CREATE TABLE ...` (sem `LOCATION`) | `CREATE TABLE ... LOCATION '...'` |
| **`DROP TABLE`** | Apaga a tabela **e os dados** | Apaga só os metadados; os arquivos ficam |

---

## 10. 🚀 Ajustes de Performance do Spark

> 🎯 Tópico cobrado na Data Engineer Associate.

```python
# Número de partições usadas após operações com shuffle (joins, groupBy)
spark.conf.set("spark.sql.shuffle.partitions", "200")

# Tamanho máximo de tabela que o Spark envia automaticamente por broadcast (em bytes)
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 10 * 1024 * 1024)  # 10 MB

# Ver o valor atual de uma configuração
spark.conf.get("spark.sql.shuffle.partitions")

# Ver o plano de execução de uma consulta (útil para achar shuffles e broadcasts)
df_join.explain()
```

> 💡 No Databricks, o **AQE (Adaptive Query Execution)** já ajusta partições e joins automaticamente. Mude essas configurações só depois de **medir** o desempenho, e meça de novo depois da mudança.

---

## 11. 💻 Databricks CLI e Bundles

> 🎯 Tópico cobrado na Data Engineer Associate.

A **Databricks CLI** roda no seu terminal (ou no CI/CD). Os **Declarative Automation Bundles** (antigos *Databricks Asset Bundles*) descrevem jobs, pipelines e código em arquivos YAML, para implantar o mesmo projeto em dev, test e prod.

```bash
# Autenticar a CLI em um workspace
databricks auth login --host https://<seu-workspace>.cloud.databricks.com

# Criar um projeto de bundle a partir de um modelo
databricks bundle init

# Validar a configuração do bundle
databricks bundle validate

# Implantar no ambiente (target) de desenvolvimento
databricks bundle deploy -t dev

# Executar um job definido no bundle
databricks bundle run -t dev nome_do_job
```

---

## 12. 🧭 Tabela-resumo: "Quero fazer X, uso Y"

| Quero... | Uso |
| :--- | :--- |
| Mudar a linguagem de uma célula | `%sql`, `%python`, `%scala`, `%r` |
| Reaproveitar funções de outro notebook | `%run ./notebook` |
| Orquestrar notebooks com parâmetros e retorno | `dbutils.notebook.run()` / `exit()` |
| Passar um valor entre tasks de um Job | `dbutils.jobs.taskValues` |
| Usar senha sem expor no código | `dbutils.secrets.get()` |
| Parametrizar um notebook | `dbutils.widgets` |
| Listar / copiar / apagar arquivos | `dbutils.fs` ou `%fs` |
| Ingerir só arquivos novos (grande volume) | Auto Loader (`cloudFiles`) |
| Ingerir só arquivos novos (SQL simples) | `COPY INTO` |
| Atualizar e inserir em uma operação | `MERGE INTO` |
| Juntar tabela grande com tabela pequena | `F.broadcast()` |
| Transformar array em linhas | `F.explode()` |
| Juntar arquivos pequenos | `OPTIMIZE` |
| Apagar arquivos antigos | `VACUUM` |
| Ver versões de uma tabela | `DESCRIBE HISTORY` |
| Voltar a tabela no tempo | `RESTORE TABLE ... TO VERSION AS OF` |
| Saber se a tabela é gerenciada ou externa | `DESCRIBE EXTENDED` |
| Dar acesso de leitura | `GRANT USE CATALOG` + `USE SCHEMA` + `SELECT` |
| Ver quem tem acesso | `SHOW GRANTS ON ...` |
| Implantar jobs em dev/prod | `databricks bundle deploy -t <ambiente>` |
