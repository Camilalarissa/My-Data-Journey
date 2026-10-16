# Guia Prático de Engenharia de Dados: Da Arquitetura ao Pipeline em Produção

Este documento detalha os conceitos fundamentais de arquitetura de dados, fluxos de ingestão para a camada Bronze, orquestração com nuvem, boas práticas de versionamento (Git) e estratégias de carga de dados.

---

##  1. Arquitetura de Dados (Conceito de Lakehouse & Medalhão)

A arquitetura de dados define como a empresa coleta, armazena, processa e disponibiliza suas informações. O padrão moderno de mercado é o **Lakehouse**, que une o baixo custo e a flexibilidade de um Data Lake com a confiabilidade e performance de um Data Warehouse.

Para organizar o fluxo, utilizamos a **Arquitetura Medalhão** (Camadas Progressivas):
* **Camada Bronze (Raw / Bruta):** O depósito imutável. Os dados chegam das fontes externas exatamente como são, sem nenhuma alteração (cápsula do tempo).
* **Camada Silver (Clean / Tratada):** Os dados são limpos, tipados, com remoção de duplicatas e padronização de esquemas.
* **Camada Gold (Aggregated / Consolidada):** A vitrine final. Dados altamente modelados em formato dimensional (*Star Schema*), prontos para alimentar relatórios de BI ou modelos de IA.

---

##  2. Ingestão na Camada Bronze

A **Ingestão Bronze** é a porta de entrada do pipeline. Seu objetivo é extrair os dados da origem (APIs, bancos relacionais, arquivos) e salvá-los no Storage na nuvem o mais rápido possível.

###  Regras de Ouro da Camada Bronze:
1. **Imutabilidade:** Nunca altere ou delete dados na Bronze. Ela garante a auditoria histórica.
2. **Enriquecimento de Metadados:** Adicione colunas técnicas de controle (ex: `_processing_timestamp` e `_source_file`).
3. **Evolução de Esquema:** O pipeline deve tolerar alterações inesperadas nas fontes sem quebrar.

---

##  3. O Ecossistema: Ferramentas do Azure e Databricks

* **Microsoft Azure (Nuvem Base):**
  * **Azure Data Lake Storage (ADLS Gen2):** O grande reservatório de arquivos onde as camadas Bronze, Silver e Gold ficam armazenadas fisicamente.
  * **Azure Data Factory (ADF):** O orquestrador central de transporte de dados e orquestração de pipelines.
* **Databricks (Motor de Processamento):**
  * Roda integrado à nuvem processando os dados através do **Apache Spark**.
  * Utiliza o **Auto Loader (`cloudFiles`)** para detectar e ingerir arquivos incrementais na Bronze de forma automatizada e performática.
  * **Unity Catalog:** Garante a governança centralizada e linhagem automática de dados entre as camadas.

---

##  4. Pipeline no Azure Data Factory (ADF)

O ADF atua como o "maestro" da infraestrutura na nuvem:
* **Função no Workflow:** Gerencia a execução sequencial das etapas. Ele pode se conectar a fontes legadas, disparar cópias em massa para o Storage e, em seguida, **acionar o Databricks** via API (*Databricks Notebook Activity*) para rodar o processamento pesado de transformação.

---

##  5. Notebook Databricks no Workflow

Uma vez acionado pelo ADF, o **Notebook Databricks** executa a lógica de engenharia (geralmente em PySpark ou SQL):
* **Função no Workflow:** Lê os dados brutos da camada Bronze, aplica as regras de negócio, trata anomalias e grava os dados de forma otimizada utilizando o formato **Delta Lake** (garantindo transações ACID e Time Travel).

---

## 6. O Primeiro Pull Request (Boas Práticas de DataOps)

Para garantir que o código desenvolvido não vá direto para produção quebrando tudo, utilizamos controle de versão (**Git**):
* **O Processo:** 
  1. Você desenvolve o código em uma branch isolada (`feature/...`).
  2. Faz o *commit* e o *push* para o repositório remoto (Azure Repos / GitHub).
  3. Abre um **Pull Request (PR)** solicitando que outro engenheiro revise o código.
  4. Após a aprovação e testes, o código é integrado (*merge*) à branch principal de produção (`main`).

---

##  7. Carga Full (Carga Completa)

* **O que é:** Estratégia onde **todo o conjunto de dados da origem é copiado e sobrescrito** no destino a cada execução.
* **Quando usar:** Em tabelas dimensionais muito pequenas (ex: lista de países ou categorias) ou quando a origem não possui campos de controle de data/hora para rastrear alterações.
* **Desvantagem:** Inviável para bases massivas de dados devido ao alto consumo de rede e tempo de processamento.

---

##  8. Implementação de Carga Delta (Carga Incremental)

* **O que é:** Estratégia inteligente onde o pipeline busca e processa **apenas os registros novos ou alterados** desde a última execução.
* **Como implementar:**
  * **Watermarking (Baseada em Data):** Filtrando registros onde `data_atualizacao >= ultima_data_processada`.
  * **Auto Loader (Databricks):** Identificando nativamente via eventos de nuvem quais arquivos no S3/ADLS ainda não haviam sido lidos.
  * **Change Data Capture (CDC):** Consumindo logs de transação do banco de origem.
* **Vantagem:** Extrema performance e economia de recursos computacionais em Big Data.
