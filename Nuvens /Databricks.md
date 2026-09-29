# Databricks: A Plataforma Unificada de Lakehouse

Este repositório reúne os conceitos fundamentais, a arquitetura e os objetivos do **Databricks**, a principal plataforma do mercado para unificar Engenharia de Dados, Ciência de Dados, IA e Business Intelligence em um único ecossistema.

---

## Qual é o Objetivo Principal do Databricks?

Historicamente, as empresas dividiam suas operações em dois mundos isolados:
1. **Data Lakes:** Armazenavam dados brutos a baixo custo, mas eram difíceis de gerenciar e propensos a corrupção de dados.
2. **Data Warehouses:** Eram estruturados e rápidos para relatórios, mas caros e limitados para dados não estruturados ou Machine Learning.

O **objetivo principal do Databricks** é resolver esse problema através do conceito de **Lakehouse**, unindo a flexibilidade e o baixo custo de um Data Lake com a confiabilidade, a governança e a alta performance de um Data Warehouse. 

Além disso, a plataforma busca **quebrar os silos** entre equipes: engenheiros de dados, cientistas de dados e analistas de negócios trabalham juntos no mesmo ambiente colaborativo, acessando a mesma fonte de dados oficial.

---

##  Os Pilares e Componentes da Ferramenta

### 1. Apache Spark (O Motor de Processamento)
O Databricks foi fundado pelos mesmos criadores do Apache Spark. Toda a capacidade de processamento distribuído da plataforma é impulsionada pelo motor Spark, permitindo processar petabytes de dados em paralelo usando **Python (PySpark)**, **SQL**, **Scala** ou **R**.

### 2. Delta Lake (A Camada de Confiabilidade)
O **Delta Lake** é um formato de armazenamento de código aberto integrado ao Databricks que adiciona robustez ao Data Lake:
* **Transações ACID:** Garante que gravações e leituras de dados ocorram com total segurança e consistência (sem dados parciais ou corrompidos).
* **Time Travel (Viagem no Tempo):** Permite consultar versões anteriores dos seus dados e reverter alterações caso ocorra algum erro no pipeline.

### 3. Databricks Notebooks & Workspace
Um ambiente de desenvolvimento baseado na nuvem altamente colaborativo. Vários membros da equipe podem editar o mesmo código simultaneamente, alternando entre linguagens (ex: um engenheiro escreve em PySpark e um analista consulta em SQL no mesmo notebook).

### 4. MLflow (Gestão de Machine Learning)
Plataforma integrada para gerenciar todo o ciclo de vida de IA e Machine Learning. Permite rastrear experimentos, registrar parâmetros e métricas de modelos, e fazer o *deploy* de algoritmos de forma padronizada.

---

##  A Arquitetura Medalhão (Medallion Architecture)

O Databricks popularizou a estruturação de dados em camadas progressivas de qualidade:

1. **Camada Bronze (Raw / Bruta):** 
   * Armazena os dados exatamente como vieram da fonte original (APIs, bancos relacionais, arquivos CSV/JSON), sem modificações.
2. **Camada Silver (Clean / Tratada):** 
   * Dados limpos, com tipos corrigidos, remoção de duplicatas, padronização de nomes de colunas e enriquecimento básico.
3. **Camada Gold (Aggregated / Consolidada):** 
   * Dados altamente modelados e agregados em formato de tabelas dimensionais (*Star Schema*), prontos para alimentar relatórios de BI (como o QuickSight ou Power BI) ou modelos de Machine Learning.

---

##  Por que o Mercado Utiliza o Databricks?

* **Plataforma Única:** Elimina a necessidade de gerenciar múltiplos sistemas separados para ETL, armazenamento e Machine Learning.
* **Governança Unificada (Unity Catalog):** Permite controlar com precisão quem pode ver quais tabelas, linhas ou colunas em toda a empresa.
* **Escalabilidade em Nuvem:** Integra-se perfeitamente com os principais provedores de nuvem (AWS, Azure e GCP), gerenciando a criação e destruição de clusters de servidores de forma automática (Serverless/Autoscaling).
