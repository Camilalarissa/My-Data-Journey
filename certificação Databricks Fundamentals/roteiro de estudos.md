# 🧭 Bússola de Estudos: Databricks Fundamentals
Este guia serve como um roadmap prático cobrindo os pilares fundamentais da plataforma Databricks, do Lakehouse e do ecossistema Apache Spark.

## 🏗️ Módulo 1: Introdução ao Lakehouse Platform & Databricks
Compreender a arquitetura unificada que combina o melhor de Data Lakes e Data Warehouses.

[ ] Conceito de Lakehouse: O que é e por que resolve os silos entre BI, Engenharia de Dados e Machine Learning.

[ ] Arquitetura da Plataforma Databricks: Visão geral do ambiente baseado em nuvem (AWS, Azure ou GCP).

[ ] Workspace do Databricks: Navegação pela interface, gerenciamento de pastas, workspaces e controle de acesso básico (ACLs).

[ ] Gestão de Clusters: O que são clusters de computação, tipos de nós (Driver e Workers), dimensionamento automático (auto-scaling) e término automático por ociosidade.

## 📓 Módulo 2: Databricks Notebooks & Colaboração
Dominar a ferramenta de desenvolvimento interativo do dia a dia.

[ ] Linguagens Suportadas: Utilização e alternância entre Python, SQL, Scala e R no mesmo notebook usando comandos mágicos (%python, %sql, %scala, %r).

[ ] Colaboração em Tempo Real: Compartilhamento de notebooks, controle de versão e adição de comentários.

[ ] Visualizações Nativas: Criação rápida de gráficos, tabelas dinâmicas e painéis direto na saída das células do notebook.

## ⚡ Módulo 3: Apache Spark & DataFrames Básicos
Entender o motor de processamento distribuído por trás do Databricks.

[ ] O que é o Apache Spark? Conceitos de processamento distribuído, computação em memória e tolerância a falhas.

[ ] Spark DataFrames: Criação, manipulação e operações básicas em DataFrames (semelhantes ao Pandas, mas distribuídos).

[ ] Spark SQL: Como executar consultas SQL tradicionais diretamente em cima de dados tabulares e arquivos no Databricks.

[ ] Transformações vs. Ações: Diferença entre operações lazy (lentas/preguiçosas) e ações que disparam o processamento no cluster.

## 🏛️ Módulo 4: Delta Lake & Medallion Architecture
A tecnologia padrão do Databricks para armazenamento confiável.

[ ] O que é o Delta Lake? Camada de armazenamento de código aberto que traz confiabilidade ACID para o Data Lake.

[ ] Transações ACID: Garantia de atomicidade, consistência, isolamento e durabilidade nas operações de dados.

[ ] Time Travel (Viagem no Tempo): Como consultar versões anteriores de uma tabela Delta usando histórico de alterações e timestamps.

[ ] Arquitetura Medallion (Camadas Bronze, Silver e Gold):

Bronze: Dados brutos vindos das fontes originais.

Silver: Dados limpos, filtrados e enriquecidos.

Gold: Dados agregados e prontos para consumo de negócios e Machine Learning.

## 🔄 Módulo 5: Orquestração e Governança (Unity Catalog & Jobs)
Como automatizar e proteger os dados no ambiente corporativo.

[ ] Databricks Jobs: Como agendar notebooks para rodar automaticamente (pipelines de ETL recorrentes).

[ ] Unity Catalog: Conceitos básicos de governança unificada, controle de acesso centralizado a tabelas, arquivos e modelos de machine learning.

[ ] Integração com Git: Conexão de repositórios (como GitHub/GitLab) diretamente com o Databricks para versionamento de código.

## 💡 Dica de Ouro para a Prova:
A prova de Fundamentals costuma focar muito em conceitos (o que é o Delta Lake, para que serve o Lakehouse, quais são as vantagens do processamento distribuído e a estrutura do Workspace). Certifique-se de entender bem o propósito de cada ferramenta do ecossistema!
