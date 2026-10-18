# 🔄 ETL vs. ELT: Os Processos de Integração de Dados

Este documento explica os dois principais paradigmas para mover, tratar e carregar dados em uma arquitetura corporativa: o tradicional **ETL** e o moderno **ELT**.

---

## 🏭 O Conceito Base: O que é Pipeline de Dados?
Antes de diferenciar as siglas, vale lembrar que o objetivo de ambos é o mesmo: pegar dados espalhados em fontes diferentes (APIs, bancos relacionais, arquivos) e levá-los para um destino centralizado (Data Lake ou Data Warehouse) onde possam ser analisados.

A divergência está **na ordem e em ONDE a transformação (limpeza, filtros e regras de negócio) acontece**.

---

## 1. ⚙️ ETL (Extract, Transform, Load / Extração, Transformação e Carga)

No **ETL**, os dados são extraídos da origem, passam por um servidor de processamento intermediário (onde sofrem todas as transformações e limpezas) e **só depois** são carregados limpos no destino final.

* **Ordem:** 
  1. **Extract:** Puxa os dados brutos da origem.
  2. **Transform:** O servidor de ETL (ex: SSIS, Talend) limpa, filtra e formata os dados *antes* de salvá-los.
  3. **Load:** Os dados já tratados são gravados no Data Warehouse.
* **Características:**
  * **Uso Histórico:** Muito comum em ambientes *on-premise* (servidores físicos locais), onde o espaço de armazenamento era caro e limitado (não dava para guardar lixo).
  * **Gargalo:** O processamento pesado ocorre antes de salvar. Se o volume de dados explodir, o servidor de transformação pode travar por falta de memória.

---

## 2. 🚀 ELT (Extract, Load, Transform / Extração, Carga e Transformação)

Com a chegada da **nuvem** (Cloud Computing), o armazenamento ficou extremamente barato e o poder de processamento tornou-se elástico. Isso permitiu a inversão da ordem, dando origem ao **ELT**.

* **Ordem:**
  * **Extract:** Puxa os dados da origem.
  * **Load:** Joga os dados **imediatamente** e sem alterações no armazenamento em nuvem (ex: Camada Bronze no S3 ou Azure Data Lake).
  * **Transform:** Usa o poder de processamento do próprio destino (como o **Databricks/Spark** ou o **Snowflake/BigQuery**) para transformar os dados *já dentro da nuvem* (gerando as camadas Silver e Gold).
* **Características:**
  * **Padrão Moderno (Lakehouse):** É a base da arquitetura moderna de dados.
  * **Vantagem:** Nada é descartado na entrada (auditoria garantida na Bronze) e o processamento é distribuído e infinitamente escalável.

---

## 📊 Tabela Comparativa: ETL vs. ELT

| Critério | ETL (Tradicional) | ELT (Moderno / Nuvem) |
| :--- | :--- | :--- |
| **Ordem dos Fatores** | Extrai $\to$ Transforma $\to$ Carrega | Extrai $\to$ Carrega $\to$ Transforma |
| **Onde Ocorre o Processamento** | Em um servidor intermediário de ETL. | No próprio destino em nuvem (Databricks, Spark, BigQuery). |
| **Custo de Armazenamento** | Alto (evita guardar dados sujos/brutos). | Baixo (armazena tudo na Camada Bronze no S3/ADLS). |
| **Flexibilidade** | Baixa (se a regra mudar, é difícil reprocessar o passado). | Alta (os dados brutos estão salvos; basta alterar o código de transformação). |
| **Volume de Dados** | Ideal para volumes menores ou estruturados. | Essencial para **Big Data** (petabytes de dados não estruturados). |

---

## 💡 Resumo Prático para o Engenheiro de Dados

* Se você está construindo uma arquitetura moderna baseada em **Lakehouse (Databricks + AWS/Azure)**, você usará **ELT**. Você ingere os dados crus na **Camada Bronze** (Load) e usa o **Spark/PySpark** para transformá-los nas camadas **Silver e Gold** (Transform).
