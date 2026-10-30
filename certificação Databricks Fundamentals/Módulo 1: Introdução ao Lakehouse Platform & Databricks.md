# 🎓 Guia de Estudos: Databricks Fundamentals — Módulo 1

> **Exame:** Databricks Lakehouse Fundamentals  
> **Tópico:** Módulo 1 — Introdução à Plataforma Lakehouse e Databricks  
> **Foco:** Conceitos-chave, definições de arquitetura e pegadinhas do exame oficial.

---

## 📑 Índice
1. [Evolução das Arquiteturas de Dados](#1-evolução-das-arquiteturas-de-dados)
2. [Pilares do Ecossistema Databricks](#2-pilares-do-ecossistema-databricks)
3. [Arquitetura de Segurança: Control Plane vs. Compute Plane](#3-arquitetura-de-segurança-control-plane-vs-compute-plane)
4. [Padrões de Questões e Pegadinhas do Exame](#4-padrões-de-questões-e-pegadinhas-do-exame)
5. [Cheat Sheet de Mnemônicos para a Prova](#5-cheat-sheet-de-mnemônicos-para-a-prova)

---

## 1. 🏛️ Evolução das Arquiteturas de Dados

O exame avalia constantemente a capacidade de comparar as limitações das arquiteturas legadas com a proposta de valor do **Data Lakehouse**.

┌─────────────────────────┐       ┌─────────────────────────┐       ┌─────────────────────────┐
│     Data Warehouse      │   +   │        Data Lake        │   =   │     Data Lakehouse      │
│ (Apenas Estruturado/BI) │       │ (Todos os Formatos/ML)  │       │ (ACID + BI + ML + Open) │
└─────────────────────────┘       └─────────────────────────┘       └─────────────────────────┘

### 🔹 Data Warehouse (DW)
* **Conceito:** Banco de dados relacional otimizado para consultas analíticas em dados estruturados (*Star Schema* / Snowflake Schema).
* **Pontos de Atenção na Prova:**
  * Não suporta dados não estruturados ou semi-estruturados (JSON complexo, imagens, áudio, logs brutos) para uso em IA e Machine Learning.
  * Armazenamento e computação acoplados (alto custo de escalabilidade).
  * Lock-in de fornecedor devido a formatos de arquivo e motores proprietários.

### 🔹 Data Lake
* **Conceito:** Repositório centralizado em nuvem (S3, ADLS Gen2, GCS) contendo dados em formatos abertos (CSV, Parquet, JSON).
* **Pontos de Atenção na Prova:**
  * **Ausência de Transações ACID:** Risco de leituras inconsistentes durante processos de escrita simultâneos.
  * **Problema do Data Swamp (Pântano de Dados):** Falta de governança e validação de esquema (*Schema Enforcement*), resultando em arquivos corrompidos ou mal formatados.
  * **Baixo Desempenho em BI:** Falta de mecanismos de indexação e compactação avançada.

### 🚀 Data Lakehouse
* **Conceito-Chave:** Arquitetura aberta que unifica o **gerenciamento e desempenho do Data Warehouse** (transações ACID, governança, consultas SQL rápidas) com a **flexibilidade e baixo custo do Data Lake** (formatos abertos, suporte a todos os tipos de dados).
* **Camada Base:** Sustentado pela camada de armazenamento em formato aberto **Delta Lake**.

---

## 2. 🧱 Pilares do Ecossistema Databricks

O exame exige associar cada tecnologia aberta ao seu papel específico na plataforma:

| Tecnologia Aberta | Função no Lakehouse | Ponto Focado no Exame |
| :--- | :--- | :--- |
| **Apache Spark** | Motor de processamento distribuído. | Processamento em lote (*Batch*) e fluxo (*Streaming*) unificados. |
| **Delta Lake** | Camada de armazenamento estruturado. | Traz **Transações ACID**, *Time Travel* e *Schema Enforcement* para o Lake. |
| **MLflow** | Gestão do ciclo de vida de Machine Learning. | Rastreamento de experimentos, métricas, parâmetros e registro de modelos. |
| **Unity Catalog** | Governança unificada de Dados e IA. | Controle de acesso centralizado (*Fine-grained ACLs*), linhagem de dados (*lineage*) multi-cloud. |

---

## 3. 🛡️ Arquitetura de Segurança: Control Plane vs. Compute Plane

Um dos tópicos com maior número de questões no exame aborda a separação de responsabilidades entre os planos de controle e processamento.

┌──────────────────────────────────────────────────────────┐
│                    CONTROL PLANE                         │
│  Gerenciado pela Databricks (Web UI, Workflows, Jobs)    │
└────────────────────────────┬─────────────────────────────┘
│ (Metadados e comandos)
▼
┌──────────────────────────────────────────────────────────┐
│              COMPUTE PLANE (Data Plane)                  │
│  Sua Conta de Nuvem (AWS / Azure / GCP)                  │
│  Clusters Spark, VMs, Armazenamento de Dados (Storage)   │
└──────────────────────────────────────────────────────────┘



* **Control Plane (Plano de Controle):** Reside na conta gerenciada da Databricks. Contém a interface web, orquestrador de tarefas (Jobs), gerenciamento de contas e metadados. **Os dados do cliente NUNCA transitam ou ficam armazenados no Control Plane.**
* **Compute / Data Plane (Plano de Processamento):** Reside **dentro da assinatura/conta de nuvem do próprio cliente (AWS/Azure/GCP)**. Onde os clusters Spark processam os dados e onde as tabelas/arquivos do Data Lake permanecem armazenados.

---

## 4. 🎯 Padrões de Questões e Pegadinhas do Exame

### ❓ Questão Padrão 1: Problemas do Data Lake
* **Pergunta:** "Qual é a principal limitação de um Data Lake tradicional que o Data Lakehouse resolve utilizando o Delta Lake?"
* ❌ **Incorreto:** "A incapacidade de armazenar grandes volumes de arquivos não estruturados."
* ✅ **Correto:** "A ausência de suporte a transações ACID e a falta de mecanismos para garantir a qualidade dos dados (Schema Enforcement)."

### ❓ Questão Padrão 2: Privacidade e Governança de Dados
* **Pergunta:** "Em qual componente da arquitetura do Databricks os dados brutos e as tabelas do cliente são processados e armazenados?"
* ❌ **Incorreto:** "No Control Plane gerenciado pela Databricks."
* ✅ **Correto:** "No Compute Plane (Data Plane), localizado na conta de nuvem do próprio cliente."

### ❓ Questão Padrão 3: Formatos Abertos vs. Vendor Lock-in
* **Pergunta:** "Como a plataforma Databricks evita o vendor lock-in ao armazenar dados?"
* ❌ **Incorreto:** "Convertendo automaticamente todos os dados para tabelas relacionais proprietárias em disco."
* ✅ **Correto:** "Utilizando o Delta Lake, um formato de armazenamento de código aberto baseado em arquivos Parquet e logs de transação JSON."

---

## 5. 📌 Cheat Sheet de Mnemônicos para a Prova

* **Data Lakehouse =** Data Lake (Baixo custo/Formatos abertos) + Data Warehouse (ACID/Performance/Governança).
* **Control Plane =** Interface, Jobs, Metadados (Gerenciado pela Databricks).
* **Compute Plane =** Onde os dados moram e são processados (Gerenciado na nuvem do cliente).
* **Delta Lake =** Parquet + Transaction Log (`_delta_log`).
* **Unificação =** Uma única plataforma para Data Engineering, SQL Analytics, Data Science e IA.
